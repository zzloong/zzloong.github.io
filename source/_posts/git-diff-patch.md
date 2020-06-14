---
title: Git 基本命令 -- 你用过 git diff 吗？补习一下吧
date: 2020-06-14 20:21:24
tags: [Git]
categories: ToolsDev
keywords:
---

![](https://raw.githubusercontent.com/Michael728/markdown_images/master/20200614235455.png)

## 前言

上周介绍过一篇 Git cherry-pick 的文章，阅读数比之前有明显的增加。本周再接再厉，总结了 Git 的 diff 相关的用法。经过自己的学习，弥补了一些知识盲区。通过本文总结一下吧，以便日后查阅，也希望对你也有所帮助。

## 工作区和版本库的概念

在介绍 diff 命令之前，我们先认识一下 Git 的工作目录。在一个 GIt 工作区文件夹下，有一个 `.git` 隐藏目录。它其实不算是工作区内容，它是 Git 的版本库。

![](https://raw.githubusercontent.com/Michael728/markdown_images/master/20200614214701.png)

当我们使用 `git add` 命令时，会将文件添加到暂存区（也称索引区）。需要使用 `git commit` 命令才能将修改提交到对象库中，这里的 「提交」对应的英文也就是 `commit`。

## git diff 命令的 4 种比较

### `git diff`

比较「暂存区」与「工作区」之间的差异。

![初始状态](https://raw.githubusercontent.com/Michael728/markdown_images/master/20200614185732.png)

当我们直接修改了工作区中的文件之后，在添加到暂存区之前，想要看看修改了那些内容，保证修改正确性。这时候运行 `git diff` 即可，将会显示暂存区与工作区文件的差异。例如下图就是一个示例：

![git diff](https://raw.githubusercontent.com/Michael728/markdown_images/master/20200614190104.png)

可以看到工作区的 `test.txt` 文件比暂存区 `test.txt` 文件新增了一行内容。

### `git diff commit`

比较「给定提交 ID」与「工作区」的差异。

有时候你需要将工作区的改动和历史中某个提交点的内容进行对比，这个命令就有用了。例如，我要将目前工作区的内容和当前分支的最新一次的提交进行比较，运行 `git diff 3f0c1b` 或者 `git diff HEAD`  即可：

![git diff](https://raw.githubusercontent.com/Michael728/markdown_images/master/20200614190104.png)

### `git diff --cached commit`

比较「暂存区」与「给定提交 ID」的差异。

因为已经将修改内容添加到暂存区了，这时候直接运行 `git diff` 就看不到差异了。因为我们手速快，已经将工作区修改的文件通过 `git add` 命令添加到暂存区了，这时候，又想要知道暂存区的变更和给定提交点的差异。只需要加上 `--cached` 选项即可。

例如，我现在将上面的修改已经添加到了暂存区，运行 `git diff --cached HEAD` 即可将暂存区内容与最新一次提交进行比较：

![git diff](https://raw.githubusercontent.com/Michael728/markdown_images/master/20200614190104.png)

> 如果省略 commit，那么，就是默认指 HEAD。

### `git diff commit1 commit2`

比较指定的两次提交 「commit1」与 「commit2」的差异。

运行 `git log --pretty=oneline --abbrev-commit` 命令看一下当前分支的提交记录，选择两个进行比较。

![git log](https://raw.githubusercontent.com/Michael728/markdown_images/master/20200614191509.png)

运行 `git diff 3f0c1 41312` 比较这两个 commit 的差异：

![git diff](https://raw.githubusercontent.com/Michael728/markdown_images/master/20200614190104.png)

好奇宝宝附体，咱们颠倒一下 commit 之间的顺序，看看差异效果：

![git diff](https://raw.githubusercontent.com/Michael728/markdown_images/master/20200614192305.png)

可以看到，比较对象的先后顺序决定了 diff 的差异结果的「增删」。上面几个命令的的描述，我其实都有注意顺序的表达。按照该顺序显示的效果，就是「后者」-「前者」的差异。这里需要你自己意会一下啦。可能我的总结并不是很精准。

### 比较有用的选项

- `--stat` 显示有多少行发生变化，简洁的展示差异；

### 限制路径

- 如果只要显示 `src` 文件夹下的差异，指定文件夹名即可， `git diff --stat HEAD~ HEAD src`
- 如果质押显示 `test.txt` 文件的差异，指定文件即可， `git diff HEAD~ HEAD src/test.txt`

## 补丁 patch

补丁名是自定义的，通常带有 `patch` 命名，这样命名比较通俗易懂。

前面介绍的 4 中比较差异的场景，我们可以通过比较差异的命令加上 `> patch` 这样的方式导出补丁文件。这里的 `patch` 就是自定义的补丁名。

我们来进行一个示例。使用 `git diff HEAD~ HEAD > patch` 导出上一次提交和这一次提交差异的补丁。

![](https://raw.githubusercontent.com/Michael728/markdown_images/master/20200614220952.png)

运行上述命令之后，会生成一个叫 `patch` 补丁文件。现在来看看差异文件到底表示的什么含义吧：

- `-` 表示第 1  个文件
- `+` 表示第  2  个文件
- `@@ -1 +1,2@@` 表示比较的区块，第 1 个文件的第  1 行，第 2 个文件的第 1 行起的连续 2 行
- `+new line in work dir` 新增的内容

总结起来就是表示当前分支的倒数第 2 个提交与最后 1 个提交之间的差异是，最后一个提交在 `test.txt` 的第 2 行新增了一行。

怎么用这个补丁呢？示例：

```shell
# 切换至倒数第 2 个提交
git reset --hard HEAD~
# 应用一下补丁
git apply patch
```

这时候刚刚差异的内容就会在相应的文件夹进行修改了，但是并不会生成提交。

> 补丁文件可以发给其他人，只需执行 git apply patch 就可以实现打补丁的效果喽。

在打补丁之前，可以先检验补丁能否应用，执行 `git apply --check patch` 即可。如果没有任何输出，那么表示可以顺利打补丁。

## IDEA 中的 diff 使用

![](https://raw.githubusercontent.com/Michael728/markdown_images/master/20200614232221.png)

IDEA 的右键菜单 Git 中提供了一些 Compare 的命令：

- `Compare with the Same Repository version` 与 Git 仓库的相同版本进行对比
- `Compare with` 选择当前分支的两个提交进行对比
- `Compare with Branch` 选择指定的分支进行对比

## 一言

本周在园区进行了 5 KM 的跑步，因为是和同事一起跑的，速度也被他带上来了，竟然跑进了 30 min。想起来上一任老大当时和我的聊天，「如果不知道前进的方向的话，不妨从周围同事身上学习那些优秀的习惯。」

![](https://raw.githubusercontent.com/Michael728/markdown_images/master/20200614233948.png)

本周还发生了一件让我震惊的事，进公司的第一任老大竟然短短一周内突然「走了」，前同事告诉我之后，真是感觉人生无常啊，太遗憾了……希望大家在搬砖的同时，别忘记身体健康才是最重要的。珍惜当下，peace~



## 参考

- [卖逗搞 IT —— 聊聊 git diff 命令](https://mp.weixin.qq.com/s/18VKdS6OduBA7eYEU1vYrQ)