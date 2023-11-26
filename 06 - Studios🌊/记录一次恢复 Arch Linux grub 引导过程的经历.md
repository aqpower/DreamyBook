---
creation date: 2023-11-26 21:43
---
#🌲长青 #Linux 
![brokenmemory.JPG](https://jgox-image-1316409677.cos.ap-guangzhou.myqcloud.com/blog/brokenmemory.JPG)

## 起因
图为我坏掉的硬盘。

是的，我又掉盘了，打开电脑报错提示，3F0找不到操作系统，GG，还能怎么办，只能去找售后客服保修了。不幸中的万幸就是我的 Arch Linux 没有装在这块坏掉的硬盘上。

解决掉盘的办法也很简单，换一块硬盘即可，但是我的 Arch Linux 的引导程序也在坏掉的那块硬盘上啊，换了一块硬盘岂不是引导不了我的 Arch Linux 了。😢

## 修复

其实修复也很简单，grub 引导程序没了，重新写入不就好了。

网上看了各种经验贴，这里总结一下要点和各种经验贴中一笔带过省略的点。

首先我们需要一块 Arch Linux 启动盘，让我们的电脑从 U 盘启动，进入 Live 环境，在这里，挂载我们的 Arch Linux 所在的盘符和/boot 引导分区所在的盘符。我的 Arch Linux 使用的是<span style="background:#fff88f"> btrfs 文件系统</span>，挂载的参数不一样。

```
mount -t btrfs -o subvol=@ /dev/nvme1n1p3 /mnt #@为子卷名称，挂载根目录的话留空即可。
mount /dev/nvme0n1p1 /mnt/boot
```

挂载之后切换到原来的系统

```
arch-chroot /mnt
```

然后重写引导即可，

```
grub-install --target=x86_64-efi --efi-directory-id=ARCH
```

之后，<span style="background:#fff88f">需要重新安装微码和生成 Linux 引导</span>!!! ，当然你得先连上网（在这里夸一下 iPhone，手机用数据线连接电脑后，再打开热点，iPhone 就可以当成一个有线网络来使用，就不需要在命令行复杂的配置网络连接了）

```
pacman -S intel-ucode
pacman -S linux
```

之后再重新生成 Linux 引导程序，应当输出 find 了各种 img 镜像。

```
grub-mkconfig -o /boot/grub/grub.cfg
```

这里我是没有找到 Windows 的，因为我的 Windows 装在另一块硬盘上，没有检测到，我们通过引导程序进入 Linux 图形化界面之后再引导一遍即可。当然需要先将已经挂载的盘符解除挂载 umount 再重启。

为了再次引导并且能够找到 Windows 启动程序，我们需要再次把 boot 分区挂载到/mnt/efi，再执行，结果如下。再重启就可以看到熟悉的 grub 引导界面了，可以成功引导进入 Arch Linux 和 Windows。

```
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

![image.png](https://jgox-image-1316409677.cos.ap-guangzhou.myqcloud.com/blog/20231126220345.png)

## 收获

我之前对于挂载 mount 其实一直不太理解，现在学了操作系统的文件系统的设计和实现之后，然后现在又操作了一波，mount 就是把某个盘符，挂载到某个目录下，我们通过被挂载的目录可以直接访问被挂载的目录。

Arch Linux 出意外了可以通过一块 Arch Linux 启动盘，通过挂载系统盘再 arch-chroot，获取到对 Arch Linux 的控制权。













