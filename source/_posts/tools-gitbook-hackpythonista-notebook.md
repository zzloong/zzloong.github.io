---
title: 使用GitBook+Github编写文档书籍
date: 2018-09-08 18:49:51
tags: [Gitbook, Tools]
categories: ToolsDev
---

{%cq%}Books are to mankind what memory is to the individuanl. 书之于人类，犹如记忆于之个人。{%endcq%}

最近看到了[python-web-guide](https://python-web-guide.readthedocs.io/zh/latest/index.html#)这样的文档以及[explore python](https://funhacks.net/explore-python/)，觉得知识体系还是用这种方式全面组织比较系统一点，因此，放假在家也搞一下gitbook吧！

欢迎访问我的第一本[GitBook-HackPythonista](https://michael728.gitbooks.io/hackpythonista/content/)，目前内容有待丰富，囧

鉴于已有相当多的文档介绍了Git，本文仅做简要笔记，同时记录一下遇到的坑。

<!-- more -->

## gitbook基本使用

- 全局安装 gitbook

```
sudo npm install gitbook-cli -g
```

安装完成，执行`gitbook -V`查看版本信息；

- gitbook常用命令

```
gitbook init //初始化目录文件
gitbook help //列出gitbook所有的命令
gitbook --help //输出gitbook-cli的帮助信息
gitbook build //生成静态网页
gitbook serve //生成静态网页并运行服务器
gitbook build --gitbook=2.0.1 //生成时指定gitbook的版本, 本地没有会先下载
gitbook ls //列出本地所有的gitbook版本
gitbook ls-remote //列出远程可用的gitbook版本
gitbook fetch 标签/版本号 //安装对应的gitbook版本
gitbook update //更新到gitbook的最新版本
gitbook uninstall 2.0.1 //卸载对应的gitbook版本
gitbook build --log=debug //指定log的级别
gitbook builid --debug //输出错误信息
```

- 与github集成，参考文章[chengweiBlog-GitHub 集成](http://www.chengweiyang.cn/gitbook/gitbook.com/config/github.html)
- 安装gitbook插键，参考文章[chengweiBlog-book.json](http://www.chengweiyang.cn/gitbook/customize/book.json.html)
- 在书籍根目录创建`book.json`:
```
{
  "gitbook": "3.2.3",
  "language": "zh",
  "description": "Michael翔技术栈NoteBook",
  "plugins": [
    "disqus@0.1.0",
    "anchor-navigation-ex",
    "-lunr",
    "-search",
    "search-pro-kui"
  ],
  "pluginsConfig": {
    "disqus": {
      "shortName": "HackPythonista"
    },
    "anchor-navigation-ex": {
      "isShowTocTitleIcon": true,
      "tocLevel1Icon": "fa fa-hand-o-right",
      "tocLevel2Icon": "fa fa-hand-o-right",
      "tocLevel3Icon": "fa fa-hand-o-right"
    }
  }
}


```

## gitbook插键

### disqus插键

按照前面参考文章安装`disqus`插键时，使用命令`npm install gitbook-plugin-disqus`会默认安装disqus的最新版本，然后`hexo serve`时，就会报错：

![](https://ws1.sinaimg.cn/large/6d9475f6gy1fv29yxr7wvj20fv09tgnf.jpg)

```
info: loading plugin "disqus"... ERROR

Error: GitBook doesn't satisfy the requirements of this plugin: >=4.0.0-alpha
```

1. 想要尝试升级gitbook的版本，通过`gitbook ls-remote`查看可以安装的版本，然后利用`gitbook fetch 4.0.0-alpha.6`安装，尽管我用` gitbook uninstall 3.2.3`卸载了旧版本，安装之后不明白为何还是使用的3.2.3的稳定版本，后来用尝试创建[软连接解决](https://huataihuang.gitbooks.io/cloud-atlas/develop/doc/gitbook/gitbook_discussions.html)依然存在问题，没继续升级gitbook版本；
2. 查看[disqus-plugin文档](https://www.npmjs.com/package/gitbook-cli)没有解决，查看[disqus项目issue](https://github.com/GitbookIO/plugin-disqus/issues/10)，有人给出了答案，“1.0.1”版本需要gitbook的版本">=4.0.0-alpha"，可以通过`"plugins": ["disqus@0.1.0"]`的方式，指定安装旧版本的disqus插键解决这个问题。

后来看到[disqus插键官网文档](https://plugins.gitbook.com/plugin/disqus)，发现明明有写这些的，自己没注意到，**文档多重要**：

![](https://ws1.sinaimg.cn/large/6d9475f6gy1fv2cmzp2uoj20co0aemxc.jpg)

> 我们也可以通过@符号来指定插件的版本号，如："plugins1@0.1.1", 这个特性在使用一个旧版本的gitbook时是非常有用的


## PS

npm安装指定版本：
```
npm install jquery@3.0.0
```
如果加上`-g`参数，表示安装到全局目录中

## 参考

- [gitbook的使用和常用插件](https://dancon.gitbooks.io/git-books/content/in_action/gitbook%E4%BD%BF%E7%94%A8%E6%8C%87%E5%8D%97.html)
- [npm 安装指定版本](https://blog.csdn.net/xuaner8786/article/details/81630445)
- [用github写开源书籍](https://www.leolan.top/index.php/posts/30.html)
- [zhangjikai-更多插件介绍](https://gitbook.zhangjikai.com/plugins.html#disqus)
- [gitbook-plugin-anchor-navigation-ex](https://github.com/zq99299/gitbook-plugin-anchor-navigation-ex/blob/master/doc/config.md)