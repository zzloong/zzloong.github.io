---
title: Manjaro 安装体验小结
date: 2019-08-03 12:09:28
tags:
- Linux
- Manjaro
categories: Linux
keywords:
- Manjaro
- Linux
- KDE
---

![Manjaro](https://b2.bmp.ovh/imgs/2019/08/51f98679d299b9c0.png)

## Manjaro 简介

[Manjaro](https://www.manjaro.cn/category/about-manjaro-linux) 是一款基于 Arch Linux、对用户友好的 Linux 发行版。在 Linux 社区，Arch Linux 的确是一个异常快速、强大、轻量级的发行版，它提供最新的、最全的软件。然而，Arch Linux 面向高级用户，普遍认为，缺乏技术专长或者没有耐心的人是无法玩转 Arch Linux的。

<!-- more -->

Manjaro 由奥地利、法国和德国的爱好者共同开发，提供了 Arch Linux 操作系统的所有优点，同时注重用户友好性和可用性。 Manjaro 提供32位和64位版本，适合新手以及经验丰富的 Linux 用户。

Manjaro 与 Arch 有许多相同的功能，包括：

- “滚动发行”开发模式，可提供最新的系统，而无需安装新版本
- 可用 AUR

然而，Manjaro 拥有自己的一些额外的功能，包括：

- 简化、用户友好的安装过程
- 自动检测计算机的硬件（例如显卡）
- 为系统自动安装必要的软件（例如显卡驱动程序）
- 它自己的专用软件仓库，以确保提供完全测试过的稳定的软件包
- 轻松安装和使用多个内核。

补充

[Arch Linux（或 Arch /ˈɑːrtʃ/)）](https://wiki.archlinux.org/index.php/Arch_Linux_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87) 是一款基于 x86-64 架构的 Linux发行版。系统主要由自由和开源软件组成，支持社区参与。系统设计以 KISS 原则（保持简单和愚蠢）为总体指导原则，注重代码正确、优雅和极简主义，期待用户能够愿意去理解系统的操作。Arch Linux 系统安装、删除和更新软件的软件包管理器叫做 pacman。

AUR 的全称是 `Arch User Repository`，是 Arch Linux/Manjaro 用户的社区驱动存储库，创建 AUR 的目的是使共享社区包的过程更容易和有条理。使用它可以在 Arch Linux/Manjaro 系统中安装和更新软件包。这个软件仓库的软件包是相当丰富，可以查看这个网站的统计[Repository statistics](https://repology.org/repositories/statistics/total)，它的软件列表可以在[Archlinux AUR](https://aur.archlinux.org/packages/)查看

![AUR](https://b2.bmp.ovh/imgs/2019/08/ebecf2400fef188c.png)

## 安装 Manjaro

双系统基本知识如下：

- [[折腾日记]win10 ,ubuntu双系统安装避坑指南](https://segmentfault.com/a/1190000014523888)
- [Windows 下安装 Ubuntu 双系统(更新)](https://www.cnblogs.com/Duane/p/6776302.html)
- [rEFInd](https://sourceforge.net/projects/refind/)

总结注意点：

- 要是遇到启动不了的问题，可以用老毛桃制作个U盘修复引导即可，所以事先建议搞一个;
- 制作 Manjaro U盘启动盘一开始使用的是 UltraISO，后来换成了 [Rufus](https://rufus.ie/)，就 OK 了;
- 安装引导时要选择创建的 `/boot` 分区， 此外，我是双硬盘，一开始按照单硬盘的方法，怎么也启动不了，后来需要在安装 Windows 系统的 SSD 盘上划出了 600MB 空间用来挂载 `/boot` 分区并安装引导；

[官网](https://www.manjaro.cn/153)系统镜像下载，推荐从 [USTC中科大镜像下载](https://mirrors.ustc.edu.cn/manjaro-cd/)几个主要版本：

- Xfce是一个用于类UNIX操作系统的轻量级桌面环境。它的目标是快速和系统资源低耗，同时仍然保持视觉上的吸引力和对用户友好的特性。所以，家里另外一台低配置的旧笔记本，我安装了这个版本。
- KDE是一个功能丰富多样的桌面环境，提供几种不同风格的菜单来访问应用程序。还有一个优秀的内置界面，可以方便地访问、下载、安装新的主题、小部件等。 虽然在用户友好度上做的非常好，但KDE也是相当消耗系统资源的，跟XFCE比较起来，启动程序、使用桌面环境都明显偏慢。运行 Manjaro 的 64 位 KDE 桌面使用大约需要 550MB 的内存。我自己的就笔记本配置还可以，就安装了这个版本，确实很易用！
- GNOME桌面环境是作为GNU项目的一部分来开发的，它旨在简单易用，并且完全可用。

我从另外一块机械硬盘上划分了 100GB 空间用来安装，分区参考：

|大小   |  挂载点   |   用途|
|-----  |----   -|------|
|40G    | `/`   |用于存放系统相当于win10的C盘|
|8GG    | `/swap`   |一般设为电脑内存大小或2倍|
|600MB  |`/boot`    |引导分区    |
|所有剩余的空间|`/home`|用户存储数据用|

## 源

配置中国的 `mirrors`，在 终端 执行下面的命令从官方的源列表中对中国源进行测速和设置：

```shell
sudo pacman-mirrors -g  # 排列源，可不执行
sudo pacman-mirrors -c China -m rank # 更改源，在跳出的对话框里选择想要的源
```

为 Manjaro 增加中文社区的源来加速安装软件，在 `/etc/pacman.conf` 中添加 `archlinuxcn` 源，末尾加上：

```shell
[archlinuxcn]
SigLevel = Optional TrustedOnly
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch
```

安装 `archlinuxcn-keyring` 包以导入 `GPG key`，否则的话 key 验证失败会无法安装：

```shell
sudo pacman -S archlinuxcn-keyring
```

同步并更新系统：

```shell
sudo pacman -Syyu
```

## 输入法

fcitx 是 Free Chinese Input Toy for X 的缩写，国内也常称作小企鹅输入法，是一款 Linux 下的中文输入法:

```shell
sudo pacman -S fcitx-googlepinyin
sudo pacman -S fcitx-im # 选择全部安装
sudo pacman -S fcitx-configtool # 安装图形化配置工具
sudo pacman -S fcitx-skin-material
```

解决中文输入法无法切换问题: 添加文件 `~/.xprofile`：

```shell
export GTK_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS="@im=fcitx"
```

> 输入法需要重启生效

## pacman

```shell
sudo pacman -S 软件名　# 安装
sudo pacman -R 软件名　# 删除单个软件包，保留其全部已经安装的依赖关系
sudo pacman -Rs 软件名 # 除指定软件包，及其所有没有被其他已安装软件包使用的依赖关系
sudo pacman -Ss 软件名  # 查找软件
sudo pacman -Sc # 清空并且下载新数据
sudo pacman -Syu　# 升级所有软件包
sudo pacman -Qs # 搜索已安装的包
```

## yay

Yay 是用 Go 编写的 Arch Linux AUR 包管理工具。具体可以查看 [Arch Wiki](https://wiki.archlinux.org/index.php/AUR_helpers)

{% note warning %}
注意：很多教程比较老了，yaourt 目前已经停止维护，用户可以考虑迁移到 aurman 或 yay，
{% endnote %}

安装 yay：

```shell
sudo pacman -S yay
```

配置 yay 的 aur 源为[清华源 AUR 镜像](https://mirror.tuna.tsinghua.edu.cn/help/AUR/)：

```shell
yay --aururl "https://aur.tuna.tsinghua.edu.cn" --save
```

修改的配置文件位于 `~/.config/yay/config.json` ，还可通过以下命令查看修改过的配置:

```shell
yay -P -g
```

yay 的常用命令：

```shell
yay -S package # 从 AUR 安装软件包
yay -Rns package # 删除包
yay -Syu # 升级所有已安装的包
yay -Ps # 打印系统统计信息
yay -Qi package # 检查安装的版本
```

yay 安装命令不需要加 `sudo`。

## Git

```shell
git config --global user.name "Michael728"
git config --global user.email "649168982@qq.com"
ssh-keygen -t rst -C "649168982@qq.com"
```

## zsh

```shell
sudo pacman -S zsh # 安装zsh
echo $SHELL # 查看大概年前 shell
chsh -s /bin/zsh # 修改默认shell，这个是修改当前用户的终端，如果要修改 root 账户，需要切换到 root用户
wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh
sudo pacman -S autojump
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

> 需要重启生效

具体的 zsh 配置教程，可以阅读之前的总结：

- [zsh+on-my-zsh配置教程指南（程序员必备）](https://michael728.github.io/2018/03/11/tools-zsh-tutorial/)

## ss

```shell
sudo pacman -S shadowsocks-qt5

```

![ss](https://b2.bmp.ovh/imgs/2019/08/597e3675620adb9c.png)

上面是安装了一个 ss 的客户端，编辑添加好帐号之后，还需要设置一下端口转发设置之后，浏览器访问才 OK：

```shell
sudo pacman -S privoxy # 安装代理转发，用于将 socks5 代理转换为 http 代理
sudo bash -c 'echo "forward-socks5 / 127.0.0.1:1080 ." >> /etc/privoxy/config'
git config --global http.proxy 'socks5://127.0.0.1:1080'
git config --global https.proxy 'socks5://127.0.0.1:1080'
sudo systemctl start privoxy.service
```

接着，你还需要去设置网络代理：系统设置-》网络-》设置-》代理，`socks代理` 那里进行配置 `127.0.0.1:1080`。

{% note warning %}
很多网上的教程都说需要再安装一个 `switchyomega` 插件，事实上，最新版本的 `Chrome` 已经不支持直接拖动 `crx` 文件安装插件了。
{% endnote %}

终端代理安装 `proxychain-ng`，`sudo pacman -S proxychains-ng` 安装，配置：

```shell
sudo vim /etc/proxychains.conf # 添加如下内容
socks5 127.0.0.1 1080
```

使用时在需要代理的命令前加 `proxychains4` 就可以了，例如：`proxychains4 ping www.google.com`

当然，端口转发软件还有 `polipo`，一个[教程](https://www.cnblogs.com/demonxian3/p/9259912.html)介绍的， `sudo vim /etc/polipo/config`：

```shell
socksParentProxy = localhost:1080
proxyPort = 8192
```

然后重启：

```shell
systemctl start polipo
```

还有一篇教程[iamlightsmile——人生苦短，我用Manjaro！](http://www.iamlightsmile.com/articles/%E4%BA%BA%E7%94%9F%E8%8B%A6%E7%9F%AD%EF%BC%8C%E6%88%91%E7%94%A8Manjaro%EF%BC%81/)提到了 pac 代理：

```shell
sudo pip install genpac
genpac --proxy="SOCKS5 127.0.0.1:1080" --gfwlist-proxy="SOCKS5 127.0.0.1:1080" -o autoproxy.pac --gfwlist-url="https://raw.githubusercontent.com/gfwlist/gfwlist/master/gfwlist.txt" # 生成 pac 文件
```

在设置—>网络设置—>代理设置中选择自动代理，URL 填写生成的 PAC 文件地址，file://文件路径/文件名(可以直接把文件拖到URL栏)。

`electron-ssr` 这款也是 ss 软件，就是没有支持的加密算法，就没用，这是这款软件的开发者的博客 [记录在开发electron-ssr过程中遇到的问题](https://erguotou.me/develop-electron-ssr.html)。

```shell
yay -S electron-ssr
```

## 安装中文字体

为啥会突出一下要安装中欧给你问字体呢，因为我使用过程发现系统里的中文字变成了一个个小白方框的格子，安装好中文字体并重启后，就显示正常了：

```shell
sudo pacman -S wqy-zenhei
sudo pacman -S wqy-bitmapfont
sudo pacman -S wqy-microhei
sudo pacman -S ttf-wps-fonts
sudo pacman -S adobe-source-han-sans-cn-fonts
sudo pacman -S adobe-source-han-serif-cn-fonts
```

## 常用软件

```shell
# deepin 系的软件
sudo pacman -S deepin-picker # 深度取色器
sudo pacman -S deepin-screen-recorder # 录屏软件，可以录制 Gif 或者 MP4 格式
sudo pacman -S deepin-screenshot # 深度截图
sudo pacman -S deepin-system-monitor # 系统状态监控
yay -s deepin-wine-wechat
yay -S deepin-wine-tim
yay -S deepin-wine-baidupan
yay -S deepin.com.thunderspeed

# 开发软件
sudo pacman -S jdk8-openjdk
sudo pacman -S make
sudo pacman -S cmake
sudo pacman -S clang
sudo pacman -S nodejs
sudo pacman -S npm
sudo pacman -S goland
sudo pacman -S vim
sudo pacman -S maven
sudo pacman -S pycharm-professional # Python IDE
sudo pacman -S intellij-idea-ultimate-edition # JAVA IDE
sudo pacman -S goland # Go IDE
sudo pacman -S visual-studio-code-bin # vscode
sudo pacman -S qtcreator # 一款QT开发软件
sudo pacman -S postman-bin
sudo pacman -S insomnia # REST模拟工具
sudo pacman -S gitkraken # GIT管理工具
sudo pacman -S wireshark-qt # 抓包
sudo pacman -S zeal
sudo pacman -S gitkraken # Git 管理工具

# 办公软件
sudo pacman -S google-chrome
sudo pacman -S foxitreader # pdf 阅读
sudo pacman -S bookworm # 电子书阅读
sudo pacman -S unrar unzip p7zip
sudo pacman -S goldendict # 翻译、取词
sudo pacman -S wps-office
yay -S typora # markdown 编辑
yay -S electron-ssr # 缺少我需要的加密算法
yay -S xmind

# 设计
sudo pacman -S pencil # 免费开源界面原型图绘制工具

# 娱乐软件
sudo pacman -S netease-cloud-music

# 下载软件
sudo pacman -S aria2
sudo pacman -S filezilla  # FTP/SFTP

# 图形
sudo pacman -S gimp # 修图

# 系统工具
sudo pacman -S albert #类似Mac Spotlight，另外一款https://cerebroapp.com/

# 终端
sudo pacman -S screenfetch # 终端打印出你的系统信息，screenfetch -A 'Arch Linux'
sudo pacman -S htop
sudo pacman -S bat
sudo pacman -S yakuake # 堪称 KDE 下的终端神器，KDE 已经自带，F12 可以唤醒
sudo pacman -S net-tools # 这样可以使用 ifconfig 和 netstat
yay -S tldr
yay -S tig # 命令行下的 git 历史查看工具
yay -S tree
yay -S ncdu # 命令行下的磁盘分析器，支持Vim操作
yay -S mosh # 一款速度更快的 ssh 工具，网络不稳定时使用有奇效
```

### chrome

以代理模式启动 Chrome:

```shell
google-chrome-stable --proxy-server=socks5://127.0.0.1:1080
```

### wireshark-qt

Wireshark 是一款免费开源的包分析器，可用于网络排错、网络分析、软件和通讯协议开发以及教学等。然而不幸的是官方网站上仅提供了 Windows 和 MacOS 的版本。不过不用担心，你依然可以使用 pacman 直接从 Arch 的 community 软件源里直接安装 wireshark-qt 。wireshark-qt 就是用 qt 做成的 wireshark 前端界面，它依赖于终端版的 wireshark-cli 。

这里提示一点： 通常直接安装完后启动 wireshark 会提示 `/usr/bin/dumpcap` 无权限，有些用户会因此选择在命令行里 `sudo wireshark` 打开 wireshark，这其实很不安全。正确做法是运行命令 `gpasswd -a username wireshark` 然后重新启动，详情可以参见 Arch Wiki 。

## 系统设置

### Dolphin

Dolphin 是 KDE 下默认的文件管理器。

- 在系统设置-》桌面行为-》工作空间-》点击行为，勾选”双击打开文件和文件夹“；
- 在菜单中搜索”常规“-》确认，勾选”将文件或文件夹移至回收站“；

### 默认程序

Win 键打开菜单搜索”默认程序”，可以修改浏览器等默认程序；

更多一些使用技巧，可以查看这里[撸Linux——Manjaro](https://www.lulinux.com/archives/category/manjaro-linux)

## 效果


## 参考

- [标点符——Manjaro Linux的安装体验](https://www.biaodianfu.com/install-manjaro-linux.html#comment-161818)
- [rovo98——Manjaro linux 安装与配置](https://rovo98.coding.me/posts/a1898ce2/)
- [queensferry——Manjaro Linux + KDE 安装使用手记](https://queensferry.coding.me/2018/06/22/Manjaro-Linux-KDE-%E5%AE%89%E8%A3%85%E4%BD%BF%E7%94%A8%E6%89%8B%E8%AE%B0/)
- [完美脱离Windows!! Linux发行版第一系统 Manjaro 开箱教程 :)](https://www.cnblogs.com/demonxian3/p/9259912.html)
- [基于SHADOWSOCKS的科学上网](https://zenuo.github.io/blog/%E5%9F%BA%E4%BA%8Eshadowsocks%E7%9A%84%E7%A7%91%E5%AD%A6%E4%B8%8A%E7%BD%91/)
- [折腾之 Manjaro 安装使用指北](https://yqsas.com/2019/03/03/manjaro-in-t480s/)\
- [Manjaro个性化配置](https://xyz1001.xyz/articles/1418.html)
- [撸Linux——我的Linux桌面常用软件列表 (2019年春)](https://www.lulinux.com/archives/5557)