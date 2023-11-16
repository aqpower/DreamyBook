---
title: VSCode + Clangd + SSH for C/C++
date: 2022-11-12 18:50:20
tags: C++
---

Clang的代码效果和错误提示使用起来效果果然更好。

<!--more-->
  
## First Clangd

感谢这位大佬，真的写得非常好而且很有帮助

[[万字长文]Visual Studio Code 配置 C/C++ 开发环境的最佳实践(VSCode + Clangd + XMake)](https://zhuanlan.zhihu.com/p/398790625)
## Second SSH
这一步操作很简单，VScode打开远程资源管理器，配置如下
```javascript

Host Hello    //这个可以随意命名

    HostName 114.116.110.160  //服务器公网ip

    User root  // 用户名称！不能随意命名！

    Port 22 //端口，默认是22，可以不填

    PreferredAuthentications publickey,password,keyboard-interactive  

```
**值得注意的是User是不可以随便填写的。。。。。**
然后就输入密码就链接上啦，不用再面对ssh的黑框框了，可以使用vscode作为编辑器，界面可视化。
## Third 服务器端C/C++配置