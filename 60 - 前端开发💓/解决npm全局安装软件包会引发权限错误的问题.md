---
creation date: 2023-07-27 15:59 
---
 [[2023-07-27-星期四]]  #🌱发芽 #NodeJS #NPM
npm 全局安装软件包的时候，会提醒权限不足无法写入，我之前的做法是直接采取 sudo 管理员权限一把梭，但是这事实上是一个非常不好的习惯，

在使用 `sudo` 进行 npm 安装后，安装的包通常会以 root 用户的权限进行写入，而且可能会导致后续的权限问题。在使用普通用户身份运行 npm 时，它会将包安装到你的用户目录下，因此无需担心权限问题。
<!--more-->
## 解决方案
**不要去更改权限！**
Chown 或 chmod 不是解决方案，因为存在安全风险。
**正确的做法有两种：**
### 1. 为指定用户全局安装软件包
如果调用，首先检查 npm 指向何处：

```bash
npm config get prefix
```

如果返回 /usr，则可以执行以下操作：

```bash
mkdir ~/.npm-global
export NPM_CONFIG_PREFIX=~/.npm-global
export PATH=$PATH:~/.npm-global/bin
```

这将在你的主目录中创建一个 npm 目录，并将 `npm` 指向它。

要使这些更改永久生效，必须在 .bashrc/.zshrc 中添加 export-command 命令：

```bash
echo -e "export NPM_CONFIG_PREFIX=~/.npm-global\nexport PATH=\$PATH:~/.npm-global/bin" >> ~/.bashrc
```

### 2. 使用 NVM 管理器安装 Node. js
[NVM](https://github.com/creationix/nvm) (Node Version Manager)（Node 版本管理器）允许您在没有 root 权限的情况下安装 Node. js，还允许您安装多个版本的 Node，以方便使用...完美的开发工具。

1. 卸载 Node（可能需要 root 权限）。[这](https://stackoverflow.com/q/9283472/1480391) 可能对你有帮助。 
    
2. 然后按照说明安装 NVM。[GitHub - nvm-sh/nvm: Node Version Manager - POSIX-compliant bash script to manage multiple active node.js versions](https://github.com/creationix/nvm)
3. 通过 NVM 安装 Node： `nvm install node`


现在，`npm link`、`npm install -g` 将不再要求 root 权限。

## 参考文章
[如何在没有 sudo 的情况下修复 npm 引发的错误](https://stackoverflow.com/questions/16151018/how-to-fix-npm-throwing-error-without-sudo)
[npm-global-without-sudo.md](https://github.com/sindresorhus/guides/blob/main/npm-global-without-sudo.md)
[docs.npmjs.com/getting-started/fixing-npm-permissions/](https://docs.npmjs.com/getting-started/fixing-npm-permissions)



