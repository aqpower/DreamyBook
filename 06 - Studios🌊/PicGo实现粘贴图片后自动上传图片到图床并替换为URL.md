---
creation date: 2023-04-25 18:04 
---
 [[2023-04-25-星期二]]  #🌲长青  #图床 #Markdown #Obsidian 
在写博客的时候文章中难免会使用非常多的图片，初期的解决方案是把图片和文章放到一个目录下引用即可，之后考虑到管理混乱以及迁移不方便易丢失并且不成体系，最后采取了把图片上传到图床然后博客文章直接引用图片链接的方式。
这样做的最大的好处就是无需管理那么多图片了，但是自己手动去图床官网上传图片获取链接还是比较麻烦，于是就有了这篇文章。
<!--more-->
## 安装 Picgo
1. 使用软件包管理器安装 picgo `yay picgo` 或者下载 PicGo 的 AppImage 文件，AppImage是一个可执行的文件。
2. 右键属性或用 chmod 命令赋予 PicGo 所有用户可执行的权限。
3. 如果选择下载 AppImage 文件，需右键点击文件，选择“属性”或通过 chmod 命令赋予 PicGo 所有用户可执行的权限。
4. 根据你的需求，选择合适的图床，例如腾讯云、GitHub、Gitee 等，并按照提示填写相关信息。
5. [在文件 - 偏好设置 - 图像里面设置上传服务为Pic-Core (command line)](https://blog.csdn.net/qq_42584874/article/details/116534328)，点击下载或更新下载PicGo-core，然后点击打开配置文件或在命令行中使用picgo set uploader命令配置picgo。
6. 在文件 - 偏好设置 - 图像 - 验证图片上传选项验证是否成功，如果成功，你就可以在Typora中插入图片并自动上传到图床了。

希望这些信息对你有用。😊
### AppImage 文件
如果你选择了 AppImage 文件作为 PicGo 的安装方式，请执行以下步骤：
1. 授予该 AppImage 文件可执行权限，可以使用命令 `chmod +x PicGo-2.3.0-beta.6.AppImage` 完成设置。
2. 执行 AppImage 文件需要安装依赖包 fuse，运行命令 `sudo apt install fuse` 即可安装。
## 配置 PicGo
我使用的是腾讯云 COS，下面我将解释一些看起来可能不太直观的词汇：
- SecretId 和 SecretKey 是腾讯云安全组的密钥。
- Bucket 是存储桶的名称。
- AppId 是用户编号，你可以随意设置一个。
- 对于存储路径，如果有要求的话，填写相对路径即可。

以下是配置示例图片：
![image.png](https://jgox-image-1316409677.cos.ap-guangzhou.myqcloud.com/blog/20230425180935.png)

## 使用 PicGo 遇到的问题
### 需要安装插件xclip
如果你在使用 PicGo 时遇到以下错误信息：
```
Error: Can't find no xclip
    at Socket.<anonymous> (/tmp/. mount_PicGo-8VIaqN/resources/app. asar/node_modules/picgo/dist/index. cjs. js: 1:47 289)
    at Socket. emit (node:events: 3 94:28)
    at addChunk (node: internal/streams/readable: 3 15:12)
    at readableAddChunk (node: internal/streams/readable: 2 89:9)
    at Socket. Readable. push (node: internal/streams/readable: 2 28:10)
    at Pipe. onStreamRead (node: internal/stream_base_commons: 1 99:23)
```
这是一个错误信息，说明你的系统缺少 xclip 这个工具，导致 PicGo 无法复制图片链接到剪贴板。你可以尝试安装 xclip 包，比如 `yay xclip`，然后重新运行 PicGo。
## 配置 Obsidian
如果你希望在 Obsidian 中使用 PicGo，需要安装如下图所示的插件：
![image.png](https://jgox-image-1316409677.cos.ap-guangzhou.myqcloud.com/blog/20230724221016.png)






