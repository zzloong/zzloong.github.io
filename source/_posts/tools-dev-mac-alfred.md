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

> 需要说明的是，如果你发现我介绍的功能无法使用，则代表需要花钱购买它的 Powerpack。麦哥是从淘宝购买的正版永久激活码搞定的，百十块大洋~如果你囊中羞涩，也有一些网站提供了破解版本。关注公众号回复 Alfred 会提供对应的下载网站（需要注意的是：破解版软件往往会有安全风险。）

## General 通用设置

- `Startup`：勾选上，这样就能在登录系统时自动启动 Alfred；
- `Alfred Hotkey`： 启动 Alfred 的快捷键，我设置的是双击 Command 键；

![通用设置](https://gitee.com/michael_xiang/images/raw/master/uPic/CzLoxh.png)

## Alfred 自带命令

- 唤起快捷键设为：command 双击
- 输入框：`lock`，锁屏
- `empty` 清空回收站，瞬间强迫症患者清爽了。
- `eject` 命令可以弹出磁盘、存储卡或者虚拟磁盘镜像，如 .dmg 挂载后的磁盘。
- `open + 文件名`或者`空格 + 文件名` 搜索文件，`Enter` 键直接打开文件，`→`+`Reveal in Finder` 可以在文件夹中打开文件；
- `find + 文件名` 直接在文件夹中打开文件；
- 输入 `in` 命令，以及待搜索的文本，列出磁盘中包含该文本的相关文件，可以快速定位文件，相当于简易的终端 find 命令。
- 输入搜索内容后，Enter 按键是直接打开文件，`Command + Enter`表示打开文件所在文件夹；
- `/`：输入栏输入 `/`，会跳转到系统根目录；
- `~`：输入 `~`，会进入当前用户的用户目录；


## Features 特性功能

Features 功能很多，这里仅介绍目前个人发现的常用设置。

### Default Results 默认结果
d
这个菜单项主要是设置 Alfred 搜索结果时，默认从哪些目录搜索出结果。

- `Essentials`：搜索系统偏好设置和联系人信息；
- `Extras`：指定搜索对象，比如文件夹、文档、图片等。如果格式不全，可以点击 `Advanced` 按钮自定义；
- `Search Scope`：指定搜索范围，哪些路径可以搜；

![Default Results](https://gitee.com/michael_xiang/images/raw/master/uPic/HbqAlq.png)

### Web Search 文件搜索

该功能非常方便，快捷键启动 Alfred 之后，输入指定网站的关键字，然后输入要搜索的内容，按下 Enter 键之后自动跳转到浏览器对应网站的搜搜结果，一气呵成的搜索体验！

![alfred-web-search.gif](https://i.loli.net/2020/09/26/9ZCbHyKspqD3OXk.gif)

只需要点击右下角的 `Add Custom Search` 按钮，即可添加你经常搜索的网站的快捷方式：

![网站搜索](https://gitee.com/michael_xiang/images/raw/master/uPic/ad01wm.png)

下面是我自定义的一些设置，方便搜索：
- 百度：`https://www.baidu.com/s?ie=utf-8&f=8&wd={query}`
- 淘宝：`https://s.taobao.com/search?q={query}`
- 豆瓣电影：`http://movie.douban.com/subject_search?search_text={query}&cat=1002`
- B 站：`http://search.bilibili.com/all?keyword={query}`
- 知乎：`https://www.zhihu.com/search?type=content&q={query}`
- DuckDuckGo：`https://duckduckgo.com/?q={query}`
- Stackoverflow：`http://www.stackoverflow.com/search?q={query}`
- Github User：`https://github.com/{query}`
- Github Search：`https://github.com/search?utf8=%E2%9C%93&q={query}`
- MDN：`https://developer.mozilla.org/zh-CN/search?q={query}`

## Web Bookmarks

又是一个非常赞的功能！虽然我浏览器中已经安装了插键可以快速搜索我的书签，但是 Alfred 的作用就是，当浏览器没有打开、你正在做其他工作时想要快速打开你浏览器中的某个书签，这时候你只需要快速启动 Alfred->搜索书签的关键字->Enter 打开该书签。又是一气呵成的操作！

![alfred-bookmarks-search.gif](https://i.loli.net/2020/09/26/rZecT87wD6Nm1Hq.gif)

我设置了一个关键字 `bm`，寓意 `bookmarks`，用来表示我目的是搜索书签：

![bm](https://gitee.com/michael_xiang/images/raw/master/uPic/33s1ZC.png)

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

### workflow 是什么

![workflow](https://gitee.com/michael_xiang/images/raw/master/uPic/TYaQGU.png)

上面截图是 Alfred 官网对 Workflow 的一个描述，简单描述就是，workflow 是一个类似工作流的功能，它可以根据你预设的流程进行一系列的操作来帮你实现一个功能。



### 日用推荐

- 人民币金额大写：`cny`然后输入数字即可
- 快递查询：输入`kd+单号`
- [YoudaoDict](https://github.com/Rouwanzi/AlfredWorkflow_YoudaoTranslate)
- [百度地图](https://github.com/wofeiwo/alfred-baidu-map)

### 开发推荐

- ip address：查看本机ip
- [Hash](https://github.com/bigluck/alfred2-hash)：查看文件MD5值等
- [http_status_code](https://github.com/ilstar/http_status_code):显示 http 状态码含义
- [CodeVar](https://github.com/xudaolong/CodeVar)：
- [encode](https://github.com/willfarrell/alfred-encode-decode-workflow)

我常用的存储在这儿了：[my-config-files/alfred-work-flow](https://github.com/Michael728/my-config-files/tree/master/alfred-work-flow)

下载源：
- [Alfred官宣/workflows](https://www.alfredapp.com/workflows/)
- [alfredworkflow](http://www.alfredworkflow.com/)
- [workflow-list](http://www.packal.org/workflow-list)
- [zenorocha/alfred-workflows](https://github.com/zenorocha/alfred-workflows)
- [github/awesome-alfred-workflows](https://github.com/alfred-workflows/awesome-alfred-workflows)

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
- [掘金-Mac 提升开发效率的小工具](https://juejin.im/post/5b0e99436fb9a009e405dbb6)
- [iHTCboy/程序员的macOS系列：高效Alfred进阶](https://ihtcboy.com/2020/02/09/2020-02-09_%E7%A8%8B%E5%BA%8F%E5%91%98%E7%9A%84macOS%E7%B3%BB%E5%88%97%EF%BC%9A%E9%AB%98%E6%95%88Alfred%E8%BF%9B%E9%98%B6/)