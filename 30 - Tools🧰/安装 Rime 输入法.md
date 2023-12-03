---
creation date: 2023-04-25 16:50 
---
 [[2023-04-25-星期二]]  #🌱发芽
## 一些官网地址

輸入法官網：[RIME | 中州韻輸入法引擎](https://rime.im/)
Github 地址： [RimeWithIBus · rime/home Wiki · GitHub](https://github.com/rime/home/wiki/RimeWithIBus)
配置倉庫地址 [Rime 配置：雾凇拼音 - Dvel's Blog](https://dvel.me/posts/rime-ice/)
```shell
sudo apt-get install ibus-rime
```
## 来自 Bing 的帮助
> 您好，这是 Bing。我可以帮您配置并启用雾凇拼音。😊

[根据您提供的链接](https://rime.im/)[1](https://rime.im/)，雾凇拼音是一个 Rime 配置仓库，它提供了功能齐全，词库体验良好，长期更新修订的简体中文输入方案。

[要配置并启用雾凇拼音，您需要先下载各平台对应的 Rime 发行版](https://www.rime.jp/)[2](https://www.rime.jp/)，并将其安装到您的电脑上。

然后，您可以选择手动安装或使用 plum 安装雾凇拼音的配置文件。

手动安装的方法是将仓库所有文件复制粘贴到 Rime 的配置目录中。配置目录的位置取决于您使用的输入法框架。例如，iBus 的配置目录是$HOME/.config/ibus/rime/ ，Fcitx5的配置目录是 $HOME/.local/share/fcitx5/rime/ 。

使用 plum 安装的方法是运行下面的命令：
`bash rime-install iDvel/rime-ice:others/recipes/full`

[这个命令会安装或更新全部文件。如果您只想更新词库文件，可以使用其他的配方命令](https://rime.im/)[1](https://rime.im/)。

安装完成后，您需要重新部署 Rime 输入法，才能生效。部署方法是按组合键 Ctrl+` 或 F4 键唤出输入方案菜单，点击“部署”按钮。

部署成功后，您就可以切换到雾凇拼音输入方案，并享受它的功能和词库了。

## 配置翻页快捷键
去修改配置文件即可，配置文件的注释写得非常清楚
配置地址：
-   iBus 为 `$HOME/.config/ibus/rime/`