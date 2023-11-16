---
creation date: 2023-04-25 18:04 
---
 [[2023-04-25-星期二]]  #🌱发芽 #图床 #Markdown
## 安装 Picgo
1.  [下载PicGo的AppImage文件](https://blog.csdn.net/qq_42584874/article/details/116534328)[1](https://blog.csdn.net/qq_42584874/article/details/116534328)[2](https://picgo.github.io/PicGo-Doc/zh/)[3](https://blog.csdn.net/qq_42731705/article/details/120452401)，这是一个可执行的文件，不需要安装。
2.  [右键或用chmod命令赋予PicGo所有用户可执行的权限](https://blog.csdn.net/qq_42584874/article/details/116534328)[1](https://blog.csdn.net/qq_42584874/article/details/116534328)[3](https://blog.csdn.net/qq_42731705/article/details/120452401)。
3.  [运行PicGo-2.3.0-bata.6.APPImage](https://blog.csdn.net/qq_42584874/article/details/116534328)[1](https://blog.csdn.net/qq_42584874/article/details/116534328)[，你会看到一个悬浮的小图标，右键点击小图标打开详细窗口，设置PicGo](https://blog.csdn.net/qq_42584874/article/details/116534328)[1](https://blog.csdn.net/qq_42584874/article/details/116534328)[2](https://picgo.github.io/PicGo-Doc/zh/)。
4.  [选择你想要的图床，比如腾讯云、GitHub、Gitee等](https://blog.csdn.net/qq_42584874/article/details/116534328)[1](https://blog.csdn.net/qq_42584874/article/details/116534328)[2](https://picgo.github.io/PicGo-Doc/zh/)[4](https://zhuanlan.zhihu.com/p/384477655)[，参照配置手册](https://blog.csdn.net/qq_42584874/article/details/116534328)[1](https://blog.csdn.net/qq_42584874/article/details/116534328)[2](https://picgo.github.io/PicGo-Doc/zh/)填写相关信息。
5.  [在文件 - 偏好设置 - 图像里面设置上传服务为Pic-Core (command line)](https://blog.csdn.net/qq_42584874/article/details/116534328)[1](https://blog.csdn.net/qq_42584874/article/details/116534328)[，点击下载或更新下载PicGo-core](https://blog.csdn.net/qq_42584874/article/details/116534328)[1](https://blog.csdn.net/qq_42584874/article/details/116534328)[，然后点击打开配置文件或在命令行中使用picgo set uploader命令配置picgo](https://blog.csdn.net/qq_42584874/article/details/116534328)[1](https://blog.csdn.net/qq_42584874/article/details/116534328)。
6.  [在文件 - 偏好设置 - 图像 - 验证图片上传选项验证是否成功](https://blog.csdn.net/qq_42584874/article/details/116534328)[1](https://blog.csdn.net/qq_42584874/article/details/116534328)，如果成功，你就可以在Typora中插入图片并自动上传到图床了。

希望这些信息对你有用。😊
### AppImage 文件
1. 授予可执行权限
`chmod +x PicGo-2.3.0-bata.6.APPImage`
2. 需要安装依赖包 fuse
`sudo apt install fuse 5`
## 配置 PicGo
我是用的是腾讯云 COS，去存储桶列表获取所需要的信息就可
![image.png](https://jgox-image-1316409677.cos.ap-guangzhou.myqcloud.com/blog/20230425180935.png)

## 使用 PicGo 遇到的问题
### 需要安装插件xclip
使用的时候报错：
Error: Can't find no xclip
    at Socket.<anonymous> (/tmp/. mount_PicGo-8VIaqN/resources/app. asar/node_modules/picgo/dist/index. cjs. js: 1:47 289)
    at Socket. emit (node:events: 3 94:28)
    at addChunk (node: internal/streams/readable: 3 15:12)
    at readableAddChunk (node: internal/streams/readable: 2 89:9)
    at Socket. Readable. push (node: internal/streams/readable: 2 28:10)
    at Pipe. onStreamRead (node: internal/stream_base_commons: 1 99:23)
这是一个错误信息，说明你的系统缺少 xclip 这个工具，导致 PicGo 无法复制图片链接到剪贴板。你可以尝试安装 xclip 包，比如 `sudo apt install xclip`，然后重新运行 PicGo。






