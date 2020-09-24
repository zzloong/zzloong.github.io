---
title: Mac 开发环境必备神器 —— Alfred
date: 2020-09-23 23:05:50
tags: [Mac, Tools]
categories: ToolsDev
keywords:
  - mac
  - app
  - tutorial
---

![](https://gitee.com/michael_xiang/images/raw/master/computer-mac.jpg)

## 前言

[alfred](https://www.alfredapp.com/) 这款软件称为「神器」真是当之无愧。今天专门总结一下，作为之前 [Mac 配置教程-开发篇](https://michael728.github.io/2019/04/14/tools-dev-mac/) 的补充。

## Alfred 自带命令

- 唤起快捷键设为：command双击
- 输入框：`lock`，锁屏
- `empty` 清空回收站，瞬间强迫症患者清爽了。
- `eject` 命令可以弹出磁盘、存储卡或者虚拟磁盘镜像，如 .dmg 挂载后的磁盘。
- `open + 文件名`或者`空格 + 文件名` 搜索文件，`Enter` 键直接打开文件，`→`+`Reveal in Finder` 可以在文件夹中打开文件；
- `find + 文件名` 直接在文件夹中打开文件；
- 输入 `in` 命令，以及待搜索的文本，列出磁盘中包含该文本的相关文件，可以快速定位文件，相当于简易的终端 find 命令。

## 快捷搜索 URL

```shell
百度- https://www.baidu.com/s?ie=utf-8&f=8&wd={query}
淘宝 -  https://s.taobao.com/search?q={query}
豆瓣电影：http://movie.douban.com/subject_search?search_text={query}&cat=1002
bilibili：http://search.bilibili.com/all?keyword={query}
百度：https://www.baidu.com/s?ie=utf-8&f=8&wd={query}
stackoverflow：http://www.stackoverflow.com/search?q={query}
githubUser：https://github.com/{query}
githubSearch：https://github.com/search?utf8=%E2%9C%93&q={query}
MDN：https://developer.mozilla.org/zh-CN/search?q={query}
```

参考：
- [掘金-Mac 提升开发效率的小工具](https://juejin.im/post/5b0e99436fb9a009e405dbb6)

## 剪贴板历史

有了 Alfred 的剪贴板增强，以上场景都不是问题，首先要在 Features ->; Clipboard 中打开剪贴板历史：

这里我把它们都设置为存储三个月，同时把打开剪贴板的默认快捷键为 `Command + Shift + V`。

通过 `Command + 数字键`，就可以把它粘贴出来

## 文本片段

有一些文本是需要反复输入的，比如你的邮箱、QQ号、地址、手机号码、某个网址等信息，亦或者聊天常用语句、表情等等，这些都可以通过 Alfred 来管理，节省输入时间。

![snippets](https://gitee.com/michael_xiang/images/raw/master/uPic/NVuLa8.png)

## 字典设置

输入`define`开头，然后输入查询的单词；

![define](https://gitee.com/michael_xiang/images/raw/master/uPic/C2pSvb.png)

## workflow

个人推荐的workflow:
- [github](https://github.com/gharlan/alfred-github-workflow)：使用之前，需要先`gh > login`。
- ip address：查看本机ip
- 快递查询：输入`kd+单号`
- [YoudaoDict](https://github.com/Rouwanzi/AlfredWorkflow_YoudaoTranslate)
- 人民币金额大写：`cny`然后输入数字即可
- [Hash](https://github.com/bigluck/alfred2-hash)：查看文件MD5值等
- [http_status_code](https://github.com/ilstar/http_status_code):显示 http 状态码含义
- [CodeVar](https://github.com/xudaolong/CodeVar)：
- [encode](https://github.com/willfarrell/alfred-encode-decode-workflow)
- [百度地图](https://github.com/wofeiwo/alfred-baidu-map)

我常用的存储在这儿了：[my-config-files/alfred-work-flow](https://github.com/Michael728/my-config-files/tree/master/alfred-work-flow)

下载源：
- [alfredworkflow](http://www.alfredworkflow.com/)
- [workflow-list](http://www.packal.org/workflow-list)
- [zenorocha/alfred-workflows](https://github.com/zenorocha/alfred-workflows)

## 自定义终端

```shell
on alfred_script(q)
    tell application "iTerm"
        set _length to count window
    if _length = 0 then
        create window with default profile
    end if
    set aa to (get miniaturized of current window)
    if aa then
        set miniaturized of current window to false
    end if
    set bb to (get visible of current window)
    if bb is false then
        set visible of current window to true
    end if
    set cc to frontmost
    if cc is false then
        activate
    end if
        (*if _length = 0 then*)
            set theResult to current tab of current window
        (*else
            set theResult to (create tab with default profile) of current window
        end if*)
        write session of theResult text q
end tell
end alfred_script
```

## 参考
- [总是在 Mac 「装机必备」看到的搜索利器 Alfred，究竟是怎么用的？| 新手问号](https://sspai.com/post/43973)
- [效率神器 Alfred workflow 插件推荐](https://hufangyun.com/2018/alfred-workflow-recommend/)
- [Alfred 效率神器全攻略](http://blog.surfacew.com/tool/2016/08/03/Alfred/)
- [Alfred神器使用手册](http://louiszhai.github.io/2018/05/31/alfred/#%E5%A6%82%E4%BD%95%E5%AE%89%E8%A3%85alfred)
- [Mac效率神器Alfred系列教程---文件搜索](https://www.jianshu.com/p/2ce1dd633f4f)