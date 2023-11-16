---
creation date: 2023-11-08 17:33 
---
#🌲长青  #OS #操作系统

## 进程概念
在rCore关于进程这一部分之前，我们有 **任务** 的概念，即 **正在执行的程序** ，主角是程序。而相比于 **任务** ， **进程** (Process) 的含义是 **在操作系统管理下的<span style="background:#fff88f">程序的</span>一次执行过程**。

打一个比方，<span style="background:#fff88f">可执行文件本身</span>可以看成一张编译器解析源代码之后总结出的一张记载如何利用各种硬件资源进行一轮生产流程的 **蓝图** 。而内核的一大功能便是作为一个硬件资源管理器，它可以随时启动一轮生产流程（即执行任意一个应用），这需要**选中一张蓝图**（此时确定执行哪个可执行文件），接下来就需要内核按照蓝图上所记载的对资源的需求来对应的将各类资源分配给它，让这轮生产流程得以顺利进行。当按照蓝图上的记载生产流程完成（应用退出）之后，内核还需要将对应的硬件资源回收以便后续的重复利用。

因此，进程就是操作系统选取某个可执行文件并对其进行一次动态执行的过程。

相比可执行文件，它的动态性主要体现在：

1. 它是一个过程，从时间上来看有开始也有结束；
    
2. 在该过程中对于可执行文件中给出的需求要相应对 **硬件/虚拟资源** 进行 **动态绑定和解绑** 。
## 数据结构
![image.png](https://jgox-image-1316409677.cos.ap-guangzhou.myqcloud.com/blog/20231111163328.png)
### 进程标识符

同一时间存在的所有进程都有一个唯一的进程标识符，它们是互不相同的整数，这样才能表示表示进程的唯一性。这里我们使用 RAII 的思想，将其抽象为一个 `PidHandle` 类型，当它的生命周期结束后对应的整数会被编译器自动回收。当然我们也需要一个使用简单栈式分配策略的进程标识符分配器 `PidAllocator`。
	
```rust
pub struct PidHandle(pub usize);

struct PidAllocator {
	current: usize,
	recycled: Vec<usize>,
}
```

### 内核栈
就像之前为每个 task 提供一个内核栈一样，我们也需要为每个进程提供一个内核栈。可以想想内核地址空间布局，有一片连续的空间用于放置每个进程的内核栈。

在没有进程之前我们将每个应用的内核栈按照人为给定的应用编号从小到大的顺序将它们作为逻辑段从高地址到低地址放在内核地址空间中，且两两之间保留一个守护页面使得我们能够尽可能早的发现内核栈溢出问题。

现在引入了进程的概念后我们将应用编号替换为<span style="background:#fff88f">进程标识符决定每个进程内核栈在地址空间中的位置</span>。这里的实现关键是实现 `kstack_alloc()` 用于申请内核栈空间，在 new 或 fork 创建新进程的时候会用到，其中需要实现 ` kernel_stack_position()` 去获取该进程对应内核栈在内核地址空间中的位置，获取到位置后需要在内核地址空间中建立映射。

```rust
/// Kernel stack for a process(task)
pub struct KernelStack(pub usize);

/// allocate a new kernel stack
pub fn kstack_alloc() -> KernelStack {
    let kstack_id = KSTACK_ALLOCATOR.exclusive_access().alloc();
    let (kstack_bottom, kstack_top) = kernel_stack_position(kstack_id);
    KERNEL_SPACE.exclusive_access().insert_framed_area(
        kstack_bottom.into(),
        kstack_top.into(),
        MapPermission::R | MapPermission::W,
    );
    KernelStack(kstack_id)
}

/// Return (bottom, top) of a kernel stack in kernel space.
/// 根据进程标识符计算内核栈在内核地址空间中的位置
pub fn kernel_stack_position(app_id: usize) -> (usize, usize) {
    let top = TRAMPOLINE - app_id * (KERNEL_STACK_SIZE + PAGE_SIZE);
    let bottom = top - KERNEL_STACK_SIZE;
    (bottom, top)
}

```
### Task Control Block

在初始化之后就不再变化的元数据：直接放在任务控制块中。

在运行过程中可能发生变化的元数据：则放在 `TaskControlBlockInner` 中，将它再包裹上一层 `UPSafeCell<T>` 放在任务控制块中。保证外层只能获取任务控制块的不可变引用，若想修改里面的部分内容的话这需要 `UPSafeCell<T>` 所提供的内部可变性。

进程控制块的本体是被放到内核堆上面的, 子进程的进程控制块并不会被直接放到父进程控制块中，因为子进程完全有可能在父进程退出后仍然存在。

```rust
	pub struct TaskControlBlock {
	    // immutable
	    pub pid: PidHandle,
	    pub kernel_stack: KernelStack,
	    // mutable
	    inner: UPSafeCell<TaskControlBlockInner>,
	}
	
	pub struct TaskControlBlockInner {
	    pub trap_cx_ppn: PhysPageNum,
	    pub base_size: usize,
      pub task_status: TaskStatus,
      pub task_cx: TaskContext,
      pub memory_set: MemorySet,
  
      pub parent: Option<Weak<TaskControlBlock>>,
      pub children: Vec<Arc<TaskControlBlock>>,
  
      pub exit_code: i32,
  }
```

### TaskManager
负责管理所有的进程，我们不直接将任务控制块放到 `TaskManager` 里面，而是将它们放在内核堆上，在任务管理器中仅存放他们的引用计数智能指针，这也是任务管理器的操作单位。

  ```rust
  pub struct TaskManager {
      ready_queue: VecDeque<Arc<TaskControlBlock>>,
  }
  ```
### Processor

```rust
	pub struct Processor {
	    // 正在执行的任务
	    current: Option<Arc<TaskControlBlock>>,
	    // 空闲控制流的上下文
	    idle_task_cx: TaskContext,
	}
```

`Processor` 是描述CPU 执行状态 的数据结构。

### 任务调度的 idle 控制流

- 运行在这个 CPU 核的启动栈上
	- 在内核初始化完毕之后，会通过调用 `run_tasks` 函数来进入 idle 控制流
	- 功能是尝试从任务管理器中选出一个任务来在当前 CPU 核上执行。
	- 在这个 loop 里面不停地去问调度器（fetch_task）有没有新的任务
	- 至于下一个任务是什么，只跟调度器有关系
	- 图为run_tasks模型。
	
	![img](https://jgox-image-1316409677.cos.ap-guangzhou.myqcloud.com/blog/2022-07-05-110645.png)

```rust
pub fn run_tasks() {
    loop {
        let mut processor = PROCESSOR.exclusive_access();
        // Round robin 轮转调度
        if let Some(task) = fetch_stride_least_task() {
            // 得到 __switch 的第一个参数，也就是当前 idle 控制流的 task_cx_ptr
            let idle_task_cx_ptr = processor.get_idle_task_cx_ptr();
            // access coming task TCB exclusively
            let mut task_inner = task.inner_exclusive_access();
            // 获取任务块内部的 next_task_cx_ptr 作为 __switch 的第二个参数
            let next_task_cx_ptr = &task_inner.task_cx as *const TaskContext;
            task_inner.task_status = TaskStatus::Running;
            // release coming task_inner manually
            drop(task_inner);
            // release coming task TCB manually
            processor.current = Some(task);
            // release processor manually
            drop(processor);
            unsafe {
                __switch(idle_task_cx_ptr, next_task_cx_ptr);
            }
        } else {
            warn!("no tasks available in run_tasks");
        }
    }
}
```

当一个应用用尽了内核本轮分配给它的时间片或者它主动调用 `yield` 系统调用交出 CPU 使用权之后，内核会调用 `schedule` 函数来切换到 idle 控制流并开启新一轮的任务调度。

```rust
pub fn schedule(switched_task_cx_ptr: *mut TaskContext) {
    let mut processor = PROCESSOR.exclusive_access();
    let idle_task_cx_ptr = processor.get_idle_task_cx_ptr();
    drop(processor);
    unsafe {
        __switch(
            switched_task_cx_ptr,
            idle_task_cx_ptr,
        );
    }
}
```

## 进程管理机制的设计与实现

### 第一个进程的创建

内核手动调用 `TaskControlBlock::new` 来创建一个进程控制块，创建完成后调用 `task` 的任务管理器 `manager` 子模块提供的 `add_task` 接口，将其加入到任务管理器中的就绪队列中。

这里的关键实现是，TaskControlBlock:: new ，需要传入 ELF 可执行文件的数据切片作为参数作为 TCB 的构造函数，第一个进程就是通过这个构造的。
```rust
/// At present, it is only used for the creation of initproc
    pub fn new(elf_data: &[u8]) -> Self {
        // memory_set with elf program headers/trampoline/trap context/user stack
        let (memory_set, user_sp, entry_point) = MemorySet::from_elf(elf_data);
        let trap_cx_ppn = memory_set
            .translate(VirtAddr::from(TRAP_CONTEXT_BASE).into())
            .unwrap()
            .ppn();
        // alloc a pid and a kernel stack in kernel space
        let pid_handle = pid_alloc();
        let kernel_stack = kstack_alloc();
        let kernel_stack_top = kernel_stack.get_top();
        // push a task context which goes to trap_return to the top of kernel stack
        let task_control_block = Self {
            pid: pid_handle,
            kernel_stack,
            inner: unsafe {
                UPSafeCell::new(TaskControlBlockInner {
                    trap_cx_ppn,
                    base_size: user_sp,
                    task_cx: TaskContext::goto_trap_return(kernel_stack_top),
                    task_status: TaskStatus::Ready,
                    memory_set,
                    parent: None,
                    children: Vec::new(),
                    exit_code: 0,
                    fd_table: vec![
                        // 0 -> stdin
                        Some(Arc::new(Stdin)),
                        // 1 -> stdout
                        Some(Arc::new(Stdout)),
                        // 2 -> stderr
                        Some(Arc::new(Stdout)),
                    ],
                    signals: SignalFlags::empty(),
                    signal_mask: SignalFlags::empty(),
                    handling_sig: -1,
                    signal_actions: SignalActions::default(),
                    killed: false,
                    frozen: false,
                    trap_ctx_backup: None,
                    heap_bottom: user_sp,
                    program_brk: user_sp,
                })
            },
        };
        // prepare TrapContext in user space
        let trap_cx = task_control_block.inner_exclusive_access().get_trap_cx();
        *trap_cx = TrapContext::app_init_context(
            entry_point,
            user_sp,
            KERNEL_SPACE.exclusive_access().token(),
            kernel_stack_top,
            trap_handler as usize,
        );
        task_control_block
    }
```
### 进程调度机制
这里的关键是 `suspend_current_and_run_next` 函数的实现，可以暂停当前任务并切换到下一个任务。当应用调用 `sys_yield` 主动交出使用权、本轮时间片用尽或者由于某些原因内核中的处理无法继续的时候，就会在内核中调用此函数触发调度机制并进行任务切换。
### 进程生成机制
想要生成进程关键是两个系统调用的实现：`fork` 和 `exec`。
### 进程资源回收机制
这里的关键是进程什么时候结束，我们已经编写好的析构函数实现彻底回收掉它占用的所有资源，包括：内核栈和它的 PID 还有它的应用地址空间存放页表的那些物理页帧等等。




