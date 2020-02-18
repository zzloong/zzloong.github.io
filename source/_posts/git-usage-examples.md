---
title: Git 基础 —— 常见使用场景
date: 2019-11-14 20:21:24
tags: [ss,vps]
categories: ToolsDev
keywords:
---

## Git 基础学习系列

- [Git 基础 —— 安装 配置 别名 对象](https://michael728.github.io/2019/11/14/git-install-config/)
- [Git 基础 —— 常用命令](https://michael728.github.io/2019/11/14/git-useful-commands/)
- [Git 基础 —— 常见使用场景](https://michael728.github.io/2019/11/14/git-usage-examples/)
- [Git基础 —— Github 的使用](https://michael728.github.io/2019/11/14/git-github-experiance/)


## 突然插入 Bugifx 工作，回退工作目录
- `git stash` 保存所有工作内容，放到一个特殊的区域，为了避免遗忘，可以 `git stash save [message] ` 保存一点提示信息；
- `git stash list` 查看保存列表，如 `stash@{2}` 和 `stash@{1}`，序号中数字大的代表的是较早的 stash；
- `git stash show stash@{0} -v` 比较当前工作目录和 stash 的内容做比较；
- `git stash clear` 可以清除刚刚所有保存的信息；

解决完 bug，又要切回之前的状态：
```
git stash pop|apply [stash]
```

- `apply` 作用，恢复之前存放的内容，stash 列表中的内容还在；
- `pop` 恢复之前内容后，stash 列表中的内容就不存在了；

用法：
```
git stash pop stash@{2}
git stash pop = git stash pop stash@{0}
```
我们 pop 的时候可以加具体的序号，不加序号的（缺省情况下）为 `stash@{0}`。

stash 回来是有可能发生冲突的，需要解决冲突

## 多人单分支协作
有个新特性需要开发，在远端仓库新建了特性分支 `feature/add_gitcommand`，现在，团队中的多人需要基于这个分支进行协作开发。

### 本地拉取特性分支进行开发
如果远端这个分支已有同事提交了代码，而本地还没有这个分支，需要先 fetch 远端分支到本地（本地裸仓库中会增加相关 commit 信息），才能在 `git branch -av` 中看到这个远端分支信息：
```
git fetch github
```

注意，因为我本地仓库添加了多个远端主机，此处的 `github` 是我当时 `git remote add`  时给远端主机起的别名，一般会叫 `origin`。

接着，在本地创建并切换到远端特性分支：
```
# 拉取远端分支时，会自动将本地的这个分支和其关联
git checkout -b feature/add_gitcommand github/feature/add_gitcommand
```

如果直接 push 的话，往往会遇到错误。因为在你提交之前，远端分支又有了新的提交，有时候内容和你修改的地方一样，还会有冲突。只要你的提交落后了，那么不管有没有冲突，推送时都会报错：
![](https://ws1.sinaimg.cn/mw690/6d9475f6ly1fzcxmhkaeqj211c09atai.jpg)

为了避免上面的问题，在本地开发提交之后，执行推送操作之前需要拉取远端在进行合并：
```
git pull github
#  pull 等价于下面两个操作
git fetch github
git merge github/feature/add_gitcommand
```

![](https://ws1.sinaimg.cn/mw690/6d9475f6ly1fzcxqgwc5yj20to044aak.jpg)

发生了冲突，需要我们在推送之前先本地解决一下冲突：

查看有冲突的文件
```
hello
<<<<<<< HEAD
we are on feature/add__gitcommand
we are on michael728
=======
we are on feature/add__gitcommand 123
>>>>>>> c6fc3bc57c65f099a45b1c98b6ce2782a2c072f9
```

HEAD 和 `=====` 之间的内容就是发生冲突的地方，这之间的内容是我们本地提交的内容，`====` 和 `>>>>>` 之间的是远端的内容。

删除这些标记行，然后重新 `add `、`commit`，最终 `git push github`即可推送到远端特性分支了，如果只关联了一个远端仓库，那么直接`git push`即可。

什么时候会有冲突呢？
远端修改的地方，比如修改了第一行，而本地也修改了第一行，这时候 merge 的时候，就会发生冲突。

### 同时变更了文件名

某同事在他提交时，通过 `git mv` 命令重命名了一个文件，`index.html to index.htm`，并且推送到了远端仓库中。

这时候我在本地修改了 `index.html` 内容，并进行了提交，准备推送到远端。因为远端又比本地多了提交记录，所以会 `rejected`，这时候执行：
```
git pull
```

执行完之后，发现本地 Git 自动帮我们也对文件进行了重命名并进行了内容的合并。

前面讲 Git 的文件存储时，说 Git 存放 blob 文件时是以文件内容来区分的，并不以文件名来区分；此处的变更文件名操作和变更文件内容的操作能够自动被 Git 处理，原因就在于 blob 文件并没有发生修改的冲突。

如果其中一个人既变更了文件名又修改了文件，同时另一个人也修改了该文件的同一位置的内容，就会被 Git 识别为冲突，而不能自动进行处理了，处理方法和下面一种情况类似，都需要手动来处理了。

### 把同一文件修改为了不同的文件名

A修改了文件的文件名并进行了提交推送到了远端，B也修改了文件名，这时候也准备推送。

![](https://ws3.sinaimg.cn/large/006tNc79ly1fzd5am0p7uj31380na7ac.jpg)

`diff index1.html index2.html` 发现两个文件内容一致，通过 `git status` 查看状态，需要自己手动进行处理。

## Git 集成的禁忌
- 禁止向集成分支执行 `git push -f` 操作，集成分支会丢失 commit 的；
- 禁止向集成分支执行变更历史的操作，公共的分支严禁拉到本地，进行 rebase 操作的，因为这也会造成丢失 commit；

## 想知道某行代码谁修改的

阅读代码时，想知道某行代码是谁修改的？

- 找到对应 `commit id`：
```
git blame src/xxx.c
```

- 查看具体提交的内容
```
git show <commit id>
```

## 回退到历史版本

- 查找commit id
  通过`git log`查找想要会退到的历史版本的`commit id`
- 本地执行回退

```
# 这会让工作区和暂存区都回退到这个 commit id 对应的时间点的状态
git reset --hard [commit id]
```

- 强制推送
```
git push -f
```

参考：[git 远程仓库版本的回退以及git reset 几种常用方式记录](https://www.cnblogs.com/chenfulin5/p/6732036.html)

## cherry-pick
将某一提交点的修改拿到当前分支上：
`git cherry-pick <commit id>`

这会将那次的提交与当前内容进行合并，然后生成一个新的 commit。

## 查找 Git 仓库的活跃贡献者
输出git repo中前十位最活跃的提交者：
```
git shortlog -s | sort -rn | head 
```

## 如何将 Git 仓库备份到本地

常用的传输协议：
![](https://ws4.sinaimg.cn/large/006tNc79ly1fzcai93n6yj30sh0ftwh6.jpg)

- 直观区别：哑协议传输进度不可⻅；智能协议传输可⻅。
- 传输速度：智能协议⽐哑协议传输速度快。

创建用作备份的裸仓库，通常位于远端

- 哑协议克隆一个不带工作区的（bare）裸仓库：
```
 git clone --bare /Users/michael/Code/Git-Geek/git_learning
```

- 智能协议克隆一个裸仓库并重命名：
```
# /Users/michael/Code/Git-Geek/666-backup/zhineng.git
git clone --bare file:///Users/michael/Code/Git-Geek/git_learning/.git zhineng.git
```

为了将本地工作仓库备份到远端仓库，下面，切到之前的工作仓库：
```
# /Users/michael/Code/Git-Geek/git_learning
git remote -v # 发现没有关联的远端仓库
git remote add zhineng file:///Users/michael/Code/Git-Geek/666-backup/zhineng.git # 发生关联了
git push zhineng
```

`file:///Users/michael/Code/Git-Geek/666-backup/zhineng.git` 就是表示的远端备份仓库地址。

从远端备份仓库克隆：
```
git clone file:///Users/michael/Code/Git-Geek/666-backup/zhineng.git git_learnging_2019
```

## 从一个 Git 仓库搬迁到另外一个 Git 仓库中
1. 新建一个空仓库B，注意，不要勾选生成 `README.md` 文件；
2. `git  clone --bare A仓库`
3. `git push  --mirror B仓库`

这种方式最方便，一次性把分支、tag、commit 记录都同步过去了

参考：

- [从一个git仓库迁移到另外一个git仓库](https://blog.csdn.net/samxx8/article/details/72329002)

## 本地代码库关联到另外的一个代码库

```shell
cd existing_repo
git remote rename origin old-origin
git remote add origin ssh://git@github.com:2222/Michael/my-config-files.git
git push -u origin --all
git push -u origin --tags 
```

## 仓库设置多个远端仓库

```shell
git remote add github git@github.com:Michael728/awesome-books-for-me.git
git push -u github master
git remote add gitee git@gitee.com:michael_xiang/awesome-books-for-me.git
git push -u gitee master
```

## Git lfs 管理大文件

Git LFS（Large File Storage, 大文件存储）是 Github 开发的一个 Git 的扩展，用于实现 Git 对大文件的支持。

Git LFS可以把音乐、图片、视频等指定的任意文件存在 Git 仓库之外，而在 Git 仓库中用一个占用空间 1KB 不到的文本指针来代替文件的存在。

通过把大文件存储在 Git 仓库之外，可以减小 Git 仓库本身的体积，使克隆 Git 仓库的速度加快，也使得 Git 不会因为仓库中充满大文件而损失性能。

使用细节，可以看码云的介绍：[Git LFS 操作指南](https://gitee.com/help/articles/4235#article-header0)

常用命令：
```shell
# 使用 Git LFS 管理指定的文件
git lfs track "*.psd"
# 查看当前使用 Git LFS 管理的匹配列表
git lfs track
# 不再使用 Git LFS 管理指定的文件
git lfs untrack "*.psd"
# 类似 git status，查看当前 Git LFS 对象的状态
git lfs status
# 枚举目前所有被 Git LFS 管理的具体文件
git lfs ls-files
```

## Git FAQ
### git merge和git rebase的区别
rebase 跟 merge 的区别你们可以理解成有两个书架，你需要把两个书架的书整理到一起去，第一种做法是 merge ，比较粗鲁暴力，就直接腾出一块地方把另一个书架的书全部放进去，虽然暴力，但是这种做法你可以知道哪些书是来自另一个书架的；第二种做法就是rebase ，他会把两个书架的书先进行比较，按照购书的时间来给他重新排序，然后重新放置好，这样做的好处就是合并之后的书架看起来很有逻辑，但是你很难清晰的知道哪些书来自哪个书架的。

只能说各有好处的，不同的团队根据不同的需要以及不同的习惯来选择就好。

rebase 和 merge 的另一个区别是rebase 的冲突是一个一个解决，如果有十个冲突，先解决第一个，然后用命令

```
git add -u
git rebase --continue
```

继续后才会出现第二个冲突，直到所有冲突解决完，而merge 是所有的冲突都会显示出来。

另外如果rebase过程中，你想中途退出，恢复 rebase 前的代码则可以用命令

```
git rebase --abort
```

关于`git rebase`还有很多知识点：

- [聊下git rebase -i](https://www.cnblogs.com/wangiqngpei557/p/5989292.html)
- [git merge 和 git rebase 小结](https://blog.csdn.net/wh_19910525/article/details/7554489)
- [Git Community Book 中文版-rebase](http://gitbook.liuhui998.com/4_2.html)
- [压缩多个Commit](https://www.cnblogs.com/wangdaijun/p/5956129.html)
- [合并多个 Commit](https://www.jianshu.com/p/964de879904a)

## 其他资料

- [IBM-Git 进阶：比较、回滚、撤销、分支合并和冲突解决](https://www.ibm.com/developerworks/cn/opensource/os-cn-git-and-github-4/index.html?ca=drs-)