---
title: hexo设置主页/归档页面文章显示条数
date: 2022-10-29 11:24:55
tags: Hexo
---

浏览归档页面是总是要频繁的翻面，整体而言非常的不实用不美观  
解决方法是打开博客目录下配置文档`_config.yml`设置即可

<!--more-->

## 自我探索

因为hexo是自己一步步配置起来的，对于配置文档还算比较熟，浏览以下就找到了如下配置
```javascript
# Home page setting
# path: Root path for your blogs index page. (default = '')
# per_page: Posts displayed per page. (0 = disable pagination)
# order_by: Posts order. (Order by date descending by default)
index_generator:
  path: ''
  per_page: 5
  order_by: -date
```
很显然,这是对于主页文章条数的控制，hexo自带  
然而如果想要改变归档界面需要再额外去安装插件
在博客任意目录下
```
npm install --save hexo-generator-archive
```
再按下方配置，即可实现理想的效果!=W=
```javascript
# Home page setting
# path: Root path for your blogs index page. (default = '')
# per_page: Posts displayed per page. (0 = disable pagination)
# order_by: Posts order. (Order by date descending by default)
index_generator:
  path: ''
  per_page: 5
  order_by: -date

archive_generator:
  per_page: 50
  yearly: true
  monthly: true
```

