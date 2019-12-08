---
title: Git基础 —— Github 使用技巧
date: 2019-11-14 21:21:24
tags: [ss,vps]
categories: ToolsDev
keywords:
---

## Git 基础学习系列

- [Git 基础 —— 安装 配置 别名 对象](https://michael728.github.io/2019/11/14/git-install-config/)
- [Git 基础 —— 常用命令](https://michael728.github.io/2019/11/14/git-useful-commands/)
- [Git 基础 —— 常见使用场景](https://michael728.github.io/2019/11/14/git-usage-examples/)
- [Git基础 —— Github 的使用](https://michael728.github.io/2019/11/14/git-github-experiance/)


## Github  的利用
[Github feature 官宣](https://github.com/features)，介绍了 Github 的主要特性。

### 详细演示下怎么给一个项目发起 Pull Request(PR)

- 第一步，找到你想发起 PR 的项目，点击右上角的 Fork 按钮，然后该项目就出现在了你自己账号的 Repository 里。
- 第二步，把fork的项目 clone 到本地，然后修改的 bug 也好，想要新增的功能也好，总之把自己做的代码改动开发完，接着，把自己做的代码改动 push 到 你自己的 GitHub 上去。
- 第三步，点击你 Fork 过来的项目主页的 Pull requests 页面，点击右上角的New pull request。

页面自动会比较该项目与原有项目的不同之处，最顶部声明了是源仓库的分支与你fork过来的分支的对比。同样的我写好标题和描述，然后我们点击中间的 Create pull request 按钮，至此我们就成功给该项目提交了一个 PR。

然后就等着项目原作者 review 你的代码，并且决定会不会接受你的 PR，如果接受，那么恭喜你，你已经是该项目的贡献者之一了。

## 发现好用的开源项目
GitHub 其中一个最重要的作用就是发现全世界最优秀的开源项目，你没事的时候刷刷微博、知乎，人家没事的时候刷刷 GitHub ，看看最近有哪些流行的项目，久而久之，这差距就越来越大，那么如何发现优秀的开源项目呢？

1. 关注一些活跃的大牛
2. Explore菜单下的Trending，看到最近的一些热门开源项目，很多人主动获取开源项目的最好的途径，可以选择“当天热门”，“一周之内热门”和“一月之内热门”来查看，并且，可以分语言来查看。
3. Search，按照Most Stars来筛选。

GitHub 的 Search 还有一些小技巧：
- 使用高级搜索的功能，搜索语法见 [Understanding the search syntax](https://help.github.com/articles/understanding-the-search-syntax/)；
- 使用 `in:readme` 可以扩大搜索的范围，否则直接输入输入搜索文字，只在仓库名、描述的内容中去匹配搜索；

搜索词示例：
```
git 最好 学习 资料 in:readme stars:>2000
# 搜仓库里带有 gitlab-ci.yml 的仓库，同时文件里有 after_script + stge:deploy 内容，根据代码内容搜索
'after_script'+'stage: deploy'  filename:.gitlab-ci.yml
```

除此之外，有些人如果习惯用 Google 进行搜索，那么想搜索 GitHub 上的结果，不妨前面加 GitHub 关键字就ok了，比如我在 google 里输入 GitHub android http ，每个关键字用空格隔开。

## 怎么选择团队适合的工作流

### 主干开发
![](https://ws2.sinaimg.cn/large/006tNc79ly1fzdcgk1dayj31au0ngk1q.jpg)
Facebook 采用主干开发模式。如果没有质量保证的话，commit  容易出现错误，要能快速迭代，才能采用主干开发模式。

### Git Flow
流程太过复杂，互联公司不太常见，和主干开发模式不同，它类似于特性分支模式。
![](https://ws1.sinaimg.cn/large/006tNc79ly1fzdh71wtcuj315f0u0h21.jpg)
Github Flow：
特性分支开发测试完毕，回合到主干，既可以立即发布了
![](https://ws2.sinaimg.cn/large/006tNc79ly1fzdh98wrzbj31cm0netis.jpg)
### Gitlab Flow 带生产分支
 master 分支用于持续集成的，特性分支会集成到 master，production 分支用于发布的。![](https://ws1.sinaimg.cn/large/006tNc79ly1fzdhaoty4hj314a0so7bk.jpg)
 Gitlab Flow 带环境分支：
![](https://ws2.sinaimg.cn/large/006tNc79ly1fzdhd1gnivj31840t0gum.jpg)
Gitlab Flow 带发布分支：
因为硬件有多个版本，为了适配这些硬件，同一个时间点，可能会针对硬件发布多个版本
![](https://ws4.sinaimg.cn/large/006tNc79ly1fzdhefrrshj315b0u0aiv.jpg)

## 如何挑选合适的分支集成策略
Github > Insights > Network 可以看到版本演进;

Settings 中，有一个 `Merge Button`，可以选择 MR 的方式：
![](https://ws4.sinaimg.cn/large/006tNc79ly1fzdhmkv7nfj31560iaad5.jpg)
Github 的合并策略：
- allow merge commits：合入主干时，会产生一个 merge commit；
- allow squash merging：相当于把你的提交会先合并成一个 commit，然后再提交，这样的话，你原来的特性分支没有变，主干分支多了一个 commit，主干也是线性演进；
- allow rebase merging：主干是一个线性演进，看着很舒服，实质上就是特性分支的一个个 cherry pick  到主干上，比如特性分支上有3个 commit，那么执行之后，主干也就产生3个 commit 了，原特性分支没有变化；
![](https://ws4.sinaimg.cn/large/006tNc79ly1fzdhzsv3llj30q50oiqex.jpg)
上面，仅仅是 Github 的 MR 策略，与 Gitlab 的策略也是有所区别的。

> 可以根据软件包发布时是从什么分支上发布的，分为主干开发和分支开发，从主干分支上出包发布，就叫主干开发，从特性分支上出包的，就叫分支开发。

## 启用 issue 跟踪需求和任务
仓库的 settings 页面，有启用 issue 的按钮，默认是勾选的，可以 `set up template`，可以选择预设的模板。模板文件是放在仓库的 .github 文件夹中，可以参考 [vue 项目的模板](https://github.com/vuejs/vue/tree/dev/.github)

## 如何用 project 管理 issue？
在仓库的 projects 页面可以创建 project，也是有 template 的。以后添加 issue 时，就可以选择对应的 project。

晨会过进度的时候，还是很方便直观的。后期可以通过项目看板帮我们有序的管理任务和推进。
![](https://ws3.sinaimg.cn/large/006tNc79ly1fzekk461k7j31do0gijte.jpg)

## 项目团队怎么实施 code review?
在仓库 settings branches 中添加规则，支持 `release*` 这样的规则，通配符，还可以指定 review 的其他规则，比如审核人要几个等。

通过 pull request 发起 review 的申请。

## 福利
GitHub 上影响力很大，同时又对你们很有用的项目：
- [free-programming-books]( https://github.com/EbookFoundation/free-programming-books):  这个项目整理了所有跟编程相关的免费书籍，而且全球多国语言版的都有，中文版的在这里 [free-programming-books-zh]( https://github.com/EbookFoundation/free-programming-books/blob/master/free-programm)
- [ob-my-zsh]( https://github.com/robbyrussell/oh-my-zsh):  俗话说，不会用 shell 的程序员不是真正的程序员。oh-my-zsh 毫无疑问就是目前最流行，最酷炫的 shell
- [zsh+on-my-zsh配置教程指南（程序员必备）](https://michael728.github.io/2018/03/11/tools-zsh-tutorial/)
- [awesome]( https://github.com/sindresorhus/awesome):  GitHub 上有各种 awesome 系列，简单来说就是这个系列搜罗整理了 GitHub 上各领域的资源大汇总，比如有 awesome-android, awesome-ios, awesome-java, awesome-python 等等等，就不截图了，你们自行去感受。
- [github-cheat-sheet]( https://github.com/tiimgreen/github-cheat-sheet/): GitHub 的使用有各种技巧，只不过基本的就够我们用了，但是如果你对 GitHub 超级感兴趣，想更多的了解 GitHub 的使用技巧，那么这个项目就刚好是你需要的，每个 GitHub 粉都应该知道这个项目。
- [LearningNotes](https://github.com/francistao/LearningNotes)：这是一份非常详细的面试资料，涉及 Android、Java、设计模式、算法等等等，你能想到的，你不能想到的基本都包含了，可以说是适应于任何准备面试的 Android 开发者，看完这个之后别说你还不知道怎么面试！

## 补充-Gitlab
- [DevOps-Tools](https://about.gitlab.com/devops-tools/)
- [DevOps-lifecycle](https://about.gitlab.com/stages-devops-lifecycle/)
- [Gitlab-ee/boards](https://gitlab.com/gitlab-org/gitlab-ee/boards/338779) Gitlab 的看板示例，本身企业版的开发讨论；
- [Gitlab 代码集成 流水线](https://gitlab.com/gitlab-org/gitlab-ee/pipelines) 保证集成的质量，要想跑 CI，需要在 CI/CD 中注册 Runner。