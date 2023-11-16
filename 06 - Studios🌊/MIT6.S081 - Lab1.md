---
creation date: 2023-09-17 19:07
---
 [[2023-09-17-星期日]]  #🌱发芽
 
## 编译问题 
报错信息为无穷递归
> in function 'runcmd'  
> error: infinite recursion detected[-werror= infinite -recursion ]

### 解决方法 
在 xv6-labs-2021/user/sh. c 文件中, runcmd 函数上面添加设置特殊属性的宏:
```
__attribute__((noreturn))
 void
 runcmd(struct cmd *cmd)
 {
```

## 如何调试
我们在 Lab 中要编写用户程序，因此下面介绍如何调试（以 user/ls. C 文件为例）。
STEP 1：在 user/ls. C 中 main 函数的某一行打上断点
STEP 2：按 F 5 进入调试模式，点击调试控制台输入：-exec file ./user/_ls
STEP 3：点击调试工具的运行按钮，并在终端输入 ls 命令
然后 VSCode 会自动停在断点处，我们就可以对其进行调试了。






