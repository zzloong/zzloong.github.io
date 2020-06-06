---
title: Git 基础 —— 常用命令
date: 2019-11-14 19:21:24
tags: [Git]
categories: ToolsDev
keywords:
---

## Git 基础学习系列

- [Git 基础 —— 安装 配置 别名 对象](https://michael728.github.io/2019/11/14/git-install-config/)
- [Git 基础 —— 常用命令](https://michael728.github.io/2019/11/14/git-useful-commands/)
- [Git 基础 —— 常见使用场景](https://michael728.github.io/2019/11/14/git-usage-examples/)
- [Git 基础 —— Github 的使用](https://michael728.github.io/2019/11/14/git-github-experiance/)


## git init
创建 Git 本地仓库

### 远端无仓库，本地无仓库，本地新建一个仓库
```
git init git_learning
```

### 远端有仓库，本地无仓库，拉取远端仓库到本地
```
git clone git@github.com:Michael728/michael-git.git
cd michael-git
# 提交一个 readme 文件
touch README.md
git add README.md
git commit -m "add README"
git push -u origin master
```

### 远端有空仓库，本地已有项目文件，关联远端仓库
查看我们当前项目有哪些远程仓库可以执行如下命令：
```
git remote -v
```

如果发现没有关联远端仓库，可以这么做：
```
cd micahel-git
git init #  如果本地已经是一个 Git 仓库，这行就跳过
git remote add origin git@github.com:Michael728/michael-git.git # 添加一个远端主机，并命名为 origin
git push -u origin --all # --all 表示 push all branches，-u 选项指定了一个默认主机
git push -u origin --tags # --tags All refs under refs/tags are pushed
```

将本地的`master`分支推送到`origin`主机，同时指定`origin`为默认主机，后面就可以不要再指定远端主机名 `origin` 了，直接使用`git push`。

远端主机名可以定义为其他，比如 `github`。通过 `git remote add` 命令，一个仓库其实可以与多个远端仓库发生关联的，这时候只要远端主机名取不一样的即可区别。为什么要给远程仓库取名字？因为我们可能一个项目有多个远程仓库，比如，Github一个，比如公司一个，这样的话，提交的时候可以提交到不同的远程仓库就需要指定不同的仓库名字了。

参考：
- [git push 的 -u 参数具体适合含义？](https://www.zhihu.com/question/20019419)
- [Git远程操作详解](http://www.ruanyifeng.com/blog/2014/06/git_remote.html)

## git clone
下载一个远程仓库：
```
git clone [-b br_name] <git@github.com:Michael728/michael-git.git> [本地仓库名]
```

克隆的时候，可以指定下载远端的分支、自定义本地仓库的名字。如果不加分支名参数，`git clone` 命令会默认自动设置本地 `master` 分支跟踪克隆的远程仓库的 `master` 分支（其实是仓库的默认分支，大部分仓库默认分支是 master）。同时，默认远端主机设置别名为 `origin`。

## git mv
文件重命名：
```
git mv <old filename> <new filename>
```

## git branch
- `git branch -r` 只显示远端分支，
- `git branch -a` 显示本地分支和远程分支

### 新建分支
新建 develop 分支，并切换到 develop 分支：
```
git branch develop
git checkout develop
# 新建并切换分支
git checkout -b develop 
```

### 本地分支推送到远端仓库
本地分支推送到远程服务器时，远程分支自动创建，推送本地分支到远程：
```
git push --set-upstream <remote_host_name> <local_branch_name>:<remote_branch_name>
```

- `<remote_host_name>`：远程 Git 服务器名称，一般为`origin`
- `<local_branch_name>`：本地分支名称
- `<remote_branch_name>`：远程分支名称
- `--set-upstream`参数用来关联本地分支和远程分支

一般情况下，本地分支和远程分支名称相同，所以可简化为：

```
git push --set-upstream <remote_host_name> <branch_name>
```

参考：
- [Git创建远程分支](https://majing.io/questions/718)
- [阮一峰--Git远程操作详解](http://www.ruanyifeng.com/blog/2014/06/git_remote.html)

### 查看本地分支：
```
git branch # 查看本地分支
git branch -r # 查看远端分支
git branh -av # 查看所有分支，信息详细点
```

### 删除分支：
删除本地分支：
```
git branch -d develop
git branch -D develop # 强制删除
```

删除远程分支：
```
git push origin :<remote_branch_name>
# 和如下命令等同
git push origin --delete <remote_branch_name>
```

## git add

多个场景会用到这个命令:
- 可以用它开始跟踪新文件
- 把已跟踪的文件放到暂存区
- 还能用于合并时把有冲突的文件标记为已解决状态，这个是在解决冲突时会用到的功能

常用命令：
- `git add -u`：将文件的修改、文件的删除，添加到暂存区，用` -u`有个好处，避免把工作区没准备好的新文件直接加到暂存区了，用的较多；
- `git add .`：将文件的修改，文件的新建，添加到暂存区，慎用；
- `git add -A/--all`：将文件的修改，文件的删除，文件的新建，添加到暂存区，慎用；

`git add -A`相对于`git add -u`命令的优点 ： 可以提交所有被删除、被替换、被修改和新增的文件到数据暂存区，而`git add -u `只能操作跟踪过的文件。

## git diff
### 比较工作区和暂存区的差异
将工作区和暂存区所有文件进行比较：
```
git diff 
```

只对某些文件和暂存区进行比较：
```
git diff -- README.md [filename ...]
```

### 比较暂存区和 HEAD 之间的差异
```
git diff --cached
# 或者
git diff --staged
```

### 比较的是工作区和HEAD之间的差异
```
git diff HEAD
```

### 比较两个分支的差异
```
git diff master temp
```

只关心这两个分支中某个文件的差异：
```
git diff master temp -- index.html
```

其实，分支名就是一个指针，就是一种引用，可以直接使用 commit id 比较：
```
$ git diff 622a8 7e7a -- index.html
```

注意了：`git diff A B` 比较的结果可以看做是 `B-A` 的差集，调换 `A B` 顺序，正负号会有变化的。

## git reset

### 暂存区文件的恢复
暂存区全部文件恢复成和 HEAD 一样：
```
git reset HEAD
```

- reset 命令不加 --hard，则暂存区的内容恢复成HEAD对应的内容，工作区的变更继续保留；
- 如果加了 --hard，则不管工作区还是暂存区，内容都变回HEAD对应的内容，危险的命令，会让你在工作区的修改丢失；

`git reset` 有三个参数：
- `--soft` 这个只是把 HEAD 指向的 commit 恢复到你指定的 commit，暂存区 工作区不变
- `--hard` 这个是 把 HEAD， 暂存区， 工作区 都修改为 你指定的 commit 的时候的文件状态
- `--mixed` 这个是不加时候的默认参数，把 HEAD，暂存区 修改为 你指定的 commit 的时候的文件状态，工作区保持不变


怎样取消暂存区部分文件的修改？
```
git reset HEAD style.css
```

### 将工作区和暂存区保持一致
有时候修改了文件，已经保存到暂存区，之后又在工作区进行了修改，此时，发现工作的效果不如暂存区的效果好，想要将工作区和暂存区保持一致。

```
git checkout -- <file>...
```

其实，`git status` 都有友好的提示的：
![](https://ws3.sinaimg.cn/large/006tNc79ly1fzc741ckucj30yc0c8771.jpg)
- 如果想要变更工作区的内容，那么要想到和 `checkout` 命令相关；
- 如果想要变更暂存区的内容，那么要想到和`reset` 命令相关；

### 消除最近的几次提交

丢弃一些 commit，直接HEAD 指向了你指定的某个 commit，同时，暂存区和工作区也恢复到哪个 commit 时的状态：
```
git reset --hard <commitid>
```

有些 commit 会丢失，是条危险的命令，要慎用。但是当你明确了你的需求，需要将工作区暂存区提交记录明确恢复到某个 commit 状态时，可以执行这个命令。

## git commit

「提交」操作。当你前面采用 `add` 命令将文件添加到暂存区跟踪后，需要通过`commit`将暂存区的内容提交到当前分支：
```
git commit -m "test"
```

当一些已被追踪的文件修改后，常常需要`git add file`，然后再`git commit -m "xxxx"`，其实这两个步骤可以合二为一：
```
git commit -am "test"
```

这么写个人觉得挺好，可以有效避免有些懒人`git add .`的方式，将一切文件都添加到了暂存区，导致最后多余文件提交入库。

### amend
修改最近一次 commit 的 message：
```
git commit --amend
```

修改完 message 信息之后，保存退出即可

`git commit --amend`命令本质上是用新的 commit 应该是替代了上一次的提交，不只是修改 message。比如上一次提交时有几个文件没有 add 以及 commit，可以重新进行 add 之后再 `commit --amend` 提交。但这次提交之后，在分支的 git log 中，不会增加一次新的 commit（因为被替换了嘛），看着效果相当于在父 commit 的基础上进行的修改。

### 修改历史提交的 message 信息：
![](https://ws1.sinaimg.cn/mw690/6d9475f6ly1fzbz3iinr0j20pg0hwdhz.jpg)
如图，想要修改 be4c 这次提交的 message 信息，那么可以使用 `git rebase` 命令，因为 `be4c` 这次提交会被新的提交替换掉，所以，「变基」操作的「基」要选择它的父提交，`85807`：
```
git rebase -i 8580
```

- `-i` 会进入交互模式，有一系列指令操作对应的 commit，不要用 `pick` 命令，而是使用 `reword` 命令操作 `add ref` 那次 commit，然后保存，就进入修改 message 的窗口，修改完再保存，最后就会 OK 了。

```
$ git rebase -i 8580
[detached HEAD b2b5486] Add ref project
 Date: Mon Jan 14 23:56:14 2019 +0800
 1 file changed, 1 insertion(+), 1 deletion(-)
Successfully rebased and updated refs/heads/master.
```
 
 `git log -3` 查看最近的3次提交，变为这样了，会发现，倒数第二次的 message 信息修改 OK 了，最新一次的 message 虽然没变，但其实，`commit id` 都发生了变化，「替换」的概念要记得。
![](https://ws2.sinaimg.cn/large/006tNc79ly1fzbzb6tgbxj30wu0hqdin.jpg)
git rebase 工作的过程中，就是用了「分离头指针」。rebase 意味着基于新 base 的 commit 来变更部分 commits。它处理的时候，把 HEAD 指向base 的 commit，此时如果该 commit 没有对应branch，就处于分离头指针的状态，然后重新一个一个生成新的 commit，当rebase 创建完最后一个 commit 后，结束分离头状态，Git 让变完基的分支名指向 HEAD

PS：对于团队中公用的分支，例如发布分支等，禁用 rebase，因为这样会破坏历史的 commit  信息的，将来要溯源、基于构建历史拉取补丁分支等就会带来极大不便。

### 连续多个 commit  合并
![](https://ws3.sinaimg.cn/large/006tNc79ly1fzbzitp4n0j30q407cdha.jpg)
目前 commit 还在本地，没有 push 到团队分支上，想要将网页相关的 commit 合并成一个，就是从图中 `55a9` 开始的6个 commit 合成一个：
```
git rebase -i 7e7a
```
![](https://ws1.sinaimg.cn/large/006tNc79ly1fzbzrbwrd3j30yg0n643u.jpg)
```
$ git rebase -i 7e7a
[detached HEAD 1c102e6] Create a complete web page
 Date: Mon Jan 14 23:46:26 2019 +0800
 5 files changed, 20 insertions(+)
 create mode 100644 index.html
 create mode 100644 js/a.js
 create mode 100644 style.css
 create mode 100644 styles/a.css
 create mode 100755 大嘴猴-头像-logo.jpg
Successfully rebased and updated refs/heads/master.
```
![](https://ws4.sinaimg.cn/large/006tNc79ly1fzbztd0xd7j30ru046mxz.jpg)

变基过程中有可能会遇到冲突的，只要解决冲突即可，解决冲突的时候，只需要先修改有冲突的文件的内容，然后执行 `git add <file_with_conflict>`即可，不要再 `git commit`，否则多出来 commit 的，然后接着 `git base --continue`即可，参考[简书-git rebase解决合并冲突](https://www.jianshu.com/p/4c30eb30323a)

### 历史中不连续的 commit  合并
将历史中和 readme 相关的 commit 合并，就是下图中52af 7e7a 03be 这三个 commit。
![](https://ws4.sinaimg.cn/large/006tNc79ly1fzbztd0xd7j30ru046mxz.jpg)

```
git rebase -i 52af # 因为52af 是首个，没有父亲，因此需要补充一下
```
![](https://ws1.sinaimg.cn/large/006tNc79ly1fzc05snpqgj30se07q40c.jpg)
```
$ git status
interactive rebase in progress; onto 52af14b
Last command done (1 command done):
   pick 52af14b
Next commands to do (3 remaining commands):
   squash 7e7a518 modify readme
   squash 03bef1e Modify readme to README file
  (use "git rebase --edit-todo" to view and edit)
You are currently rebasing branch 'master' on '52af14b'.
  (all conflicts fixed: run "git rebase --continue")

nothing to commit, working tree clean

# michael @ Michael-MBP in ~/Code/Git-Geek/git_learning on git:52af14b o [17:08:05]
$ git rebase --continue
[detached HEAD fe08624] Add readme.md
 Author: Michael728 <michael@163.com>
 Date: Mon Jan 14 23:35:44 2019 +0800
 1 file changed, 1 insertion(+)
 create mode 100644 README.md
Successfully rebased and updated refs/heads/master.
```
最终整理成了两个 commit：
![](https://ws2.sinaimg.cn/large/006tNc79ly1fzc0bfti81j30p802a74n.jpg)
有意思的发现，有两个 commit 是没有祖先的：
![](https://ws4.sinaimg.cn/large/006tNc79ly1fzc0d7cbf2j30j004oaag.jpg)
如果将 temp 分支、js01 tag 删掉，Git 会清理掉下面那个树。

## git pull
git pull命令的作用是，取回远程主机某个分支的更新，再与本地的指定分支合并。它的完整格式稍稍有点复杂：
```
git pull <远程主机名> <远程分支名>:<本地分支名>
```

比如，取回 origin 主机的 next 分支，与本地的master分支合并：
```
git pull origin next:master
```

如果远程分支是与当前分支合并，则冒号后面的部分可以省略：
```
git pull origin next
# 等价于下面两个命令
git fetch origin
git merge origin/next
```

参考：
- [阮一峰-Git远程操作详解](http://www.ruanyifeng.com/blog/2014/06/git_remote.html)

## git push
`git push`命令用于将本地分支的更新，推送到远程主机：
```
$ git push <远程主机名> <本地分支名>:<远程分支名>
```

如果省略远程分支名，则表示将本地分支推送与之存在"追踪关系"的远程分支（通常两者同名），如果该远程分支不存在，则会被新建。
```
$ git push origin master
```

上面命令表示，将本地的 master 分支推送到 origin 主机的 master 分支。如果后者不存在，则会被新建。

不管是否存在对应的远程分支，将本地的所有分支都推送到远程主机，这时需要使用 `--all` 选项：
```
$ git push --all origin
```

`git push` 不会推送标签（`tag`），除非使用 `--tags` 选项：
```
$ git push origin --tags
```

可能会遇到 `rejected` 的 error，因为远端包含了一些本地是没有的变更，比如，创建远端仓库时，在远端仓库的 master 分支上新建了文件，比如 License，而本地是没有这次提交的：
![](https://ws1.sinaimg.cn/large/006tNc79ly1fzcu3d1mg1j311608o40c.jpg)
把远端拉取下来：
```
git fetch github master
```

语法：
```
git fetch <远端主机名> <远端分支名>
```

`non-fast-forward` 表示，你本地 master 分支的演进不是基于远端 master 分支进行的，二者是割裂的，经过 fetch 之后，通过 `gitk --all` 可以看到：
![](https://ws1.sinaimg.cn/mw690/6d9475f6ly1fzcu7h3riij20nw062q47.jpg)
解决这个问题，可以通过 rebase 或者 merge 的方式解决，现在先采用 merge 的方式：

```
# 切到本地 master 分支
$ git merge github/master
fatal: refusing to merge unrelated histories
$ git merge github/master --allow-unrelated-histories
Merge made by the 'recursive' strategy.
 LICENSE | 21 +++++++++++++++++++++
 1 file changed, 21 insertions(+)
 create mode 100644 LICENSE
```

上面 fetch 和 merge 方式，和如下 pull 命令等效：
```
$ git pull github master --allow-unrelated-histories
```

现在合并之后，分支演进如下：
![](https://ws1.sinaimg.cn/mw690/6d9475f6ly1fzcundxvl5j20qg0jc418.jpg)
可以看到merge 这种方式新生成的 commit 有两个父亲。

现在重新启动将本地的 master 分支 push 到远端：
```
git push github master
```

## git rm
从 Git 中移除某个文件，就必须从已跟踪的文件清单中删除（从暂存区域移除文件），然后提交。可以使用 `git rm` 命令完成此项工作，并连带从工作目录中删除指定的文件，以后这个文件就不会出现在 Git 库中了。

当我们先把某文件从 Git 库中删除（亦即从暂存区移除），但仍然希望保留在当前工作目录中。比如当你忘记在`.gitignore`文件中将一些文件忽略，但是却不小心把大的日志文件添加到暂存区域时，这一做法很有用：

```
# --cached 将 README 文件从暂存区移除，但是工作区目录仍然保留
git rm --cached README
```

## git checkout
基于某分支创建新分支：
```
git checkout -b <new_branch_name> <base_branch_name>
```

这里的 `base_branch_name` 是指创建分支时的「基」。

- 当省略时，就是基于当前分支创建；
 ```
git checkout -b <new_branch_name>
```

- 当这个「基」是远端分支名时，就实现了在本地基于远端分支创建分支。

```
git branch -av # 查看本地+远程分支列表
git checkout -b dev  origin/dev
```

还可以可以在`checkout`命令中使用 Hash 值作为起点创建分支：
```
git checkout -b <name_of_branch> <commit id>
```

除了有“切换”的意思，`checkout`还有一个撤销的作用。

举个例子，假设我们在一个分支开发一个小功能，刚写完一半，这时候需求变了，而且是大变化，之前写的代码完全用不了，好在你刚写，甚至都没有 `git add` 进暂存区，这个时候很简单的一个操作就直接把原文件还原：
```
git checkout <filename>
```

参考：[在git中checkout历史版本](https://www.cnblogs.com/crazyacking/p/5620635.html)

## git log
查看版本演变历史：
```
git log --pretty=oneline # 检查提交日志，都在一行：<commit id> <message>
git log --oneline # 与上面命令等价
```

查看某人的提交：
```
git log --author=michael
```

- 一个常用的选项是`-p`，用来显示每次提交的内容差异;
- 可以加上`-2`或者`-n2`来仅显示最近两次提交：

```
git log -n2 # 查看最近的2次提交
git log -p -2
git log -n1 --format=format:%h # 查看当前分支最新的 commit id 缩略值
```

列出最近两周内的提交：
```
git log --since=2.weeks
```

图形化查看分支演变：
```
# 加了 --all 表示查看所有分支的历史，否则只能看到当前分支的演变历史
git log --all --graph
# 只查看指定分支的演变历史，比如 temp 分支，此时就不能使用参数 --all
git log --oneline --graph temp
```

- [git log命令全解析，打log还能这么随心所欲！](http://www.cnblogs.com/bellkosmos/p/5923439.html)
- [git-scm 2.3 Git 基础 - 查看提交历史](https://git-scm.com/book/zh/v2/Git-%E5%9F%BA%E7%A1%80-%E6%9F%A5%E7%9C%8B%E6%8F%90%E4%BA%A4%E5%8E%86%E5%8F%B2)

## 远程仓库的使用
### 查看远程仓库
```
git remote -v
```

如果想查看远程仓库更多的信息，可以使用`git remote show <remote-name>`命令。

远程仓库的移除与重命名：
```
git remote rename  <old-remote-name> <new-remote-name>
git remote rename pb paul
```

如果因为一些原因要移除一个远程仓库，可以使用`git remote rm <remote-name>`。

### 添加一个新的远程 Git 仓库，同时指定一个可以轻松引用的简写：
```
git remote add <remote_host_name> <url>
```

这里的`remote_host_name`常常取名为`origin`。所以，常见的`origin`其实是一个你 Git 仓库跟踪的远程仓库的简写。

### 拉取远端仓库有但你本地没有的信息：

```
git fetch <remote_host_name>
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
git checkout -b <new_br> <tag_name>
```

参考：
- [The Junior Git](https://thoamsy.github.io/blogs/git/)
- [6 Git 基础 - 打标签](https://git-scm.com/book/zh/v1/Git-%E5%9F%BA%E7%A1%80-%E6%89%93%E6%A0%87%E7%AD%BE)

## 最后
- [Git使用教程笔记](https://michael728.github.io/2018/11/24/git-advance/)
- [Pro Git 中文](https://git-scm.com/book/zh/v2)
- [Git常用命令备忘](https://foofish.net/git-command.html)
- [掘金-今年下半年，中日合拍的《Git游记》即将正式开机，我将...（上集）](https://juejin.im/post/5c22056551882518fc5fe294)
- [git术语解释staging，index，cache](http://sexywp.com/git-staging-area-index-cache.htm?f=http://blogread.cn/)
- [pcottle/learnGitBranching](https://github.com/pcottle/learnGitBranching) 分支演示网站