---
creation date: 2023-09-02 20:44 
---
 [[2023-09-02-星期六]]  #🌱发芽

[Fetching Title#3ezq](https://blog.csdn.net/m0_72132740/article/details/128847993)
[ArchLinux调优: 显卡、声卡与电源 - rqdmap | blog](https://rqdmap.top/posts/archlinux%E8%B0%83%E4%BC%98/)
```shell
$ sudo pacman -Sy 
$ sudo pacman -S pipewire pipewire-audio pipewire-pulse sof-firmware linux-firmware alsa-firmware alsa-ucm-conf alsa-utils
```
解决方案是在 grub 中添加内核选项 `snd_hda_intel.dmic_detect=0`, 禁用对麦克风的自动检测, 好消息是这样可以启用扬声器, 当时就能够成功播放音频, 因而当我在 Dell 上安装 Arch 时也沿用了改内核选项, 启用扬声器.

坏消息是这样会禁用麦克风

使用`alsamixer`, F6选择声卡, 即可自由地调整笔记本内置的麦克风和扬声器选项了! 通过腾讯会议喊一些123123即可测试确实成功.







