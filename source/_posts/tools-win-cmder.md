---
title: Windows 开发环境必备终端工具 Cmder
date: 2020-05-16 23:10:57
categories: ToolsDev
tags:
    - Tools
    - Win
    - Terminal
keywords:
  - 效率工具
---

![cmder](https://gitee.com/michael_xiang/images/raw/master/uPic/9bt2Mf.png)

## 概述

Windows 的命令终端确实不太好用，这也是很多人吐槽 Windows 的一个槽点。今天安利一款叫 Cmder(http://cmder.net/) 的神器，希望它能让你在 Windows 上的开发体验提升一点！Cmder 现在确实也成为了我工作中的一款必备软件。

<!-- more -->

Cmder 的优势主要有：

- 它提供了 `portable` 的版本，也就是移动版，一次配置之后，方便在其他环境再使用；
- 颜值比 Windows 默认的高，可以安装相关主题、设置背景图片等，自定义比较丰富；
- 支持一些常用命令的自定义（设置一些 `alias`）；
- 提供了一些 Linux 命令，比如 `ls`、`pwd`、`curl` 等；
- 支持多窗口，这对于开发确实方便；
- 操作方便，比如命令行的复制、粘贴等；

基于以上的原因，推荐还在 Windows 平台工作的小伙伴可以安装试用一下，不会后悔的！

## 安装

官网主页上提供下载，有两个版本 mini 与 full 版，full 版本自带了 msysgit 工具，推荐下载。

> 默认会跳转至 Github(https://github.com/cmderdev/cmder/releases/tag/v1.3.14)。国内下载速度比较慢，如果有需要，可以在公众号后台回复 `cmder` 即可获得百度云的下载链接。

![公众号](https://gitee.com/michael_xiang/images/raw/master/uPic/XiIO0t.png)

安装：

- 解压下载下来的压缩包至你一般安装软件的文件夹，比如 `C:\Program Files`
- 配置系统的环境变量，在 Path 中新增：`C:\Program Files\cmder`
- `Win+R` 输入 cmder 即可打开 cmder 客户端了，可以将快捷方式固定至任务栏；

## 配置

### 将 cmder 添加至右键菜单

添加 cmder 到右键菜单，需要先以管理员权限打开 Windows 自带 cmd，然后运行如下命令:

```shell
Cmder.exe /REGISTER ALL
```

经过上面的设置，在需要打开终端的文件夹下，你就可以在右键菜单中看到 `Cmder Here`，免去原来还需要在终端中输入路径进行跳转的繁琐步骤！

![cmder-here](https://gitee.com/michael_xiang/images/raw/master/uPic/cmder-here.png)

### 避免中文乱码

在使用 `ls` 等命令时，如果文件夹下有中文名乱码的问题，需要进行如下的设置解决（`settings->Startup->Environmen`）：

```shell
set LANG=zh_CN.UTF-8
```

![中文显示](https://gitee.com/michael_xiang/images/raw/master/uPic/中文显示.png)

> Win+Alt+P 是打开设置的快捷键，或者在顶部/底部右击点击 settings, 也可以进入设置页面

### 默认终端设置、默认目录设置

选择默认的终端，比如设置 `Cmder As Admin` 作为默认选项；

![默认终端](https://gitee.com/michael_xiang/images/raw/master/uPic/默认终端.png)

更改默认开启 cmder 时默认的目录，选择对应终端，增加`-new_console:d:D:` 即可，默认在 D 盘根目录：

![默认目录](https://gitee.com/michael_xiang/images/raw/master/uPic/默认目录.png)

### 别名（alias）设置

在 `Cmder` 终端，输入 `alias` 可以看到默认的一些别名设置。可以根据个人需要，自定义常用命令行操作的别名，提升开发效率！

依次进入 `Cmder 安装目录->config`，`user-aliases.cmd` 文件中定义了命令的别名，如下是我的一些示例，你也可以根据你的需要进行自定义：

```shell
2c=cd /d "D:\020-Code"
2dl=cd /d "C:\Users\xiang\Downloads"

ga=git add
gst=git status

ci=commit
co=checkout
```

- 快速切换至一些常用的目录；
- git 常用命令的缩写；

> 系统默认的有一个别名设置比较有用，`e.` 可以快速打开文件夹。

![explore](https://gitee.com/michael_xiang/images/raw/master/uPic/explore.gif)

### 背景的透明度、背景图片的设置

打开 `settings->Feature->Transparency`，可以进行终端透明度的设置，看起来效果比较酷炫。

背景图片的设置：

![背景图片](https://gitee.com/michael_xiang/images/raw/master/uPic/背景图片.png)

### 主题

主题，我使用的是 Dracula 吸血鬼主题（https://draculatheme.com/cmder）。 顺便安利这款主题，它也提供了 VSCode/IDEA/Pycharm 等一系列 IDE 软件对应的主题！

![安装主题](https://gitee.com/michael_xiang/images/raw/master/uPic/安装主题.png)

- `Win+Alt+P`
- 点击 `Import`
- 选择 `Dracula.xml`

### 设置分屏的快捷键

![分屏效果](https://gitee.com/michael_xiang/images/raw/master/uPic/分屏效果.png)

进入 `Settings->Keys&Macro`，搜索 `Split: Duplicate` 设置。我的分屏快捷键设置为：

- `ctrl+shift+→`：左右分屏
- `ctrl+shift+↓`：上下分屏

![分屏快捷](https://gitee.com/michael_xiang/images/raw/master/uPic/分屏快捷.png)

### 快速复制/粘贴

下面这两个功能很常用，比 Windows 终端的复制和粘贴好用太多：

- 鼠标左键选中需要复制的内容，即可将终端的内容快速复制至剪贴板；
- 鼠标右键即可将剪贴板的内容快速粘贴；

![鼠标](https://gitee.com/michael_xiang/images/raw/master/uPic/鼠标.png)

### Cmder 窗口放大/缩小快捷键

默认的快捷键是 `ctrl+~`，这个和 VSCode 中终端启动的快捷键冲突了，因此，我修改了 Cmder 的这个快捷键，改为了 `Alt+~`

![缩写-主题](https://gitee.com/michael_xiang/images/raw/master/uPic/缩写-主题.png)

## 快捷键

- Tab：路径的自动补全；
- Ctrl+T：建立新页签；
- Ctrl+W：关闭页签;
- Ctrl+Tab：切换页签;
- Ctrl+1：快速切换到第 1 个页签，`Ctrl+n` 快速切换到第 n 个页签；
- Ctr+R：历史命令搜索;
- Ctrl+滑鼠滚轮：字体的快速放大/缩写；
- Alt+F4：关闭所有页签
- Alt+enter：切换到全屏状态；
- Win+Alt+P：进入cmder设置菜单；

## 参考

- [Win下必备神器之Cmder](https://jeffjade.com/2016/01/13/2016-01-13-windows-software-cmder/)
- [简书-cmder 一个比cmd强n倍的神器](https://www.jianshu.com/p/7a706c0a3411)