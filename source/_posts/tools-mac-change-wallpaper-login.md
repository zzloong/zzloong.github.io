---
title: Mac 10.15 Catalina 开机登录壁纸图片修改
date: 2020-03-04 21:36:50
tags: [Mac, Tools, 壁纸]
categories: ToolsDaily
keywords:
  - mac
  - 壁纸
---

![zyu2MS](https://gitee.com/michael_xiang/images/raw/master/uPic/zyu2MS.png)

## 前言

为了修改 macOS 10.15 Catalina 系统开机时的登录界面的壁纸，折腾了好一会儿才搞定，没想到这么麻烦，记录一贴，方便有需求的小伙伴借鉴 

<!-- more -->

## 步骤

- 关闭 SIP：重启 Mac，在开机时立马按住 `Command+R` 键，直到看到苹果的logo-》选择语言-》选择账户-》下一步进入macOS实用工具的界面，然后点击上方菜单栏的「实用工具」下的「终端」，输入下面的命令：

```shell
csrutil disable
```

- 将准备好的壁纸下载到「下载」目录下，当然，你也可以放到其他目录下，但是本文以该目录下的壁纸文件作为操作路径。将壁纸重命名为 `Catalina.heic`
- 打开访达，在菜单栏上点击「前往 - 前往文件夹」，或者使用快捷键 `Command+Shift+G` 打开「前往文件夹」，路径地址 `/System/Library/Desktop Pictures`。默认的登录时的壁纸就位于该文件夹下。使用命令行操作比较简单：

```shell
# 这个命令是把分区 mount 成可写模式。这个命令在系统重启后失效
sudo mount -uw /
# 备份原来的默认壁纸
sudo cp /System/Library/Desktop\ Pictures/Catalina.heic ~/Downloads/Catalina.old.heic
# 将下载后重命名好的壁纸替换掉默认路径下的壁纸
sudo cp -f ~/Downloads/Catalina.heic /System/Library/Desktop\ Pictures/Catalina.heic
# 执行了下面这行关键操作之后，开机壁纸才最最终生效！查了很多资料都没提到这个
diskutil apfs updatePreboot /
```

注意点：

- 现在网上很多 Mojave 系统的教程，路径说的是 `/Library/Desktop Pictures/`，其实已经不适用了。
- `diskutil apfs updatePreboot /` 这个命令执行之后，开机壁纸才最终成功修改成功，很多旧教程中没有提到
- 修改完毕之后，建议按照步骤一的方式开启 SIP，命令行是：`csrutil enable`


## 参考

- [CSDN——MMac OS 10.15 修改登录壁纸](https://blog.csdn.net/SummerCloudXT/article/details/103753626?depth_1-utm_source=distribute.pc_relevant.none-task&utm_source=distribute.pc_relevant.none-task)
- [CSDN——MAC：Read-only file system问题](https://blog.csdn.net/Mr_BJL/article/details/102780325?depth_1-utm_source=distribute.pc_relevant.none-task&utm_source=distribute.pc_relevant.none-task)
- [少数派——macOS 开启或关闭 SIP](https://sspai.com/post/55066)