---
title: Git入门私房菜
tags: [Git, Tools]
toc: true
date: 2015-12-07 14:51:29
categories: Tools
thumbnail: http://ww3.sinaimg.cn/thumbnail/6d9475f6jw1eyr4l2cttjj20b40b4mxi.jpg
---
昨天下午参考廖雪峰的博客和其他一些文章，简单了解了一下传说中的Git，发现常见用法入门还是挺容易上手的，在此做一些笔记，方便以后查阅和复习。

<!-- more -->

----

# Git安装

## Linux

`sudo apt-get install git`命令安装Git

## Windows

[官网下载](https://git-for-windows.github.io/) 安装即可，不赘述。

注：安装时注意一步：选择第二项，将git添加到path中。



# 创建版本库

1. 创建项目文件夹，例如：demo

2. 进入demo目录下：`git init`命令将这个目录变成git可以管理的仓库（repository）。将仓库建好后，仓库目录下会多了一个.git隐藏文件夹。可以用`ls -ah`查看隐藏文件。

3. 向库里添加一个`readme.txt`文件：

4. `git add`告诉git，要把文件添加到仓库：

    ```
    git add readme.txt
    ```
    执行命令，其实，这一步的作用就是将文件添加到stage（暂存区）。

    + `git add .`将所有文件添加到暂存区。

    + `git add -u`将所有文件添加暂存区，同时，将删除的文件从仓库中删除。

5. `git commit`命令，将文件提交到仓库。

    ```
    git commit -m "wrote a readme file"
    ```
    注：`-m`是对本次提交的说明，不该偷懒省去。

6. `git status`查看当前版本库的状态。

7. `git diff`显示具体的尚未缓存的改动记录。

8. `git log`记录每次`commit`的信息。`git log --pretty=oneline`将每次`commit`的记录打印成一行，内容精简。

9. `git reset`修改命令：

    + `git reset HEAD`废除这次修改记录，当前文件回到上一次提交的状态。

    + `git reset --hard [commit id]

10. `git rm`慎用。这个命令会将所有版本库记录删除。

# 远程仓库

创建SSH Key。在类似`C:\Users\xiang_000\.ssh`这样的主目录下，查看是否有`id_rsa`和`id_rsa.pub`文件。如果有了，直接可以将公钥添加到github账户上。如果没有，打开Shell(windows下的Git Bash):

```
ssh-keygen -t rsa -C "youremail@example.com"
```

`id_rsa.pub`是公钥，可以放心的告诉任何人。

在github的Account settings里，Add SSH Key。

## 从远程库克隆

如果从零开发，**推荐**的方式是先在github上创建远程仓库。然后，从远程库**克隆**。

在github创建名为：`learngit`的新仓库。勾选添加`README.md`。

`git clone`克隆一个本地库：

```
git clone git@github.com:michael728/learngit.git
```

## 添加(关联）远程库

如果本地已经有本地仓库了，要和远程的仓库关联起来。
在github创建新的repo，名字为：demo。根据github的提示，在本地的`demo`仓库下运行命令：

```
git remote add origin git@github.com:michae728/demo.git`
```

+ 请千万注意，把上面的`michae728`替换成你自己的GitHub账户名，否则，你在本地关联的就是我的远程库，关联没有问题，但是你以后推送是推不上去的，因为你的SSH Key公钥不在我的账户列表中。

+ 添加后，远程库的名字就是origin，这是Git默认的叫法，也可以改成别的，但是origin这个名字一看就知道是远程库。

将本地库的所有内容推送到远程库上：

```
git push -u origin master
```

把当前本地分支`master`推送到远程。由于之前远程库是空的，**第一次推送`master`分支时，加上了`-u`参数**。git不但把本地的`master`分支内容推送到远程新的`master`分支，还会把本地`master`分支和远程的`master`分支关联起来，以后推送或者拉取就可以简化命令。

从现在起，本地提交，可通过命令：

```
git push origin master
```

# 分享与更新项目

## `git push`:

+ `git push origin dev`表示提交到远程的dev分支。

+ 通常在本地的dev分支推送到远程的dev分支，注意两地分支命名要相同，保证不发生混乱

## `git pull`:

+ `git pull origin dev`表示拉远程dev分支到本地，并和本地dev分支合并

`git fetch`(不常用）：

+ `git fetch origin dev`表示拉远程dev分支到本地，但是，不和本地dev分支合并

## `git remote`:
+ `git remote add github git@github.com:michael728/test.git`将本地仓库推送到github上名为test的仓库里



# 分支管理

## 创建于合并分支

创建`dev`分支，然后切换到`dev`分支：

```
git checkout -b dev
```

相当于下面两条命令：
```
git branch dev
git checkout dev
```

`git branch`命令用于查看当前分支。当前分支前面会有`*`:

+ `git branch`查看本地分支

+ `git branch -a`查看本地和远程所有分支

+ `git branch -r`查看远程分支

+ `git branch -d`删除本地分支

+ `git push :origin dev`删除远程`dev`分支

+ `git checkout master`命令用于`dev`分支完成工作后，切换回`master`分支。


`git merge`用于本地的指令。如果当前分支是master，本地另一个分支是`dev`，用下面命令将分支合并到master：

```
git merge dev
```

# 后悔药

## 版本回退

`git log`来查询历史记录。`git log --pretty=oneline`显示精简信息。

git中，`HEAD`表示当前版本，上一个版本就是`HEAD^`,上上个版本`HEAD^^`，上100个版本，写成`HEAD~100`。

回到上一个版本：

```
git reset --hard HEAD^
```

或者通过`git reset --hard [commit id]`回到指定版本号的版本。

git版本的回退速度很快，因为git内部有个指向当前版本的`HEAD`指针。git仅仅是通过修改`HEAD`指向就可以完成版本切换的工作。

`git reflog`用来查看命令历史。通过这个，就可以查到之前的版本号啦！

## 工作区和暂存区

工作区（Working Directory)

例如我们创建的`demo`文件夹，他就是一个工作区。

版本库（Repository)

工作区有一个隐藏目录`.git`，这个不算工作区，它是git的版本库。

git的版本库里存了很多东西，最重要的是称为`stage`的暂存区，还有git为我们自动创建的第一个分支`master`，以及指向`master`的一个指针叫`HEAD`。

![版本库示意图](http://ww2.sinaimg.cn/large/6d9475f6jw1eyr4eiqsbsj20dm07bt94.jpg)
说明：

+ `git add`实际上就是把文件修改添加到暂存区；

+ `git commit`提交更改，实际上就是把暂存区的所有内容提交到当前分支。

因为我们创建爱你git版本库是，git自动为我们创建了唯一一个`master`分支，所以，现在`git commit `就是往`master`分支上提交更改。（因为以后还有可能往分支上提交啊！）

        + `git status`可以查看状态。如果有文件从来没有被添加过，那么它的状态是：`Untracked`。

        + 提交成功后，如果没有对工作区做任何修改，那么工作区就是`working directory clean`

## 管理修改

1. `git diff HEAD -- README.md` 命令查看工作区和版本库里最新版本的区别。

## 撤销修改

分为两种情况的修改：

修改了工作区某个文件的内容，想直接丢弃修改：

```
git checkout -- filename
```

注：`--`前后都有空格。

当修改了工作区内容，同时，添加到了暂存区，分两步：

+ 用命令`git reset HEAD filename`,回到场景1

+ 用场景1的方法操作。

## 删除文件

当删除了工作区的文件，git知道删除了文件，因此，工作区和版本库就不一致。`git status`命令会立刻告诉那些文件被删除了。有两个选择：

1. 确实要从版本库里删除该文件，用命令`git rm fimename`,之后`git commit`提交。

2. 误删，因为版本库里还有，可以很轻松的把误删的文件恢复到最新版：`git checkout -- filename`

注：`git checkout`其实是用版本库里的东西替换工作区的版本。无论工作区是修改还是删除，都可以一键还原。

# 附件

1. ![Git图谱](http://ww2.sinaimg.cn/large/6d9475f6jw1eyr5dwu6iwj21cv2fu4qp.jpg)
2. ![Git常用中文命令图谱](http://ww1.sinaimg.cn/large/6d9475f6jw1eyt6ze6ba8j21kw146tqg.jpg)

欢迎来我的博客：[Git入门私房菜](https://michael728.github.io/2015/12/07/git-tutor/)

# 参考

1. [廖雪峰git教程](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)
2. [21分钟入门Git](http://www.html-js.com/article/2331)

<script src='//w.segmentfault.com/card/1030000002497141.js?w=0&3rd=1&bg=0&bd=DDDDDD&cl=333333&btn=009a62&noBtn=0'></script>
----

