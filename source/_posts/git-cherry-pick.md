---
title: Git 的 cherry-pick 命令还没用过？快来看看它的厉害！
date: 2019-11-14 20:21:24
tags: [Git,vps]
categories: ToolsDev
keywords:
---

## 前言

最近一周在对项目的代码做一些构建工程的整改，一些通用的代码往往经常需要在代码库中的各个分支进行提交。没接触过 `cherry-pick` 功能时，往往需要你反复切换分支，在各个分支上进行相关内容的修改。这样的操作不仅繁琐，而且修改的地方一多，极易出错！

![cherry](https://gitee.com/michael_xiang/images/raw/master/uPic/BDuC2R.png)

## cherry-pick 的理解

Git 提供了 `cherry-pick` 的命令可以很好的解决上面场景的问题，高效、快捷！同样的适配，会该用法之后，比其他同事能够提前不少时间完成整改！

我们代码库中的一个个 `commit` 可以看做一个个 `cherry`。同一个代码库中的 `commit-id` 往往是唯一的，当你在分支 B 上也需要在分支 A 上的提交内容时，就可以将它们 `cherry-pick` 过来！

## 语法

```shell
git cherry-pick [--edit] [-n] [-m parent-number] [-s] [-x] [--ff]
		  [-S[<keyid>]] <commit>…
git cherry-pick (--continue | --skip | --abort | --quit)
```

稍安勿躁，上面是该命令的语法，下面我们将通过如下的这个仓库进行示例演示：

![分支状态](https://gitee.com/michael_xiang/images/raw/master/uPic/TgNQya.png)

- 代码库有三个分支：`master`、`feature-a`、`feature-b`
- `master` 分支处于 `d3113d5`
- `feature-a`基于 `master` 分支创建后，进行了三次提交，`commit-id` 分别是：
  - `b09a488c011cb954e0de0df7e4a03622daeefc8d`
  - `9517fc12ecfb81ecc6379dc7cc62368e239c4542`
  - `6b95b589152a6e54a6a7c945d9de97eb3ecc147a`
- `feature-b` 特性分支b，当前处于 `967181`，这里是为了测试的时候方便恢复

## 从 feature-a 分支选择 2 个提交合入 feature-b

首先解决一个疑问，可能有同学会说，为何不采用 merge 进行分支的合并呢？

因为，并不是每个场景都是可以进行分支的合并的。比如，这两个分支就是不同的发布分支，一个是版本 1.0.0 的分支，一个是版本 2.0.0 的分支，这两个版本分支是需要独立保存的。这时候将它们进行合并就不合适。但是这时候有一个 bug 是它俩都有的，需要修复。使用 cherry-pick 就可以仅仅将你在一个分支上修复的内容筛选出来，提交到另一个分支上！

看🌰：我现在需要将我在`feature-a` 分支上的两次提交 `b09a488` 和 `6b95b5` 改动的内容也在 `feature-b` 上进行修改提交：

```shell
# 切换到 feature-b 分支
git checkout feature-b
# 挑选 commit
git cherry-pick 6b95b5 b09a488
```

这时候，如果没有冲突的话， `git log` 就可以看到 `feature-b` 分支上会多出  2 次新的提交。

> 注意点：虽然表面上看似是将那两次提交拿过来再用一遍，但其实 Git 只是拿到修改生成了新的提交，因此，这里会看到这 2 个新的提交，commit-id 和我们挑选commit-id 并不一致。

## 从 feature-a 分支选择连续的提交合入到 feature-b

如果需要挑选 feature-a 上那 3 次连续的 `commit` 进行合入，那么，有种简单的写法进行这种操作：

```shell
# 切换到 feature-b 分支
git checkout feature-b
# 语法：git cherry-pick <start-commit-id>...<end-commit-id>
git cherry-pick d3113d5...b09a488c01
```

细心的读者应该发现了，为何 `start-commit-id` 选用的是 `6b95b58` 的上一次提交呢？因为上面这种写法，是「左开右闭」的！下面介绍另外一个写法，它是「左闭右闭」的：

```shell
# 切换到 feature-b 分支
git checkout feature-b
# 语法：git cherry-pick <start-commit-id>^...<end-commit-id>
git cherry-pick 6b95b58^...b09a488c01
```

![example](https://gitee.com/michael_xiang/images/raw/master/uPic/okBOuD.jpg)

> 如果要合并的多个提交修改的文件有先后依赖关系，建议要按照这几个提交的时间顺序进行 cherry-pick，否则可能会有冲突等问题

## 如何不自动提交

`-n/--no-commit` 选项可以在进行 `cherry-pick` 时不进行自动提交，而且，经过测试发现，当你选择多个 `commit-id` 时，它会将这些 `commit-id` 对应的修改在当前分支生效而不进行提交，实现了多个 `commit-id` 修改进行合并的效果。

看🌰：

```shell
# 切换到 feature-b 分支
git checkout feature-b
# 在 feature-b 分支，恢复到初始状态
git reset --hard 967181
# 选择 feature-a 上的连续三个提交修改的内容，但是不进行自动提交
git cherry-pick 6b95b58^...b09a488c01 -n
# 查看一下状态
git status
```

> 注意点：会将多个 commit-id 的修改合并为一个修改，只是修改，并不会生成新的提交，你需要手动进行提交，提交信息可以自定义

## 其他有用的选项

- `-e/--edit`：进行 `cherry-pick` 时，会在进行新的提交之前，重新编辑提交的信息
- `x`：在记录提交时，会在原始提交消息后添加一行`cherry picked from commit …`，以表明此更改是从哪个提交中挑选出来的。 这仅适用于没有冲突的 `cherry-pick`
- `-s/--signoff`：在提交信息的末尾追加一行操作者的签名，表示是谁进行了这个操作

## 樱桃摘的时候有冲突怎么办

其实和 `rebase` 命令时有冲突的解决步骤差不多：

- 解决有冲突的文件
- `git add -u` 标记有冲突的文件已经解决好冲突了
- `git cherry-pick --ontinue`

除此以外，如果你过程中不想 `cherry-pick`了，只需执行：`git cherry-pick --abort`

## 再来一段

难道介绍到这里就结束了吗？No~

麦哥再介绍一下我们日常使用的宇宙最强 IDEA 编辑器中集成的 `cherry-pick` 功能吧！

![idea-cherry-pick](https://gitee.com/michael_xiang/images/raw/master/uPic/idea-cherry-pick.gif)

通过这个动图中的信息可以看到，虽然挑选的 `commit` 在分支 `feature-b` 上也进行了提交，提交信息没变，但是其实 `commit-id` 是新的了，证明是新的提交！

## 总结

Github 作为知名的程序员交友网站，Git 就是其后面的基石。作为程序员，我们应该对一些基本的 Git 命令有所了解，这在日常开发中将大大提高开发效率！

## 一言

最近一周，我是亲眼看到一些同事由于对 Git 不熟悉，白白浪费了一些时间在提交入库的操作上。后期，我也会陆续在总结一些 Git 的基础知识，争取用通俗易懂的 demo 将它们讲明白，希望感兴趣的小伙伴持续关注起来吧~

> 生命不息，折腾不止！关注 「Coder 魔法院」，祝你 Niubilitiy ！🐂🍺

## 参考

- https://git-scm.com/docs/git-cherry-pick
- https://juejin.im/post/5925a2d9a22b9d0058b0fd9b
- http://www.ruanyifeng.com/blog/2020/04/git-cherry-pick.html