---
creation date: 2023-03-21 16:36 
---
 [[2023-03-21-星期二]]  #🪴培育 #Git
## Note
-  [怎么写gitignore - 知乎](https://zhuanlan.zhihu.com/p/341234283) 
    - **什么是 . gitignore**
        - .gitignore 是一个文件，它用来指定 git 需要忽略的文件。它对已经被 git 跟踪的文件是不起作用的。
        - 在 `gitignore` 文件中的每一行都指定了一个 pattern，在判断是否要忽略一个路径时，git 一般会检查 `gitignore` 的 pattern。pattern 的来源可以不仅仅是 `.gitignore` 文件，详细情况可以参考【1】。
    - **pattern 的格式**
        - 一个空行不会匹配任何文件，它可以用来提高可读性
        - 以 `_` 开头的行是注释，要使用 `_` 字符的话，可以在 `_` 的前面加上反斜杠 `\`
        - 行末的空格会被忽略掉，除非使用反斜杠 `\` 将它们括起来
        - 斜杠 `/` 用作目录分隔符。它可以出现在 pattern 的前面，中间和最后。
        - 如果斜杠出现在 pattern 的前面或中间（或两者都有），那么这个模式表示的是 `.gitignore` 文件所在目录的相对目录。否则 pattern 也可能在任意 `.gitignore` 所在目录的任意下级目录进行匹配
        - 如果分隔符在 pattern 的末尾，那么这个 pattern 只会匹配目录，否则它可以匹配文件和目录
        - 星号 `*` 用于匹配除斜杠 `/` 外的任何东西，问号 `?` 匹配除 `/` 外的任意一个字符。范围记号 `[a-zA-Z]` 可以匹配在范围内的一个字符。
        - 双星号 `**` 在匹配路径名时有特殊意义
        - `**/` 会在所有目录中进行匹配。例如 pattern `**/foo` 会匹配任意位置的 `foo` ，就和 pattern `foo` 一样。
        - `**/foo/bar` 会匹配任意位置的 `bar` ，只要它在目录 `foo` 之下
        - `/**` 会匹配任何在里面的东西。例如 pattern `abc/**` 会匹配相对`.gitignore`
        - 所在目录的目录 `abc` 内的所有文件，且不论文件夹的深度
        - `/**/` 会匹配 0 个或多个文件目录。例如，
        - `a/**/b` 会匹配 `a/b`，`a/x/b`，`a/x/y/b` 等
        - 其他的双星号会被当做一般的字符进行匹配
        - 感叹号 `!` 前缀会将 pattern 反转。任意之前被排除的文件会被重新包含进来。如果文件的父目录被派出了，那么这个文件就不能被重新包含了。
    - **一个例子，emacs 配置文件的 .gitignore**
        - `/auto-save-list /elpa /elpa-* elpa/ *~ /recentf \_* *.elc /history`
        - 其中最好理解的就是 $*$~。如果没有关闭 make-backup-files 的话，在编辑完一个文件后，emacs 会保留一个副本，名字是在原文件名后面加上 `~`。使用 pattern $*$~ 可以将其忽略。
        - /elpa 是忽略掉下载的插件，elpa一般是放置插件的文件夹。上面也可以看到 `elpa/` 的写法。
        - `/auto-save-list` ，`/recentf` 和 `/history` 是 emacs 生成的文件夹，存放一些文件，它们不属于配置文件。`\_~`是忽略掉以 `#` 开头的文件，它们一般是编辑时临时存在的文件。
    - **使用 .gitignore 的一些常见问题**
        - ==文件被跟踪，但需要加入到 . gitignore 中==
            - gitignore对已被跟踪的文件是不起作用的。那么这时候应该如何处理呢？
            - 对于被跟踪的文件，可以执行以下命令来取消跟踪：
            - `git rm --cached <file>`
            - 对于一整个文件夹，可以加上 -r 参数：
            - `git rm -r --cached <folder>`
            - 随后的 add 就不会包含在 `gitignore` 中标明的 pattern 所匹配的文件了。
            - 嫌麻烦的话也可以这样：
            - `git rm -r --cached . git add . git commit -m "fixed untracked files"`
    - **参考资料**
        - 【1】Git - gitignore Documentation ([http://git-scm.com](https://link.zhihu.com/?target=http%3A//git-scm.com))： [https://git-scm.com/docs/gitign](https://link.zhihu.com/?target=https%3A//git-scm.com/docs/gitignore)
        - 【2】How to make Git "forget" about a file that was tracked but is now in . gitignore? - Stack Overflow： [https://stackoverflow.com/quest](https://link.zhihu.com/?target=https%3A//stackoverflow.com/questions/1274057/how-to-make-git-forget-about-a-file-that-was-tracked-but-is-now-in-gitignore)
        - 【3】github/gitignore: A collection of useful .gitignore templates： [https://github.com/github/gitig](https://link.zhihu.com/?target=https%3A//github.com/github/gitignore)








