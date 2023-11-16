---
creation date: 2023-11-16 11:22 
---
#🪴培育  #OS #rCore 

参考文章：[扩展阅读：RISC-V 架构与内核启动 · GitBook](https://scpointer.github.io/rcore2oscomp/docs/lab1/boot.html)
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
+ 第一阶段，将必要的文件载入到 Qemu 物理内存之后，Qemu CPU 的程序计数器（PC, Program Counter）会被初始化为 `0x1000`， CPU 从物理地址 `0x1000` 开始执行一段硬件（ `Qemu` 模拟出的）上的引导代码，此时 CPU 位于 M 态。
+ 第二阶段，Qemu 的第一阶段固定跳转到 `0x80000000` ，`RustSBI ` 已经被加载到这里，开始执行 ` RustSBI ` 的初始化代码，此时 CPU 位于 M 态。
+ 第三阶段，通过 ` mret ` <span style="background:#fff88f">跳转到  0x80200000  执行内核的第一条代码</span>，同时 CPU 切换到 S 态。` 0x80200000 ` 这个地址是直接编码在 ` SBI ` 里的，不需要 ` Qemu ` 告诉它内核在哪。

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

![image.png](https://jgox-image-1316409677.cos.ap-guangzhou.myqcloud.com/blog/20231116145157.png)







