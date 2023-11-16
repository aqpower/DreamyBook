---
creation date: 2023-09-17 19:07
---
 [[2023-09-17-星期日]]  #🌱发芽
 
编译问题：
报错信息为无穷递归
> in function 'runcmd'  
> error: infinite recursion detected[-werror= infinite -recursion ]

解决方法：
在 xv6-labs-2021/user/sh. c 文件中, runcmd 函数上面添加设置特殊属性的宏:
```
__attribute__((noreturn))
 void
 runcmd(struct cmd *cmd)
 {
```








