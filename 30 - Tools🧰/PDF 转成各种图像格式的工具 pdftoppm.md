---
creation date: 2023-05-10 16:41 
---
 [[2023-05-10-星期三]]  #🌲长青 #工具 #PDF


参考文章： [怎么用Linux命令将PDF转成图片 - 知乎](https://zhuanlan.zhihu.com/p/397600837)
## Install  
```shell
sudo apt install poppler-utils     [On Debian/Ubuntu & Mint]
sudo dnf install poppler-utils     [On RHEL/CentOS & Fedora]
sudo zypper install poppler-tools  [On OpenSUSE]  
sudo pacman -S poppler             [On Arch Linux]
```
## Use
```shell
pdftoppm -<image_format> <pdf_filename> <image_name>
```
## 常用指令
`-rx` `-ry` 设置图片 DPI 质量，`--help` 查看更多帮助提示








