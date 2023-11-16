---
creation date: 2023-10-29 14:57 
---
#🌱发芽 #rCore #OS 

在那个计算机刚刚诞生的年代，很多事情并不像我们想象的那么简单。当时，程序被记录在打孔的卡片上，使用汇编语言甚至机器语言来编写。而稀缺且昂贵的计算机由专业的管理员负责操作，就和我们在上一章所做的事情一样，他们手动将卡片输入计算机，等待程序运行结束或者终止程序的运行。最后，他们从计算机的输出端——也就是打印机中取出程序的输出并交给正在休息室等待的程序提交者。

实际上，这样做是一种对于珍贵的计算资源的浪费。因为当时（二十世纪 60 年代）的大型计算机和今天的个人计算机不同，它的体积极其庞大，能够占满一整个空调房间，像巨大的史前生物。当时用户（程序员）将程序输入到穿孔卡片上。用户将一批这些编程的卡片交给系统操作员，然后系统操作员将它们输入计算机。系统管理员在房间的各个地方跑来跑去、或是等待打印机的输出的这些时间段，计算机都并没有在工作。于是，人们希望计算机能够不间断的工作且专注于计算任务本身。

**批处理系统** (Batch System) 应运而生，它可用来管理无需或仅需少量用户交互即可运行的程序，在资源允许的情况下它可以自动安排程序的执行，这被称为“批处理作业”，这个名词源自二十世纪60年代的大型机时代。批处理系统的核心思想是：将多个程序打包到一起输入计算机。而当一个程序运行结束后，计算机会 _自动_ 加载下一个程序到内存并开始执行。当软件有了代替操作员的管理和操作能力后，便开始形成真正意义上的操作系统了。

应用程序总是难免会出现错误，如果一个程序的执行错误导致其它程序或者整个计算机系统都无法运行就太糟糕了。人们希望一个应用程序的错误不要影响到其它应用程序、操作系统和整个计算机系统。这就需要操作系统能够终止出错的应用程序，转而运行下一个应用程序。这种 _保护_ 计算机系统不受有意或无意出错的程序破坏的机制被称为 **特权级** (Privilege) 机制，它让应用程序运行在用户态，而操作系统运行在内核态，且实现用户态和内核态的隔离，这需要计算机软件和硬件的共同努力。
## 本章目标
设计和实现建立支持批处理系统的泥盆纪 [邓氏鱼](http://learningos.cn/rCore-Tutorial-Book-v3/chapter2/0intro.html#dunk) 操作系统。

- 操作系统自身运行在内核态，且支持应用程序在用户态运行，且能完成应用程序发出的系统调用
- 能够感知多个应用程序的存在，并一个接一个地运行这些应用程序

<p align="center">
  <img src="http://learningos.cn/rCore-Tutorial-Book-v3/_images/deng-fish.png" alt="图片说明">

	<img src="http://learningos.cn/rCore-Tutorial-Book-v3/_images/batch-os-detail.png">
</p>
1. 改进应用程序，让它能够在用户态执行，并能发出系统调用。
2. 把本来相对松散的应用程序执行代码和操作系统执行代码连接在一起，并让操作系统能够找到应用程序的位置。
3. 给 Binary 应用分配好对应执行环境所需一系列的资源。
4. 实现 **执行应用程序** 的操作系统功能，其主要实现在 `run_next_app` 内核函数中。

# 实现应用程序
设计实现被批处理系统逐个加载并运行的应用程序。
- 应用程序的内存布局
- 应用程序发出的系统调用
# 实现批处理操作系统
在批处理操作系统中，每当一个应用执行完毕，我们都需要将下一个要执行的应用的代码和数据加载到内存。

在操作系统和应用程序需要被放置到同一个可执行文件的前提下，设计一种尽量简洁的应用放置和加载方式，使得操作系统容易找到应用被放置到的位置，从而在批处理操作系统和应用程序之间建立起联系的纽带。
# 实现特权级别的切换

- 有一个专门的 **控制状态寄存器** (CSR, Control and Status Register) 来辅助 Trap 处理。

|CSR 名|该 CSR 与 Trap 相关的功能|
|---|---|
|sstatus|`SPP` 等字段给出 Trap 发生之前 CPU 处在哪个特权级（S/U）等信息|
|sepc|当 Trap 是一个异常的时候，记录 Trap 发生之前执行的最后一条指令的地址|
|scause|描述 Trap 的原因|
|stval|给出 Trap 附加信息|
|stvec|控制 Trap 处理代码的入口地址|

- 应用程序的上下文包括通用寄存器和栈两个主要部分。在执行操作系统的 Trap 处理过程之前要把上下文保存起来。
- 特权级切换的具体实现过程一部分由硬件直接完成，主要是涉及到对于 CSR 的一些切换控制。
	- CPU 会跳转到 `stvec` 所设置的 Trap 处理入口地址，并将当前特权级设置为 S ，然后从 Trap 处理入口地址处开始执行。
	- CPU 会将当前的特权级按照 `sstatus` 的 `SPP` 字段设置为 U 或者 S ；
	- CPU 会跳转到 `sepc` 寄存器指向的那条指令，然后继续执行。
- 内核栈保存原控制流的寄存器状态和 CSR 信息。
- 切换 `sp` 寄存器即可直接换栈。
- 问题在于保存和恢复 **Trap 上下文** (类似前面提到的函数调用上下文)。

```rust
// os/src/trap/context. rs  
#[repr (C)]  
pub struct TrapContext {  
 	pub x: [usize; 32],  // 很难甚至不可能找出哪些寄存器无需保存。
 	pub sstatus: Sstatus,  
 	pub sepc: usize,  
}

pub fn init() {
	extern "C" {
		fn __alltraps();
	}
	unsafe {
		stvec::write(__alltraps as usize, TrapMode::Direct);
		// stvec 控制 Trap 处理代码的入口地址
		// `__alltraps` 函数的地址作为参数传递。
	}
}
```

- 通过 `__alltraps` <span style="background:#fff88f">将 Trap 上下文保存在内核栈上</span>。
```r
.align 2
__alltraps:
    csrrw sp, sscratch, sp 
    # now sp->kernel stack, sscratch->user stack
    # 预先分配栈帧 allocate a TrapContext on kernel stack
    addi sp, sp, -34*8 
    #  save general-purpose registers
    sd x1, 1*8(sp)
    # skip sp(x2), we will save it later
    sd x3, 3*8(sp)
    # skip tp(x4), application does not use it
    # save x5~x31
    .set n, 5
    .rept 27
        SAVE_GP %n
        .set n, n+1
    .endr
    # we can use t0/t1/t2 freely, because they were saved on kernel stack
    csrr t0, sstatus
    csrr t1, sepc
    # 我们将 CSR sstatus 和 sepc 的值分别读到寄存器 t0 和 t1 中然后保存到内核栈对应的位置上。
    sd t0, 32*8(sp)
    sd t1, 33*8(sp)
    # read user stack from sscratch and save it on the kernel stack! 
    # 首先将 sscratch 的值读到寄存器 t2 并保存到内核栈上
    csrr t2, sscratch
    sd t2, 2*8(sp)
    # set input argument of trap_handler(cx: &mut TrapContext)
    mv a0, sp
    call trap_handler
```
- 跳转到使用 Rust 编写的 `trap_handler` 函数完成 Trap 分发及处理。
```rust
#[no_mangle]
/// handle an interrupt, exception, or system call from user space
pub fn trap_handler(cx: &mut TrapContext) -> &mut TrapContext {
	let scause = scause::read(); // get trap cause
	let stval = stval::read(); // get extra value
	// 根据 `scause` 寄存器所保存的 Trap 的原因进行分发处理。
	match scause.cause() {
		// 系统调用触发trap
		Trap::Exception(Exception::UserEnvCall) => {
		// 这是中断，在 Trap 返回之后，我们希望应用程序控制流从 `ecall` 的下一条指令开始执行！异常的话则不需要这样。
		cx.sepc += 4;
		// 分发到具体的syscall处理函数并获取返回值
		cx.x[10] = syscall(cx.x[17], [cx.x[10], cx.x[11], cx.x[12]]) as usize;
		}
		// 仿存错误和非法指令错误
		Trap::Exception(Exception::StoreFault) |   Trap::Exception(Exception::StorePageFault) => {
			println!("[kernel] PageFault in application, kernel killed it.");
			run_next_app();
		}
		// 目前还不支持的trap类型
		Trap::Exception(Exception::IllegalInstruction) => {
			println!("[kernel] IllegalInstruction in application, kernel killed it.");
			run_next_app();
		}
		_ => {
			panic!(
			"Unsupported trap {:?}, stval = {:#x}!",
			scause.cause(),
			stval
			);
		}
	}
	cx // 将传入的Trap 上下文 `cx` 原样返回
}
```

- 当 `trap_handler` 返回之后，使用 `__restore` 从保存在内核栈上的 Trap 上下文恢复寄存器。
```r
__restore:
    # case1: start running app by __restore
    # case2: back to U after handling trap
    mv sp, a0
    # now sp->kernel stack(after allocated), sscratch->user stack
    # restore sstatus/sepc
    # 从内核栈顶的 Trap 上下文恢复通用寄存器和 CSR
    # 注意我们要先恢复 CSR 再恢复通用寄存器
    ld t0, 32*8(sp)
    ld t1, 33*8(sp)
    ld t2, 2*8(sp)
    csrw sstatus, t0
    csrw sepc, t1
    csrw sscratch, t2
    # restore general-purpuse registers except sp/tp
    ld x1, 1*8(sp)
    ld x3, 3*8(sp)
    .set n, 5
    .rept 27
        LOAD_GP %n
        .set n, n+1
    .endr
    # release TrapContext on kernel stack
    # 在内核栈上回收 Trap 上下文所占用的内存，回归进入 Trap 之前的内核栈栈顶。
    addi sp, sp, 34*8
    csrrw sp, sscratch, sp
    # now sp->kernel stack, sscratch->user stack
    sret

```
- 最后通过一条 `sret` 指令回到应用程序执行。

当批处理操作系统初始化完成，或者是某个应用程序运行结束或出错的时候，我们要调用 <span style="background:#fff88f">run_next_app</span> 函数切换到下一个应用程序。此时 CPU 运行在 S 特权级，而它希望能够切换到 U 特权级。

在 RISC-V 架构中，唯一一种能够使得 CPU 特权级下降的方法就是执行 Trap 返回的特权指令，如 `sret` 、`mret` 等。事实上，在从操作系统内核返回到运行应用程序之前，要完成如下这些工作：

- 构造应用程序开始执行所需的 Trap 上下文并压入内核栈！
- 通过 `__restore` 函数，从刚构造的 Trap 上下文中，恢复应用程序执行的部分寄存器；
- 设置 `sepc` CSR的内容为应用程序入口点 `0x80400000`；
- 切换 `scratch` 和 `sp` 寄存器，设置 `sp` 指向应用程序用户栈；
- 执行 `sret` 从 S 特权级切换到 U 特权级。

```rust
pub fn run_next_app() -> ! {

	let mut app_manager = APP_MANAGER.exclusive_access();

	let current_app = app_manager.get_current_app();

	unsafe {

		app_manager.load_app(current_app);

	}

	app_manager.move_to_next_app();

	drop(app_manager);

	// before this we have to drop local variables related to resources manually

	// and release the resources

	extern "C" {

		fn __restore(cx_addr: usize);

	}

	unsafe {
	// 在内核栈上压入一个 Trap 上下文，其 `sepc` 是应用程序入口地址 `0x80400000` ，其 `sp` 寄存器指向用户栈
	// `push_context` 的返回值是内核栈压入 Trap 上下文之后的栈顶，它会被作为 `__restore` 的参数,使得在 `__restore` 函数中 `sp` 仍然可以指向内核栈的栈顶
		_restore(KERNEL_STACK.push_context(TrapContext::app_init_context(

		APP_BASE_ADDRESS,

		USER_STACK.get_sp(),

		)) as *const _ as usize);

	}

	panic!("Unreachable in batch::run_current_app!");

}
```




