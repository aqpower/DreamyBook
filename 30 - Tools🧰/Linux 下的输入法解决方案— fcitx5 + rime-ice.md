---
creation date: 2023-10-17 19:30
---
 [[2023-10-17-星期二]]  #🌲长青  #Linux #输入法

![image.png](https://jgox-image-1316409677.cos.ap-guangzhou.myqcloud.com/blog/20231017202248.png)


为了修复输入法选中框中 emoji 符号不能正常显示的问题，我再次调整了我的输入法配置，也对 Linux 下的输入法配置有了比较清晰的理解，方便我日后再次配置，便在这里记录下来。

## 名词定义

明白三个概念，什么是 fcitx5，rime 和 rime-ice。

- fcitx5 是一个输入法**框架**，通常用于 Linux 操作系统上，用于支持多种不同语言和输入法的输入。
	- 配置文件在  `/home/melody/.config/fcitx5/conf/`
- [RIME | 中州韵输入法引擎](https://rime.im/) 是一个跨平台的输入法算法**引擎**，可以运行在输入法框架之上，需要进行各种配置后才可以使用的比较舒服。
- [rime-ice](https://github.com/iDvel/rime-ice) 则是一个 RIME 的**配置文件**仓库。
	- 配置文件在 `/home/${USER}/.local/share/fcitx5/rime/`

## 安装

[GitHub - Mark24Code/rime-auto-deploy: Make using Rime easy.](https://github.com/Mark24Code/rime-auto-deploy) 一个自用的脚本，帮助无痛快速安装、部署 Rime 输入法（中州韵、小狼毫，鼠须管）以及部署配置。

推荐使用这个脚本一键无痛安装。具体使用方法看脚本文档即可，我在安装过程中没有遇到大问题，即使你不是第一次使用 rime，脚本可以自动备份旧的配置文件并且安装新的配置好的文件。

## 配置文件

说实话，rime 的配置文件真的非常的混乱，即使是在已经配置好的文件中，还好每个文件中的注释都非常清晰易懂。

里面的文件主要有三个类型。

- XXX. custom. yaml 一些功能配置的模板文件
- XXX. Schema. Yaml 各个输入法方案的配置文件，不需要使用的模式可以直接删除。
	- [配置文件格式](https://github.com/LEOYoon-Tsaw/Rime_collections/blob/master/Rime_description.md) 按键对应也包含在里面
- XXX. Yaml 自动生成的一些文件，修改这里的文件是没有用的。

我主要进行了以下配置：
### 一、设置<>键进行翻页

在 default. Custom. Yaml 中添加如下代码，重新部署输入法即可实现<>翻页。

```
key_binder:

  bindings:

    - { when: has_menu, accept: less, send: Page_Up }
    - { when: has_menu, accept: greater, send: Page_Down }
```

### 二、设置模糊音

自动配置脚本配置的完整 rime，模糊音居然是自动开启了 nl 模糊，🤔🤔🤔

使用体验非常不好，里觉得合理吗

同样在模糊音配置文件 `rime_ice.custom.yaml` 中注释掉对应部分即可。

### 三、配置输入法主题风格，字体

rime-ice 的配置自带了很多皮肤字体设定，但是在 fcitx5 中都没有生效。

fcitx 5 需要去框架配置中修改皮肤设定，使用 yay 下载皮肤之后按照配置文件中说明的使用即可。字体我推荐霞鹜文楷~非常美丽：D

推荐使用这个皮肤[GitHub - hosxy/Fcitx5-Material-Color: 一款使用Material Design 配色的 fcitx5 皮肤，喜欢的话给个 star 吧 ヾ(≧へ≦)〃 😉](https://github.com/hosxy/Fcitx5-Material-Color)


### 四、修复输入框中 emoji 无法正常显示的问题

这个问题才是我这次配置输入法的主要目的！

1. 首先发现有同样问题的 issue [arch + fcitx5 不显示emoji？ · Issue #50 · fkxxyz/rime-cloverpinyin · GitHub](https://github.com/fkxxyz/rime-cloverpinyin/issues/50)，惊喜！有救了，大家果然给出了解决方案。
    -  [Fontconfig and Noto Color Emoji | dotfiles](https://flammie.github.io/dotfiles/fontconfig.html) [104542 – Color emojis are not displayed](https://bugs.freedesktop.org/show_bug.cgi?id=104542)
    -  [104542 – Color emojis are not displayed](https://bugs.freedesktop.org/show_bug.cgi?id=104542)
2. 按照解决方案中的描述，需要修改 `/etc/fonts/conf.d/68-color-emoji.conf` 这个配置文件，我欣喜的去找，发现我的目录下没有这个配置文件。但是很显然这个配置文件不是自己直接创建就可以使用的类型。我的解决问题之路陷入了迷茫期。
3. 这期间反复尝试过了重新安装 emoji 字体，都没有生效，直到我在无意间尝试安装 emoji 的时候发现了这个仓库，救命稻草啊！安装完成之后，果然我也有了需要修改的配置文件，直接修改，再重启输入法，问题解决了！
    ![image.png](https://jgox-image-1316409677.cos.ap-guangzhou.myqcloud.com/blog/20231017201651.png)


## 总结

这样看下来其实并不难，自己主动去配置经历一遍就好了，Linux 下配置是最方便的了！关键是要对自己配置的东西知其然，希望我在下次配置 Linux 输入法的时候不会连配置文件都找不到，不会缕不清配置文件中的关系。









