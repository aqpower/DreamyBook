---
creation date: 2023-11-16 11:22 
---
#🌲长青   #OS #rCore 

参考文章：[扩展阅读：RISC-V 架构与内核启动 · GitBook](https://scpointer.github.io/rcore2oscomp/docs/lab1/boot.html)

我们在 rCore 的第一步是能够在没有任何操作系统的裸机上运行我们的内核，并能够把控制权转交给 Rust 方便我们编写内核源代码，下面一起来看看为了达到这个目的需要做些什么吧。
## 启动 Qemu
rCore 编译内核源代码时设置的目标平台是 `riscv64gc-unknown-none-elf`，我们想要启动我们的内核的话，需要一台 ` RISC-V ` 架构的机器来运行我们的内核，我们可以使用 Qemu 来模拟一台。启动的时候需要给定一些启动参数，可以指定我们这台模拟的机器有些什么东西，比如下面是一条启动 qemu 并加载我们的内核的指令。

```shell
qemu-system-riscv64 \
    -machine virt \
    -nographic \
    -bios ../bootloader/rustsbi-qemu.bin \
    -device loader,file=target/riscv64gc-unknown-none-elf/release/os.bin,addr=0x80200000
```

这里介绍一些启动参数：
- `-nographic` 表示模拟器不需要提供图形界面，而只需要对外输出字符流。
- `-bios` 设置 Qemu 模拟器开机时用来初始化的引导加载程序（<span style="background:#fff88f">bootloader</span>），它是运行在 M 态的程序，负责初始化以及启动内核（指会跳转到内核所在地址），默认<span style="background:#fff88f">会被加载到内存中 0x80000000 这个地址</span>。默认情况下， `Qemu` 会使用 `OpenSBI` 启动，但 `rCore-Tutorial` 在这里指定了使用 [`RustSBI`](https://github.com/rustsbi/rustsbi-qemu/) 启动。
- `-device loader,file=$(KERNEL_BIN),addr=$(KERNEL_ENTRY_PA)` 指定了内核加载的位置是 `KERNEL_ENTRY_PA`，即 `0x80200000`，内核镜像会被放置在内存的这个位置。

## 内核启动过程

![image.png](https://jgox-image-1316409677.cos.ap-guangzhou.myqcloud.com/blog/20231116112736.png)

> Mret: 从 M 态返回到特权级更低的模式。
> Ecall 陷入特权级更高的模式。

这里解释一下 Qemu 模拟的机器启动以后到底做了些什么事情：

第一阶段，将必要的文件载入到 Qemu 物理内存之后，Qemu CPU 的程序计数器（PC, Program Counter）会被初始化为 `0x1000`， CPU 从物理地址 `0x1000` 开始执行一段硬件（ `Qemu` 模拟出的）上的引导代码，此时 CPU 位于 M 态。

第二阶段，Qemu 的第一阶段固定跳转到 `0x80000000` ，`RustSBI` 已经被加载到这里，开始执行 `RustSBI` 的初始化代码，此时 CPU 位于 M 态。

第三阶段，通过 `mret ` <span style="background:#fff88f">跳转到  0x80200000  执行内核的第一条代码</span>，同时 CPU 切换到 S 态。`0x80200000` 这个地址是直接编码在 ` SBI ` 里的，会固定跳转到该地址。

我们已经编写好的内核的第一条代码是位于 `os/src/entry.asm` 中的 `_start` 符号所在的位置。不过连接器默认的内存布局不能按照我们的想法把第一条指令加载到正确的地方，我们可以通过 **链接脚本** (Linker Script) 调整链接器的行为，使得最终生成的可执行文件的内存布局符合 Qemu 的预期，即内核第一条指令的地址应该位于 0x80200000 。

```asm
// os/src/entry.asm
    .section .text.entry
    .globl _start
_start:
    la sp, boot_stack_top
    call rust_main

    .section .bss.stack
    .globl boot_stack_lower_bound
boot_stack_lower_bound:
    .space 4096 * 16
    .globl boot_stack_top
boot_stack_top:
```

第 2 行表明我们希望将第 2 行后面的内容全部放到一个名为 `.text.entry` 的段中，将其命名为 `.text.entry` 从而区别于其他 `.text` 的目的在于我们想要确保该段被放置在相比任何其他代码段更低的地址上。这样，作为内核的入口点，这段指令才能被最先执行。

下面是链接脚本，第 12 行我们将包含内核第一条指令的 `.text.entry` 段放在最终的 `.text` 段的最开头，同时注意到在最终内存布局中代码段 `.text` 又是先于任何其他段的。因为所有的段都从 `BASE_ADDRESS` 也即 `0x80200000` 开始放置，这就能够保证内核的第一条指令正好放在 `0x80200000` 从而能够正确对接到 Qemu 上。

```
OUTPUT_ARCH(riscv)
ENTRY(_start)
BASE_ADDRESS = 0x80200000;

SECTIONS
{
    . = BASE_ADDRESS;
    skernel = .;

    stext = .;
    .text : {
        *(.text.entry)
        . = ALIGN(4K);
        strampoline = .;
        *(.text.trampoline);
        . = ALIGN(4K);
        *(.text .text.*)
    }

    . = ALIGN(4K);
    etext = .;
    srodata = .;
    .rodata : {
        *(.rodata .rodata.*)
        *(.srodata .srodata.*)
    }

    . = ALIGN(4K);
    erodata = .;
    sdata = .;
    .data : {
        *(.data .data.*)
        *(.sdata .sdata.*)
    }

    . = ALIGN(4K);
    edata = .;
    sbss_with_stack = .;
    .bss : {
        *(.bss.stack)
        sbss = .;
        *(.bss .bss.*)
        *(.sbss .sbss.*)
    }

    . = ALIGN(4K);
    ebss = .;
    ekernel = .;

    /DISCARD/ : {
        *(.eh_frame)
    }
}
```

## 函数调用 将控制权交给Rust
从 CPU 可以执行我们编写的内核的第一条指令开始，我们就等于是说已经把框架搭好了，内核已经执行我们的指令了，然而，我们无论如何也不想仅靠手写汇编代码的方式编写我们的内核，绝大部分功能我们都想使用 Rust 语言来实现。

我们需要设置栈空间，来在内核内<span style="background:#fff88f">使能函数调用</span>，随后直接调用使用 Rust 编写的内核入口函数，从而控制权便被移交给 Rust 代码。

![image.png](https://jgox-image-1316409677.cos.ap-guangzhou.myqcloud.com/blog/20231116151039.png)

关于函数调用与栈的具体细节可以参考这个链接：[为内核支持函数调用与栈](https://rcore-os.cn/rCore-Tutorial-Book-v3/chapter1/5support-func-call.html#term-function-call-and-stack)

![image.png](https://jgox-image-1316409677.cos.ap-guangzhou.myqcloud.com/blog/20231116154726.png)

> 总之，在进行函数调用的时候，我们通过 `jalr` 指令保存返回地址并实现跳转；而在函数即将返回的时候，则通过 `ret` 伪指令回到跳转之前的下一条指令继续执行。这样，RISC-V 的这两条指令就实现了函数调用流程的核心机制。
> 
> 编译器是独立编译每个函数的，因此一个函数并不能知道它所调用的子函数修改了哪些寄存器。而站在一个函数的视角，在调用子函数的过程中某些寄存器的值被覆盖的确会对它接下来的执行产生影响。我们需要为函数保护好现场，我们把在控制流转移前后需要保持不变的寄存器集合称之为 **函数调用上下文** (Function Call Context) 。
> 
> 由于每个 CPU 只有一套寄存器，我们若想在子函数调用前后保持函数调用上下文不变，就需要物理内存的帮助。在调用子函数之前，我们需要在栈 **保存** (Save) 函数调用上下文中的寄存器；而在函数执行完毕后，我们会从内存中同样的区域读取并 **恢复** (Restore) 函数调用上下文中的寄存器。这一工作是由子函数的调用者和被调用者（也就是子函数自身）合作完成。函数调用上下文中的寄存器被分为如下两类：
> 
> **被调用者保存 (Callee-Saved) 寄存器** ：由被调用的函数来保证在调用前后，这些寄存器保持不变；<span style="background:#d3f8b6">被调用的函数想要用的话，要先保存这些寄存器！</span>
> 
> **调用者保存 (Caller-Saved) 寄存器** ：由发起调用的函数来保证在调用前后，这些寄存器保持不变。<span style="background:#d3f8b6">也就是说被调用的函数可以随意更改这些寄存器！这些寄存器通常都是对恢复环境没啥意义的临时寄存器，可能也就是做做计算，如果调用者觉得值有意义的话需要自己保存。</span>


> 调用者和被调用者实际上<span style="background:#fff88f">只需分别按需保存调用者保存寄存器和被调用者保存寄存器的一个子集</span>。编译器在进行后端代码生成时，知道在这两个场景中分别有哪些值得保存的寄存器。
### 调用规范

我们已经知道了函数调用控制流需要保存函数上下文，然而，对于函数调用实现时的一些规范或者说约定，不同的指令集不同的语言都有不同的实现。**调用规范** (Calling Convention) 约定在某个指令集架构上，某种编程语言的函数调用如何实现。它包括了以下内容：

1. 函数的输入参数和返回值如何传递；
2. 函数调用上下文中调用者/被调用者保存寄存器的划分；
3. 其他的在函数调用流程中对于寄存器的使用方法。

比如，ra ( `x1` ) return address 是被调用者保存的。被调用者函数可能也会调用函数，在调用之前就需要修改 `ra` 使得这次调用能正确返回。因此，每个函数都需要在开头保存 `ra` 到自己的栈帧中，并在结尾使用 `ret` 返回之前将其恢复。栈帧是<span style="background:#fff88f">当前执行函数</span>用于存储局部变量和函数返回信息的内存结构。

下图为一个可能的函数的栈帧内容。

![image.png](https://jgox-image-1316409677.cos.ap-guangzhou.myqcloud.com/blog/20231116162612.png)

### 分配并启用栈

我们在 `entry.asm` 中分配启动栈空间，并<span style="background:#fff88f">在控制权被转交给 Rust 入口之前将栈指针 sp 设置为栈顶的位置</span>。这样 Rust 代码在进行函数调用和返回的时候就可以正常在启动栈上分配和回收栈帧了。

```asm
    .section .text.entry
    .globl _start
_start:
    la sp, boot_stack_top
    call rust_main

    .section .bss.stack
    .globl boot_stack_lower_bound
boot_stack_lower_bound:
    .space 4096 * 16
    .globl boot_stack_top
boot_stack_top:
```

## 总结
 要在一个 RISC-V 机器上运行我们的内核，首先内核要正确加载到物理内存中的约定好的位置，PC 要能够有机会指向我们编写的内核可执行文件，可以被 CPU 执行。当然我们也要正确控制内核的内存布局，确保我们编写的计划作为内核的第一条指令能够和 qemu 开始执行内核指令时的地址对齐。之后再使能函数调用，将控制权交给 rust，我们可以使用高级语言方便的编写内核代码了。

