---
creation date: 2023-04-16 23:39 
---
 [[2023-04-16-星期日]]  #🌱发芽 #Ubuntu #Pytorch

教程以及写得很好了，就记录一些自己踩的坑吧
## 驱动非 OPEN
安装系统recommend 的 open 版本驱动比如我的是 525-open 后，如果检测不到的话，可以换成对应版本的非 open 其驱动。
~~它是 Linux 的当然推荐开源了~~ 

## 环境变量配置在自己的终端文件中
比如我使用的是 zsh 在终端
配置文件就应该放在  `vim ~/. zshrc` 而不是按照教程的 `vim ~/.bashrc`
[bash - shopt command not found in .bashrc after shell updation - Stack Overflow](https://stackoverflow.com/questions/26616003/shopt-command-not-found-in-bashrc-after-shell-updation)


## 参考链接
[Ubuntu搭建Pytorch，就这一篇就够了\_ubuntu pytorch\_Chengyunlai的博客-CSDN博客](https://blog.csdn.net/qq_33806001/article/details/124850247)

[新装UBUNTU 22.04安装Nvidia GPU版Pytorch完整教程 | GTrush的个人博客](https://gtrush.com/2022/06/22/%E6%96%B0%E8%A3%85Ubuntu-22-04%E5%AE%89%E8%A3%85Nvidia-GPU%E7%89%88Pytorch%E5%AE%8C%E6%95%B4%E6%95%99%E7%A8%8B/)





