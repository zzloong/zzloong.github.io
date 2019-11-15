---
title: Git 基础 —— 安装 配置 别名 对象
date: 2019-11-14 18:21:24
tags: [ss,vps]
categories: ToolsDev
keywords:
---

## Git 基础学习系列

- [Git 基础 —— 安装 配置 别名 对象](https://www.cnblogs.com/michael-xiang/p/10465857.html)
- [Git 基础 —— 常用命令](https://www.cnblogs.com/michael-xiang/p/10465865.html)
- [Git 基础 —— 常见使用场景](https://www.cnblogs.com/michael-xiang/p/10465872.html)
- [Git基础 —— Github 的使用](https://www.cnblogs.com/michael-xiang/p/10465882.html)

## Git 安装

[Git下载地址](https://git-scm.com/downloads)

Windows 安装时需要注意在`Configuring the line ending conversions`界面，选择`Checkout as-is,commit as -s`，避免Windows的换行符问题。如果忘记设置，可以使用如下命令后期设置：

```
git config --global core.autocrlf false
```

参考：
- [GitHub 第一坑：换行符自动转换 ](https://github.com/cssmagic/blog/issues/22)

## Git 配置

可以通过  查看配置：
- `git config -l/--list`
- `git config --list [--local | --global | --system]`

说明：
- `system` 不常用
- `local` 需要在一个 Git 仓库下使用，它的配置，优先级是最高的，代表仅针对单仓库的配置

### 设置 Git 账号
配置 user 信息
```
git config --global user.name "michael728"
git config --global user.email "649168982@qq.com"
```
- `--local`：区域为本仓库
- `--global`: 当前用户的所有仓库
- `--system`: 本系统的所有用户

运行 `git config --help` 可以看到帮助文档，比如，配置错误了，可以删除：`git config --unset usr.name` 将 `usr.name` 删除，应该是 `user.name`。

### 生成 SSH Key
1.先查看有没有 SSH Key，`ls -al ~/.ssh`，如果没有，采用如下方法生成。
2.生成 ssh 公钥，不要重命名生成的文件，否则会导致 ssh 方式下载 Git 代码库失败：
```
ssh-keygen -t rsa -C "649168982@qq.com"
```

参数说明：
- `-t` 用来指定加密算法为 `rsa`；
- `-C` 后面是个注释信息，并不一定要和你 Git 账户的邮箱或者 Git 账户名保持一致，只是常常是和你账户邮箱保持一致，这样设置，就能知道这个公钥被绑定在哪个 Git 账户上了。

3.生成公钥之后，拷贝公钥的内容，粘贴到你 [Github  账户的 SSH Key 设置](https://github.com/settings/keys)中；
```
pbcopy < ~/.ssh/id_rsa.pub
```
如果没有 `pbcopy` 命令，就直接打开 `id_rsa.pub` 内容，复制即可；

关于公钥私钥的一些探究：
- 一个 ssh key 只能绑定在一个 Git 账户上，而一个 Git 账户可以绑定多个 ssh key；
- 公钥就相当于一个 `fingerprint` ，公钥和唯一一个 Git 账户绑定，那么凡是机器上有这个公钥对应的私钥，机器就有绑定账户的权限去操作相关的代码库，即在 A 机器上生成的私钥发送到 B 机器上，那么，B 机器也会有权限下载了；

参考：
- [Github Add SSH Key 文档](https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/)
- [简书-图解SSH原理](https://www.jianshu.com/p/33461b619d53)
- [简书-SSH key的介绍与在Git中的使用](https://www.jianshu.com/p/1246cfdbe460)
- [archlinux-SSH keys (简体中文)](https://wiki.archlinux.org/index.php/SSH_keys_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))

### Git 配置别名
`git config`命令可以轻松为每一个命令设置别名。例如：
```
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
```

演示将git visual定义为 gitk 的别名：
```
git config --global alias.visual '!gitk'
```

设置命令的别名，可以提高操作效率。查看`.gitconfig`文件`vim ~/.gitconfig`：
```
[user]
	name = xxx
	email = xxx
[i18n]
	commitencoding = utf-8
	logoutputencoding = utf-8
[core]
	quotepath = false
[filter "lfs"]
	clean = git-lfs clean -- %f
	smudge = git-lfs smudge -- %f
	process = git-lfs filter-process
	required = true
[alias]
	co = checkout
	br = branch
	c = commit
	s = status
	unstage = reset HEAD --
	last = log -1 HEAD
	lg = log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --date=relative

[color]
	ui = true
```

我们可以体验一个 `log` 的别名命令设置：
```
lg = log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --date=relative
```

这是超厉害的别名缩写命令，试试现在的 `git lg` 有多酷炫吧！

## 探秘 .git 目录
`.git` 裸仓库文件夹，反映了 Git 良好的文件存储机制。

- `HEAD` 文件，指向当前所在的分支，表示一个「引用」。例如当前在 develop 分支，HEAD 内容就是 `ref: refs/heads/develop`
- `config` 文件，是本地仓库的配置文件，`git config --local --list`
- `refs` 文件夹，包含了 `heads` 和 `tags` 文件夹。`heads` 归档的分支，`tags` 归档的标签，也叫做「里程碑」。
  - `heads` 文件夹下有多个文件，每个文件和仓库本地存在的分支名一致，文件内容是 commit id。文件当中存放的其实是这个分支的指针指向的 commit id。`git branch -av` 可以看到分支信息和 commit id。它是个 `commit` 类型的对象。
  - `tags` 中也有多个文件，文件名和存在 `tag` 名一致，内容也是 commit id。它也是个 `commit` 类型的对象。
- objects 文件夹（核心），存放所有的 git 对象，对象哈希值前 2 位作为文件夹名称，后 38 位作为对象文件名, 可通过 `git cat-file -p <2位+38位>` 命令查看文件内容。`git cat-file -t <2位+38位>` 查看对象类型，这是一个 `tree` 类型的对象。内容本身是一个 `blob` 对象。任何文件的内容相同，在 Git 眼里，它就是唯一的一个 `blob` 对象。

还有一些暂可以不了解的文件：
- `COMMIT_EDITMSG` 文件
- `ORIG_HEAD` 文件，好像是上一次的 commit id
- `description` 文件，仓库的描述信息文件
- `index` 文件
- `hooks` 文件夹
- `info` 文件夹
- `logs` 文件夹

### 知道了一个 sha1 值，如何查看它的对象类型、对象内容、对象大小？
- `git cat-file -t` 命令 ， 查看 git 对象的类型
- `git cat-file -p` 命令， 查看 git 对象的内容
- `git cat-file -s` 命令， 查看 git 对象的大小

### Git 对象彼此关系
`commit ` `tree` `blob` 三个对象之间的关系：
![](https://ws1.sinaimg.cn/mw690/6d9475f6ly1fzb6eybuywj212t0my435.jpg)
一次 commit id 对应一棵树（`tree`），一次快照，整个项目的快照，包含了哪些文件夹（`tree`）、哪些文件（`blob`）。`blob` 可以看做是一个文件，但是和文件名是没关系的，不管文件名是什么，只要文件内容相同，就是一个 `blob`，这种设计大大节约了存储空间。

### 实验
新建一个 git 仓库，创建一个 doc 文件夹，在 doc 文件夹中创建 readme 文件

- 在单纯创建 doc 文件夹时， git 不会理会；
- 创建 readme 文件之后，会提示有文件可以添加跟踪；
- 这时候执行 `find .git/objects --type f`， 没有发现有对象生成；
- 接着执行 `git add doc` 将文件添加到暂存区，这时候有文件对象生成了:
```
$ find .git/objects -type f
.git/objects/27/e826dc62c8a3616e7c15d45a3d77cd8e7966c0
$ git cat-file -t 27e8
blob
```

- 接着创建 commit，`git commit -m "add readme"`，这时候文件对象内容：
```
.git/objects/27/e826dc62c8a3616e7c15d45a3d77cd8e7966c0
.git/objects/87/0af27bedac0cf42d9a7637709e455fa7de7a7e
.git/objects/a0/3d66adf115035c521bc08509f54d867590a458
.git/objects/e6/b633ca37bf3532f87e833c8f765e07ea50cfe8
```

1 个 commit  对象，2 个 tree 对象，1 个 blob 对象。

为何会有2个 tree 对象呢，其中，整个工作目录是一个 tree 对象，工作目录下的 doc 文件夹对应了一个 tree 对象。

扩充：我又将之前 doc 文件夹的 readme 文件复制在仓库的根目录下，此时在 `git add readme` 之后，文件对象仍然无变化；但是，当 commit 之后，文件对象变多了，多了 1 个 commit，1个 tree 对象。这个多出来的 tree 对象，就是因为工作目录变化了，那么就多出来了一个 tree 对象。

## HEAD 与 branch
### 分离头指针
```
$ git checkout 20824e0
Note: checking out '20824e0'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.
```

`detached HEAD`就表示你当前处于「分离头指针」的状态。,其实，「分离头指针」就是表示正工作在一个没有分支的情况下，没有与分支进行挂钩, HEAD 没有指向任何分支。 处于「分离头指针」状态进行提交后，当你切换到其他分支，例如 `git checkout master` ，之前产生的 commit 可能会被 Git 当为垃圾清除掉。

### 分离头指针的应用场景
在处于分支头指针状态时，你产生了一次 commit `0635f24`，当你切换到其他分支后，有可能就找不到刚刚的提交了，你用 `gitk --all` 之后，看不到刚刚的提交的。

如果想保存刚刚的提交，需要使用如下命令将其与分支挂钩：
```
git branch <new-branch-name> 0635f24
```

如果临时想基于某个 commit 做变更，试试新方案是否可行，就可以采用分离头指针的方式。测试后发现新方案不成熟，直接 reset 回其他分支即可，省却了建、删分支的麻烦。

### 进一步理解 HEAD 和 branch
HEAD 既可以指向当前分支的最新 commit，也可以指向历史中的某一次 commit (「分离头指针」的情况)。归根结底，HEAD 指向的就是一次 commit。

当我们做分支切换时，HEAD 会跟着切换。

例如比较最近两次提交的差异，可以用命令：
```
# 下面三个命令等效
git diff xxx1 xxx2
git diff HEAD HEAD^
Git diff HEAD HEAD~1
```

- `HEAD^1` 表示 HEAD 的父亲，`HEAD^^` 表示 HEAD 父亲的父亲。
- `HEAD^^` 和 `HEAD~2`等效。

## Git 小技巧
### Git 帮助文档

授人以鱼不如授人以渔，先知道怎么通过帮助文档查看常用命令的说明吧：

```
git help
git help <cmd> # 与 git <cmd> --help 等价
git help <cmd> --web # 使用浏览器页面打开
git <cmd> -h # 显示简略的命令帮助
```

### gitk 图形界面
gitk 后面可以跟上文件的路径， 这样能看单个文件的修改历史的具体内容。

- `Patch` 表示的是「变更集」，某一次 commit 修改的文件集合；
- `Author` 作者
- `Commiter` 提交人
- `Cherry` 挑选一个 commit，放到另一个分支上，这时候，Author 和 Commiter 就有可能不是一个人了，因为你挑选的那个 commit  作者可能是 A，你 cherry pick 过来之后提交，提交人是你，但是作者信息还是 A，版权考虑。
- `Parent` 上一次的 commit id
- `Child` 下一次的 commit id 
- 菜单 View ，选择 `All refs` 可以看到所有的分支演变图

### gitignore 指定不需要 Git 管理的文件 
`.gitignore` 指定哪些文件不需要纳入版本管理的。
- `*.d` 任何 `.d` 结尾的文件都不需要；
- `*.dSYN/` 任何以 `.dSYM` 结尾的文件夹下的文件都不要；

加不加 `/` 作用是不一样的，很微妙。比如，有一个文件夹叫 `doc`，那么，`.gitignore` 中写 `doc`， Git 既会忽略 doc 文件夹，也会忽略 `doc` 文件！只有 `doc/` 这么写，才会只忽略 `doc` 文件夹而不忽略 `doc` 文件，作用才很明确。

所以，如果要忽略的是文件夹，那么就显示的加上 `/` 吧。

Github 现在创建仓库时，可以选择生成什么 `.gitignore` ，附上链接作为参考：
- [github/gitignore/maven](https://github.com/github/gitignore/blob/master/Maven.gitignore)
- [github/gitignore/java](https://github.com/github/gitignore/blob/master/Java.gitignore)

这里有一个[帖子](https://github.com/intellij-rust/intellij-rust/issues/1570)讨论了为何 `.idea` 文件夹没有加入到 `.gitignore` 中，这是因为每个开发者的编辑器不一样，大部分习惯是将编辑器产生的文件夹添加到全局的忽略配置中：
```
# file: ~/.gitignore_global
.DS_Store
.idea
```

此外，还要配置指定全局的忽略文件配置在哪儿：
```
git config --global core.excludesfile/Users/michael/.gitignore_global
```

或者编辑全局配置文件 `~/.gitconfig`:
```
[core]
    excludesfile=/Users/michael/.gitignore_global
```

参考：
- [Git中全局忽略.DS_Store文件](https://www.jianshu.com/p/8c0d262e49a6)

## 解决冲突的工具
[diffmerge](https://sourcegear.com/diffmerge/downloads.php)是一个可以用来文件对比软件。相较于 `git mergetool` 使用的自带的界面，diffmerge 提供了一个可交互的编辑窗口，大大提高了效率。我们可以通过简单的配置，在使用 `git difftool` `git mergetool` 时，默认使用 diffmerge 工具来进行展示差异。当然，你要是有钱，也可以选择 Beyond Compare 来作为对比软件。

选择 OS X 10.6+ Installer (Intel)	版本，安装好之后进行配置：
```
$ git config --global diff.tool diffmerge
$ git config --global difftool.diffmerge.cmd "/usr/local/bin/diffmerge \"\$LOCAL\" \"\$REMOTE\""

$ git config --global merge.tool diffmerge
$ git config --global mergetool.diffmerge.trustExitCode true
$ git config --global mergetool.diffmerge.cmd "/usr/local/bin/diffmerge --merge --result=\"\$MERGED\"
        \"\$LOCAL\" \"\$BASE\" \"\$REMOTE\""
```

为了避免每次运行git difftool都有提示信息，可以输入如下命令
```
$ git config --global difftool.prompt false
```

为了能够支持显示中文，需要对编码进行设置：
![](https://ws1.sinaimg.cn/mw690/6d9475f6ly1fzd371zsdzj21280p67cl.jpg)

diffmerge 的使用：
- `git difftool [fileName]`
- `git mergetool [fileName]`

使用 `git mergetool` 合并分支时，默认会生成以 `*.orig` 为扩展名的备份文件，每次都要手动删除。可以修改 Git 配置，禁止产生备份文件： 
```
git config --global mergetool.keepBackup false
```

参考：
- [油管-git mergetool explained](https://www.youtube.com/watch?v=wxh-AOxPX_A) 对自带的 `git mergetool` 命令的使用介绍
- [油管-Git Tutorial: Diff and Merge Tools](https://www.youtube.com/watch?v=iCGrKFH2oeo)
- [diffmerge 下载](https://sourcegear.com/diffmerge/downloads.php) 选择 OS X 10.6+ Installer (Intel)	版本
- [diffmerge 帮助文档](http://www.sourcegear.com/diffmerge/webhelp/sec__git__mac.html)
- [博客园-DiffMerge安装配置使用](https://www.cnblogs.com/happyflyingpig/p/7850420.html) 讲了如何配置 UTF-8
- [对于解决 Git 的 Merge Conflict 你有哪些经验和技巧](https://www.zhihu.com/question/21215715)
- [SOF-How to resolve merge conflicts in Git](https://stackoverflow.com/questions/161813/how-to-resolve-merge-conflicts-in-git)