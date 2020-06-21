---
title: 初识 Git 基本命令 merge 和 rebase
date: 2020-06-21 23:21:24
tags: [Git]
categories: ToolsDev
keywords:
---

## 前言

Git 中的分支合并是一个场景的使用场景。

- 仓库的 bugfix 分支修复完 bug 之后，要回合到主干分支，这时候两个分支需要合并； 
- 远端仓库的分支 A 有其他小伙伴合入了代码，这时候，你需要和远端仓库的分支 A 进行合并；

以上只是列举了分支合并的一些常见场景，关于 `merge` 命令你足够了解她吗？

## HEAD 的理解

在介绍本文的主要内容之前，我们先理解 `HEAD` 是怎样的东西。

`HEAD` 指向**当前所在的分支**，类似一个活动的指针，表示一个「引用」。例如当前在 `develop` 分支，`HEAD` 内容就是 `ref: refs/heads/develop`。

`HEAD` 既可以指向「当前分支」的最新 `commit`，也可以指向历史中的某一次 `commit` (「分离头指针」的情况)。归根结底，`HEAD` 指向的就是某个提交点。

当我们做分支切换时，`HEAD` 会跟着切换到对应分支。

## fast-forward 与 --no-ff 的区别

假如有下面一个场景：目前有两个分支，master 分支和 feature 分支。现在，feautre 分支需要合并回 master 分支。

![fast-forward-初始状态](https://gitee.com/michael_xiang/images/raw/master/uPic/wOR7JK.png)

`fast-forward` 合并方式是**条件允许**的情况，通过将 master 分支的 HEAD 位置移动到 feature 分支的最新提交点上，这样就实现了快速合并。这种情况，是不会新生成 commit 的。

![fast-forward](https://gitee.com/michael_xiang/images/raw/master/uPic/QpdH5g.png)

而加入不采用 `fast-forward` 的方式，采用 `--no-ff` 的方式进行合并，master 分支就会新生成一次提交记录。

![--no-ff](https://gitee.com/michael_xiang/images/raw/master/uPic/BIqlQW.png)

> 默认情况下，如果条件允许，merge 默认采用的 fast-forward 方式进行合并，除非你显示的加上 --no-ff 选项；而条件不允许时，merge 也是无法使用 fast-forward 进行合并的！

## merge 操作

上面用图解的方式介绍了 `fast-forward` 和 `--no-ff` 的区别。下面，结合实际的代码仓进行合并操作，举几个栗子理解一下。

> `git merge` 操作是区分上下文的。**当前分支始终是目标分支**，其他一个或多个分支始终合并到当前分支。这个注意点记住了，方便记忆！所以，当需要将某个分支合并到目标分支时，需要先切到目标分支上。

### fast-forward 合并

刚刚一直在强调条件允许的时候，`fast-forward` 才能合并成功。这里的条件只的是什么呢？

其实指的是要合并的分支和要合入的分支之间没有分叉（单词 `diverge`）时，才可以进行快速合并。试想，如果是下图中的场景，HEAD 快速移动是无法实现两个分支连城一条线的！

![分叉](https://gitee.com/michael_xiang/images/raw/master/uPic/RQ39Rv.png)

下面进行一个不分叉的场景的示例：

![featuren 分支的初始状态](https://gitee.com/michael_xiang/images/raw/master/uPic/A87zPe.png)

现在需要将 feature 分支合入到 master 分支，默认使用的是 `fast-forward` 方式：

```
# 切到 master 分支
git checkout master
git merge feature
```

可以发现，命令行里显示了 `Fast-forward` 的提示：

![合入效果](https://gitee.com/michael_xiang/images/raw/master/uPic/J0Zku9.png)

看一眼 master 分支合入的前后对比：

![master 分支合入前](https://gitee.com/michael_xiang/images/raw/master/uPic/KmsbrV.png)

![master 分支合入后](https://gitee.com/michael_xiang/images/raw/master/uPic/M9PM7Q.png)

### no-ff 合并

不分叉的场景是否可以强制采用 `--no-ff` 方式合并呢？可以！

```shell
# master 回到合入前的状态
git reset --hard d2fa1ae
git merge feature --no-ff
```

![no-ff](https://gitee.com/michael_xiang/images/raw/master/uPic/LTzh9p.png)

这次命令行没有 `Fast-forward` 的提示了。

看一眼 master 分支图：

![no-ff](https://gitee.com/michael_xiang/images/raw/master/uPic/Sklh3v.png)

这个图和上面讲解 `no-ff` 命令时的示意图一致，果然会有新的 `commit` 生成。

### 分叉场景的合并

![分叉](https://gitee.com/michael_xiang/images/raw/master/uPic/X69y67.png)

上面的图展示了我们经常遇到的一个场景，分支创建之后，源分支也会有新的提交。这就是形成分叉了。

这时候如果我们进行合并呢？

```
git merge feautre
```

![分支图](https://gitee.com/michael_xiang/images/raw/master/uPic/g9NjTO.png)

可以看到，虽然默认会尝试 `fast-forward` 的方式进行合并，但是它分叉了，所以此时还是会采用 `no-ff` 的方式进行合并！有了新的 `commit` 生成了！

> 其实，fast-forward 方式对应的合并参数是 `--ff`

我们试试这个参数 `--ff-only`，顾名思义，就是强制只使用 `ff` 方式进行合并：

```
# 回到合并前
git reset --hard 3793081
git merge feature --ff-only
```

![合并终止](https://gitee.com/michael_xiang/images/raw/master/uPic/yIaYHq.png)

经过测试发现，当分叉时，因为无法使用 `ff` 方式合并，即使你强制指定使用该方式也不行，会提示终止！

以下附上 Git 官方文档中的解释，方便理解：

```shell
With --ff, when possible resolve the merge as a fast-forward (only update the branch pointer to match the merged branch; do not create a merge commit). When not possible (when the merged-in history is not a descendant of the current history), create a merge commit.
```

## rebase 操作

`rebase` 命令是一个常见但是大多数人掌握的不太好的一个命令。`rebase` 合并往往又被称为 「变基」，我称为 「基化」🤣。这里的「基」的理解很重要，理解了它，其实 `rebase` 命令你就掌握了。

我的描述可能并不准确，希望能够帮助你理解。这里的「基」就是一个「基点」、「起点」的意思。「变基」就是改变当前分支的起点。注意，是当前分支！

变基前：

![分叉](https://gitee.com/michael_xiang/images/raw/master/uPic/RQ39Rv.png)

变基后：

![变基后](https://gitee.com/michael_xiang/images/raw/master/uPic/HepjTM.png)

> git rebase 命令通常称为向前移植（`forward porting`）。

### 变基提交示例

我们接下来进行实际的测试，将代码库状态构造成分叉的状态，状态图如下：

![分叉初始状态](https://gitee.com/michael_xiang/images/raw/master/uPic/o9hGJa.png)

以 master 分支为基，对 feautre 分支进行变基：

```
git checout feature
git rebase master
```

以上两行命令，其实可以简写为：`git rebase master feature`

> 特性分支 feature 向前移植到了 master 分支。经常使用 git rebase 操作把本地开发分支移植到远端的 `origin/<branch>` 追踪分支上。也就是经常会说，「把你的补丁变基到 master 分支的头」

![变基后](https://gitee.com/michael_xiang/images/raw/master/uPic/8jMmax.png)

可以发现，是在 master 分支的最新节点后面多了 2 个提交记录（生成了新的提交记录，仅仅提交信息保持了一致），而这两个提交内容就是来自变基前  feature 分支，feature 分支的提交历史发生了改变。

观察上图还可以发现，变基后，改变的只是 feature 分支，基分支（master 分支）的 HEAD 指针依然在之前的 commit 处。这时候，已经满足 `fast-forward` 的条件了，`master` 分支执行快速合并，将 HEAD 指针指向刚刚最新合入的提交点：

```
git checkout master
git merge feature
```

![快速合并](https://gitee.com/michael_xiang/images/raw/master/uPic/BUiz44.png)

看一下 master 分支图，观察 HEAD 指针的位置：
![分支图](https://gitee.com/michael_xiang/images/raw/master/uPic/dgYPmX.png)

rebase 变基操作最适合的是本地分支和远端分支之间的合并。这样理解可能会更清晰一点。比如，远端仓库里有一个特性分支 feature，除了你开发之外，还有其他人往这个分支进行合入。当你每次准备提交到远端之前，其实可以尝试变基，这时候基分支就是远端的分支。

![与远端分支分叉](https://gitee.com/michael_xiang/images/raw/master/uPic/PDdv1I.png)

```
git fetch
git rebase origin/feature
```

![变基后](https://gitee.com/michael_xiang/images/raw/master/uPic/UBCOSo.png)

这时候可以看到，我们本地的提交以远端分支的最新提交为「基」，将差异提交重新进行了提交！远端分支的提交记录依然是一条直线~如果分叉的情况，不采用这种「变基操作」，就会有如下这种分支提交图：

![no-ff 合并](https://gitee.com/michael_xiang/images/raw/master/uPic/hkVfzT.png)

可以发现，因为分叉了，采用 `git pull` 时也没法 `fast-forward` 合并，只能采用 `no-ff` 方式合并，最后的提交历史就会向上图那样。会产生一个合并提交，同时，分支图也显得稍微杂乱了一点，因为 feature 分支不是一条直线了。但是，其实也有好处，可以实际的看出来合并的提交时怎么来的。该选择哪个，往往取决于团队的选择策略。

### Rebase 总结

`rebase` 命令其实关键在于理解「基」，`git rebase <基分支>`，就是将当前分支与基分支的差异提交获取到，然后将差异提交逐个提交到「基分支」，最后将当前分支的 HEAD 指针指向最新的提交点。「基分支」的 HEAD 位置是不变的。要想完成分支合并，完成变基之后，再进行分支间的合并等操作。

rebase 命令的用法也不止于此，计划后期会专门写一篇介绍她的文章。本文本来是计划介绍 merge 命令的，但是合并的方式中，其实也经常涉及变基操作之后的合并，因此，干脆就放一起比较好了，这样易于理解记忆。 

## 补充

- `git merge --abort` 当合并的过程中，由于冲突难解决，你想放弃合并，回到未合并之前的状态；
- `git log --graph --pretty=oneline --abbrev-commit` 可以方便查看提交图

## 一言

之前在 Git 这个专辑里介绍 cherry-pick 的文章，有个小伙伴给了如下的留言，挺高兴的，说明自己的总结分享的内容获得了肯定！

![留言](https://gitee.com/michael_xiang/images/raw/master/uPic/vhpyi8.png)

今天又肝了这篇文章，介绍了 Git 中的 merge 和 rebase 的基本概念和用法，同时，又自己手动绘制了图！俗话说，一图胜千言，但完整写完才发现，真是耗时啊……不过，总结的同时，我也加深了理解，有些概念也变得更加清晰了！希望，我的总结也能让其他人读懂~

之前我经常会开启文章的「赞赏」，但是发现收效甚微，很少有小伙伴会打赏。后来我就每次发文就关闭了这个选项。本文应该是 6 月份的最后一篇总结了，月末总结，就开启一次「赞赏」吧！期待小伙伴的支持与鼓励！

> 我将本文的参考文章也都注明了，他们也都很有阅读的价值。但由于微信外链的缘故，可以点击右下角的「阅读原文」浏览！

## 参考

- [StackoverFlow-What is the difference between `git merge` and `git merge --no-ff`?](https://stackoverflow.com/questions/9069061/what-is-the-difference-between-git-merge-and-git-merge-no-ff)
- [git-scm-git-merge](https://git-scm.com/docs/git-merge)
- [分支的合并](https://backlog.com/git-tutorial/cn/stepup/stepup1_4.html)
- [Gitlab-Fast-forward merge requests](https://docs.gitlab.com/ee/user/project/merge_requests/fast_forward_merge.html)
- [颜海镜-图解4种git合并分支方法](https://yanhaijing.com/git/2017/07/14/four-method-for-git-merge/)

----

> 生命不息，折腾不止！关注 「Coder 魔法院」，祝你 Niubilitiy ！🐂🍺

