---
title: Git使用教程笔记
date: 2018-11-24 21:23:04
tags: [Git, Tools]
categories: Tools
keywords:
---

![](https://ws1.sinaimg.cn/mw690/6d9475f6ly1fxjh6nzepxj23vc2kwe81.jpg)

虽然之前写过一篇入门文章[Git入门私房菜](https://michael728.github.io/2015/12/07/git-tutor/)，但是工作中接触到的 Git 命令还有一些。有时候遇到一些小功能，就上网查一下，本文就针对用到的知识点做个全面的总结。

<!-- more -->

需要先在 Github 上创建个属于你的仓库，本文仓库名以 `michalel-git` 为例。

地址为： `git@github.com:Michael728/michael-git.git`

## Git 安装

[Git下载地址](https://git-scm.com/downloads)

Windows安装时需要注意在`Configuring the line ending conversions`界面，选择`Checkout as-is,commit as -s`，避免Windows的换行符问题。如果忘记设置，可以使用如下命令后期设置：

```
git config --global core.autocrlf false
```

参考：

- [GitHub 第一坑：换行符自动转换 ](https://github.com/cssmagic/blog/issues/22)

## Git 配置

可以通过 `git config -l` 查看配置。

### 设置 Git 账号

```
git config --global user.name "michael728"
git config --global user.email "649168982@qq.com"
```

### Git 配置别名

`git config`文件来轻松为每一个命令设置别名。例如：

```
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
```

如果你想要执行外包命令，而不是一个 Git 子命令，可以在命令前面加 `！` 符号。

演示将git visual定义为gitk的别名：

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

我们可以体验一个log的别名命令设置：

```
lg = log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --date=relative
```

这是超厉害的别名缩写命令，试试现在的 `git lg` 有多酷炫吧！

## Git 帮助文档

授人以鱼不如授人以渔，先知道怎么通过帮助文档查看常用命令的说明吧：

```
git help
git help <cmd>
```

## 创建 Git 本地仓库

### 已有远端仓库，创建本地仓库

```
git clone git@github.com:Michael728/michael-git.git
cd michael-git
touch README.md
git add README.md
git commit -m "add README"
git push -u origin master
```

### 已存在文件夹

```
cd micahel-git
git init
git remote add origin git@github.com:Michael728/michael-git.git
git add .
git commit
git push -u origin master
```

### 已存在 Git 仓库

```
cd existing_repo
git remote add origin git@github.com:Michael728/michael-git.git
git push -u origin --all # --all 表示 Push all branches，-u 选项指定了一个默认主机
git push -u origin --tags # --tags All refs under refs/tags are pushed
```

将本地的`master`分支推送到`origin`主机，同时指定`origin`为默认主机，后面就可以不加任何参数使用`git push`了。

参考：
- [git push 的 -u 参数具体适合含义？](https://www.zhihu.com/question/20019419)
- [Git远程操作详解](http://www.ruanyifeng.com/blog/2014/06/git_remote.html)

## git clone

```
git clone [-b br_name] url git@github.com:Michael728/michael-git.git
```

克隆的时候，可以指定下载远端的分支、自定义本地仓库的名字。如果不加分支名参数，`git clone` 命令会默认自动设置本地 `master` 分支跟踪克隆的远程仓库的 `master` 分支（其实是仓库的默认分支）。而且，默认远程仓库设置别名为 `origin`。

## git add

这是个多功能命令:
- 可以用它开始跟踪新文件
- 把已跟踪的文件放到暂存区
- 还能用于合并时把有冲突的文件标记为已解决状态，这个是在解决冲突时会用到的功能

## git commit

提交的操作，当你前面采用`add`命令将文件添加到暂存区跟踪后，需要通过`commit`将暂存区的内容提交到当前分支。

```
git commit -m "test"
```

当一些已追踪的文件修改了，常常需要`git add file`，然后在`git commit -m "xxxx"`，其实这两个步骤可以合二为一：
```
git commit -am "test"
```

这么写个人觉得挺好，可以有效避免有些懒人`git add .`的方式，将一切文件都添加到了暂存区，导致最后多余文件提交入库。

## git push

语法：

```
git push <远程主机名> <本地分支名>:<远程分支名>
```

如果省略远程分支名，则表示将本地分支推送与之存在"追踪关系"的远程分支（通常两者同名），如果该远程分支不存在，则会被新建。

当想要将`master`分支推送到`origin`服务器上：

```
git push origin master
```

将本地分支`test`推送到远端时可以重命名：

```
git push origin test:remote-test
```

利用该用法，还可以推送空分支到远端，实现远端分支的删除：

```
git push origin :remote-test
# 和如下命令等同
git push origin --delete remote-test
```

## git rm

从 Git 中移除某个文件，就必须从已跟踪的文件清单中删除（从暂存区域移除文件），然后提交。可以使用`git rm`命令完成此项工作，并连带从工作目录中删除指定的文件。

当我们先把某文件从 Git 库中删除（亦即从暂存区移除），但仍然希望保留在当前工作目录中。比如当你忘记在`.gitignore`文件中将一些文件忽略，但是却不小心把大的日志文件添加到暂存区域时，这一做法很有用：

```
# --cached 将 README 文件从暂存区移除，但是工作区目录仍然保留
git rm --cached README
```

## git checkout

切换到某个历史版本：

```
git checkout <commit id>
```

在 Git 中从当前分支创建并检出新分支的命令是：

```
git checkout -b new-br
```

这个命令实际是：

```
git checkout -b new-br current-br
```

在本地创建并切换到远端的分支：

```
git branch -va # 查看本地+远程分支列表
git checkout -b dev  origin/dev
```

还可以可以在`checkout`命令中使用Hash值作为起点创建分支：

```
git checkout -b name-of-branch <commit id>
```

除了有“切换”的意思，`checkout`还有一个撤销的作用。

举个例子，假设我们在一个分支开发一个小功能，刚写完一半，这时候需求变了，而且是大变化，之前写的代码完全用不了，好在你刚写，甚至都没有 `git add` 进暂存区，这个时候很简单的一个操作就直接把原文件还原：

```
git checkout <filename>
```

参考：[在git中checkout历史版本](https://www.cnblogs.com/crazyacking/p/5620635.html)

## git log

```
git log --pretty=oneline # 检查提交日志，都在一行：<commit id> <message>
```

查看某人的提交：

```
git log --author=michael
```

一个常用的选项是`-p`，用来显示每次提交的内容差异，也可以加上`-2`或者`-n2`来仅显示最近两次提交：

```
git log -p -2
git log -n1 --format=format:%h # 查看当前分支最新的 commit id 缩略值
```

列出最近两周内的提交：

```
git log --since=2.weeks
```

- [git log命令全解析，打log还能这么随心所欲！](http://www.cnblogs.com/bellkosmos/p/5923439.html)
- [git-scm 2.3 Git 基础 - 查看提交历史](https://git-scm.com/book/zh/v2/Git-%E5%9F%BA%E7%A1%80-%E6%9F%A5%E7%9C%8B%E6%8F%90%E4%BA%A4%E5%8E%86%E5%8F%B2)


## git diff

`git diff`本身只显示尚未暂存的改动，而不是自上次提交以来所做的所有改动。

若要查看已暂存的将要添加到下次提交里的内容和上次提交的内容的变化，可以用`git diff --staged`

## git branch

### 新建develop分支：

```
git branch develop
```

### 切换分支：

复习一下`checkout`的用法

```
git checkout develop
```

### 新建并切换到develop分支：

```
git checkout -b develop
```

### 将分支develop推送到远程仓库`origin`：

```
git push origin develop
```

### 如果想给远程的分支取名为develop2，可以：

```
git push origin develop:develop2
```

**不建议这么操作易混乱，还是本地分支名和远程分支名保持一致比较好。**

### 关联本地分支和远程分支：

创建本地分支并切换到分支：`git checkout -b tools-dev`

创建远程分支：`git push origin tools-dev`

本地分支推送到远程服务器时，远程分支自动创建，推送本地分支到远程：

```
git push --set-upstream <remote-name> <local-branch-name>:<remote-branch-name>
```

- `<remote-name>`：远程 Git 服务器名称，一般为`origin`
- `<local-branch-name>`：本地分支名称
- `<remote-branch-name>`：远程分支名称

一般情况下，本地分支和远程分支名称相同，所以可简化为：

```
git push --set-upstream <remote-name> <branch-name>
```

`--set-upstream`参数用来关联本地分支和远程分支

参考：
- [Git创建远程分支](https://majing.io/questions/718)
- [Git远程操作详解](http://www.ruanyifeng.com/blog/2014/06/git_remote.html)

### 查看本地分支：

```
git branch
```
### 查看远程分支：

```
git branch -r
```

### 删除本地分支：

```
git branch -d develop
git branch -D develop（强制删除）
```

### 删除远程分支：

```
git push origin :remote-test
# 和如下命令等同
git push origin --delete remote-test
```

## 撤销操作

```
git commit --amend
```

如果已经提交完了（已经`commit`）了，发现漏掉几个文件没有添加，或者提交信息（`-m `)写错了，可以运行带有`--amend`选项的提交命令重新提交。

这个命令会将**暂存区**的文件提交。文本编辑器编辑后，会覆盖原来的提交信息。

### 取消暂存文件

```
git reset HEAD  <file>
```

### 撤销对文件的修改

```
git checkout -- <file>
```

> 你需要知道 `git checkout -- [file]` 是一个危险的命令，这很重要.

## 远程仓库的使用

### 查看远程仓库

```
git remote -v
```

如果想查看远程仓库更多的信息，可以使用`git remote show <remote-name>`命令。

远程仓库的移除与重命名

```
git remote rename  <old-remote-name> <new-remote-name>
git remote rename pb paul
```

如果因为一些原因要移除一个远程仓库，可以使用`git remote rm <remote-name>`。

### 添加一个新的远程 Git 仓库，同时指定一个可以轻松引用的简写：

```
git remote add <remote-name> <url>
```

这里的`remote-name`常常取名为`origin`。所以，常见的`origin`其实是一个你 Git 仓库跟踪的远程仓库的简写。

### 拉取远端仓库有但你本地没有的信息：

```
git fetch <remote-name>
```

如果你使用`clone`命令克隆了一个仓库，命令会自动将其添加为远程仓库并默认以`origin`为缩写。

## Tag

### 列出标签

```
git tag # 列出所有标签
git tag -l 'v1.8*' # 列出以 v1.8 开头的所有标签
```

### 创建标签

Git使用两种主要类型的标签：

- 附注（annotated）标签
- 轻量（ightweight）标签

前者会包括一些注释信息，来进一步解释这个 tag 的作用，而后者就仅仅只是一个 tag 的名字

#### 附注标签

```
git tag -a v1.4 -m 'my version 1.4'
```

通过`git show <tag-name>`命令可以看到标签信息

#### 轻量标签

```
git tag v1.4
```

没用`-a`、`-m`的参数，只需要提供标签名字

### 删除标签

```
git tag -d <tagname>
```

### 补打标签

#### 假设忘记给项目打标签，可以在之后加上：

基于某历史节点的`commit id`补打`Tag`：

```
git tag -a v1.2 <commit id>
```

#### 共享标签

默认情况下，`git push`命令并不会传送标签到远程服务器上。在创建完标签后你必须显示地推送标签到共享服务器上。这个过程就像共享远程分支一样，可以运行`git push origin [tagname]`

如果想要一次性推送很多标签，也可以使用`--tags`选项的`git push`：

```
git push origin --tags
```

#### 检出标签

```
git checkout -b <new-br> <tagname>
```

参考：

- [The Junior Git](https://thoamsy.github.io/blogs/git/)
- [6 Git 基础 - 打标签](https://git-scm.com/book/zh/v1/Git-%E5%9F%BA%E7%A1%80-%E6%89%93%E6%A0%87%E7%AD%BE)

## 使用场景

### 新特性开发，创建临时分支，再合并到主干
```
#创建特性分支
git checkout -b featureA
……
#提交之前，先checkout到master分支，更新一下
git co master
git pull
#切到特性分支，在本地与最新的master分支合并
git co featureA
git rebase -i master
```

参考：

- [聊下git rebase -i](https://www.cnblogs.com/wangiqngpei557/p/5989292.html)

### 本地仓库关联远程仓库：

已有本地仓库，需要关联远端仓库，分两步：
第一步，在Github上新建一个仓库test；
第二步：将本地仓库与Github上的test项目进行关联，切换到本地仓库目录：
`git remote add origin git@github.com:xx/test.git`
什么意思？远程仓库的地址为：`git@github.com:xx/test.git`，而origin是给这项目的远程仓库起的名字，是的，名字你可以随便取，只不过大家公认的只有一个远程仓库时名字就是origin，为什么要给远程仓库取名字？因为我们可能一个项目有多个远程仓库，比如，Github一个，比如公司一个，这样的话，提交的时候可以提交到不同的远程仓库就需要指定不同的仓库名字了。

查看我们当前项目有哪些远程仓库可以执行如下命令：

```
git remote -v
```

接下来，本地的仓库可以向远程仓库进行代码提交了：

```
git push origin master
```

在代码提交之前，西安设置自己的用户名和邮箱，这些信息会出现在所有的commit记录里：

```
git config --global user.name "xxx"
git config --global user.email "xx@mail.com"
```

### 二分查找，傻瓜式定位bug

场景：定位Bug，当前版本有Bug，上个版本没有，两个版本之前有上千次commit
二分查找，N个patch只需要测试log2N次（8k个path仅需测试13次）
可以实现测试自动化，自动查找问题patch
`git bisect`：自动定位，不必找原作者

### 同时负责多个Bug的修改

- 更新远程仓库代码

```
git fetch
```

- 以origin/master为基础创建分支

```
git checkout -b fix/bug23 origin/master
```

- 修改完

```
git commit
```

- 推送前更新一下代码，看看别人是否有修改

```
git fetch
```

- 有修改的话rebase一下

```
git rebase origin/master
```

- 生成patch

```
git format-patch origin/master
```

- 发送patch或者也可以使用request pull

```
send email OR Request pull
[maintainer接收后]
```

- 分支使用已经完成，可以删除了

```
git branch -D fix/bug23
```

### 想知道某行代码谁修改的

阅读代码时，想知道某行代码是谁修改的？

- 找到对应commit id

```
git blame src/xxx.c
```

- 查看具体提交的内容

```
git show <commit id>
```

### 远端仓库会退到历史版本

- 查找commit id
  通过`git log`查找想要会退到的历史版本的`commit id`
- 本地执行回退

```
git reset --hard [commit id]
```

- 强制推送

```
git push -f
```

参考：[git 远程仓库版本的回退以及git reset 几种常用方式记录](https://www.cnblogs.com/chenfulin5/p/6732036.html)

### 突然插入bugifx，回退工作目录

`git stash`保存所有工作内容

### cherry-pick

将某一提交点的修改拿到当前分支上：
`git cherry-pick 哈希值`

## 基本的团队协作流程

多人协作下的分支管理规范很重要，就跟代码规范一样重要。

以下就跟大家推荐一种我们内部在使用的一种分支管理流程 Git Flow。

Gti Flow
Git Flow 是一种比较成熟的分支管理流程，我们先看一张图能清晰的描述他整个的工作流程：

大部分情况下都会拥有两个分支 master 和 develop，他们的职责分别是:

- master：永远处在即将发布(production-ready)状态
- develop：最新的开发状态

确切的说 master、develop 分支大部分情况下都会保持一致，只有在上线前的测试阶段
develop 比 master 的代码要多，一旦测试没问题，准备发布了，这时候会将 develop 合并到
master 上。

但是，我们发布之后又会进行下一版本的功能开发，开发中间可能又会遇到需要紧急修复 bug
，一个功能开发完成之后突然需求变动了等情况，所以 Git Flow 除了以上 master 和 develop
两个主要分支以外，还提出了以下三个辅助分支：

- feature: 开发新功能的分支, 基于 develop, 完成后 merge 回 develop
- release: 准备要发布版本的分支, 用来修复 bug，基于 develop，完成后 merge 回develop 和 master
- hotfix: 修复 master 上的问题, 等不及 release 版本就必须马上上线. 基于 master, 完成后merge 回 master 和 develop

什么意思呢？
举个例子，假设我们已经有 master 和 develop 两个分支了，这个时候我们准备做一个功能A，第一步我们要做的，就是基于 develop 分支新建个分支：

```
git branch feature/A
```

看到了吧，其实就是一个规范，规定了所有开发的功能分支都以 feature 为前缀。但是这个时候做着做着发现线上有一个紧急的 bug 需要修复，那赶紧停下手头的工作，立刻切换到 master 分支，然后再此基础上新建一个分支：

```
git branch hotfix/B
```

代表新建了一个紧急修复分支，修复完成之后直接合并到 develop 和 master ，然后发布。然后再切回我们的 feature/A 分支继续着我们的开发，如果开发完了，那么合并回 develop 分支，然后在 develop 分支属于测试环境，跟后端对接并且测试的差不多了，感觉可以发布到正式环境了，这个时候再新建一个 release 分支：

```
git branch release/1.0
```

这个时候所有的 api、数据等都是正式环境，然后在这个分支上进行最后的测试，发现 bug 直接进行修改，直到测试 ok 达到了发布的标准，最后把该分支合并到 develop 和 master 然后进行发布。

以上就是 Git Flow 的概念与大概流程，看起来很复杂，但是对于人数比较多的团队协作现实开发中确实会遇到这么复杂的情况，是目前很流行的一套分支管理流程，但是有人会问每次都要各种操作，合并来合并去，有点麻烦，哈哈，这点 Git Flow 早就想到了，为此还专门推出了一个 Git Flow 的工具，并且是开源的：

GitHub 开源地址： https://github.com/nvie/gitflow

简单点来说，就是这个工具帮我们省下了很多步骤，比如我们当前处于 master 分支，如果想要开发一个新的功能，第一步切换到 develop 分支，第二步新建一个以 feature 开头的分支名，有了 Git Flow 直接如下操作完成了：

```
git flow feature start A
```

这个分支完成之后，需要合并到 develop 分支，然而直接进行如下操作就行：

```
git flow feature finish A
```

如果是 hotfix 或者 release 分支甚至会自动帮你合并到 develop、master 两个分支。

想必大家已经了解了这个工具的具体作用，具体安装与用法我就不多提了，感兴趣的可以看
我下我之前写过的一篇博客： http://stormzhang.com/git/2014/01/29/git-flow/

### 详细演示下怎么给一个项目发起 Pull Request(PR)：

- 第一步，找到你想发起 PR 的项目，点击右上角的 Fork 按钮，然后该项目就出现在了你自己账号的 Repository 里。
- 第二步，把fork的项目 clone 到本地，然后修改的 bug 也好，想要新增的功能也好，总之把自己做的代码改动开发完，接着，把自己做的代码改动 push 到 你自己的 GitHub 上去。
- 第三步，点击你 Fork 过来的项目主页的 Pull requests 页面，点击右上角的New pull request。
  页面自动会比较该项目与原有项目的不同之处，最顶部声明了是源仓库的分支与你fork过来的分支的对比。同样的我写好标题和描述，然后我们点击中间的 Create pull request 按钮，至此我们就成功给该项目提交了一个 PR。
  然后就等着项目原作者 review 你的代码，并且决定会不会接受你的 PR，如果接受，那么恭喜你，你已经是该项目的贡献者之一了。

## Git FAQ

### git merge和git rebase的区别

```
git checkout master
git merge featureA
```

其实 rebase 命令也是合并的意思，上面的需求我们一样可以如下操作：

```
git checkout master
git rebase featureA
```

rebase 跟 merge 的区别你们可以理解成有两个书架，你需要把两个书架的书整理到一起去，第一种做法是 merge ，比较粗鲁暴力，就直接腾出一块地方把另一个书架的书全部放进去，虽然暴力，但是这种做法你可以知道哪些书是来自另一个书架的；第二种做法就是rebase ，他会把两个书架的书先进行比较，按照购书的时间来给他重新排序，然后重新放置好，这样做的好处就是合并之后的书架看起来很有逻辑，但是你很难清晰的知道哪些书来自哪个书架的。

只能说各有好处的，不同的团队根据不同的需要以及不同的习惯来选择就好。

rebase 和 merge的另一个区别是rebase 的冲突是一个一个解决，如果有十个冲突，先解决第一个，然后用命令

```
git add -u
git rebase --continue
```

继续后才会出现第二个冲突，直到所有冲突解决完，而merge 是所有的冲突都会显示出来。
另外如果rebase过程中，你想中途退出，恢复rebase前的代码则可以用命令

```
git rebase --abort
```

关于`git rebase`还有很多知识点：

- [聊下git rebase -i](https://www.cnblogs.com/wangiqngpei557/p/5989292.html)
- [git merge 和 git rebase 小结](https://blog.csdn.net/wh_19910525/article/details/7554489)
- [Git Community Book 中文版-rebase](http://gitbook.liuhui998.com/4_2.html)
- [压缩多个Commit](https://www.cnblogs.com/wangdaijun/p/5956129.html)
- [合并多个 Commit](https://www.jianshu.com/p/964de879904a)

### git branch -r与git branch -a的区别？

- `git branch -r`只显示远端分支，
- `git branch -a` 显示本地分支和远程分支

## 发现好用的开源项目-Github

GitHub 其中一个最重要的作用就是发现全世界最优秀的开源项目，你没事的时候刷刷微博、知乎，人家没事的时候刷刷 GitHub ，看看最近有哪些流行的项目，久而久之，这差距就越来越大，那么如何发现优秀的开源项目呢？

1. 关注一些活跃的大牛
2. Explore菜单下的Trending，看到最近的一些热门开源项目，很多人主动获取开源项目的最好的途径，可以选择“当天热门”，“一周之内热门”和“一月之内热门”来查看，并且，可以分语言来查看。
3. Search，按照Most Stars来筛选。
     除此之外，GitHub 的 Search 还有一些小技巧，比如你想搜索的结果中 star 数大于1000的，那么可以这样搜索：
       android http stars:>1000

有些人如果习惯用 Google 进行搜索，那么想搜索 GitHub 上的结果，不妨前面加 GitHub 关键字就ok了，比如我在 google 里输入 GitHub android http ，每个关键字用空格隔开。

## 福利大放送

GitHub 上影响力很大，同时又对你们很有用的项目：

- [free-programming-books]( https://github.com/EbookFoundation/free-programming-books):  这个项目整理了所有跟编程相关的免费书籍，而且全球多国语言版的都有，中文版的在这里 [free-programming-books-zh]( https://github.com/EbookFoundation/free-programming-books/blob/master/free-programm)
- [ob-my-zsh]( https://github.com/robbyrussell/oh-my-zsh):  俗话说，不会用 shell 的程序员不是真正的程序员。oh-my-zsh 毫无疑问就是目前最流行，最酷炫的 shell![zsh+on-my-zsh配置教程指南（程序员必备）](https://michael728.github.io/2018/03/11/tools-zsh-tutorial/)
- [awesome]( https://github.com/sindresorhus/awesome):  GitHub 上有各种 awesome 系列，简单来说就是这个系列搜罗整理了 GitHub 上各领域的资源大汇总，比如有 awesome-android, awesome-ios, awesome-java, awesome-python 等等等，就不截图了，你们自行去感受。
- [github-cheat-sheet]( https://github.com/tiimgreen/github-cheat-sheet/): GitHub 的使用有各种技巧，只不过基本的就够我们用了，但是如果你对 GitHub 超级感兴趣，想更多的了解 GitHub 的使用技巧，那么这个项目就刚好是你需要的，每个 GitHub 粉都应该知道这个项目。
- [LearningNotes](https://github.com/francistao/LearningNotes)：这是一份非常详细的面试资料，涉及 Android、Java、设计模式、算法等等等，你能想到的，你不能想到的基本都包含了，可以说是适应于任何准备面试的 Android 开发者，看完这个之后别说你还不知道怎么面试！

GitHub 上优秀开源项目真的是一大堆，就不一一推荐了，授人以鱼不如授人以渔，请大家自行主动发掘自己需要的开源项目吧，不管是应用在实际项目上，还是对源码的学习，都是提升自己工作效率与技能的很重要的一个渠道，总有一天，你会突然意识到，原来不知不觉你已经走了这么远！

## 最后

- [Git使用教程笔记](https://michael728.github.io/2018/11/24/git-advance/)
- [Pro Git 中文](https://git-scm.com/book/zh/v2)
- [Git常用命令备忘](https://foofish.net/git-command.html)