---
title: 关于 Hexo 无法正常显示图片的问题
date: 2022-06-21 21:51:53
tags: Hexo
---

遇到了图片部署后无法正常显示的情况，网上找了很多解决方法最后解决了这个问题，亲测有效！
原因是 Hexo 3.0 以上默认无法处理文章插入本地图片，需要通过扩展插件支持。

<!--more-->

## 问题描述

如下图所示，图片部署后不能正常显示

<img src="关于-hexo-无法正常显示图片的问题/image-20220621214702330.png" alt="image-20220621214702330"  />

## 解决方法

1. 修改 _config.yml 中的 post_asset_folder，false 改为  true ，这样修改后，每次  ‘ hexo new page ’  生成新文章，都会在文章文件同级目录创建一个与文章文件名同名的文件夹，我们就在这里存放此文章的图片。
2. Typora 图片设置如下

![image-20220621215012531](image-20220621215012531.png)

若手动插入图片需要使用下方样式插入图片
```
![图片相对位置](你的博文名字/图片名称)
```
1. 安装插件hexo-asset-img：
`npm install hexo-asset-img --save`
`hexo clean && hexo g && hexo s`
完成安装再去调试图片就可以正常显示啦

## 参考资料

[[1] 资源文件夹 | Hexo](https://hexo.io/zh-cn/docs/asset-folders)  
[[2] hexo博客中插入图片失败——解决思路及个人最终解决办法](hexo博客中插入图片失败——解决思路及个人最终解决办法)
[[3] Hexo框架图片引用处理的两种思路和原理分析](https://donnadie.top/hexo-image-processing-analysis/)