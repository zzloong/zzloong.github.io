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

## 安装 Manjaro

### 制作 U 盘启动盘

- []()

## 源

配置中国的 `mirrors`，在 终端 执行下面的命令从官方的源列表中对中国源进行测速和设置：

```shell
sudo pacman-mirrors -g  # 排列源，可不执行
sudo pacman-mirrors -c China -m rank # 更改源，在跳出的对话框里选择想要的源
```

Manjaro 是基于 Arch 的，而 AUR 是 Arch 社区用户维护的软件包（也是 arch 可用软件量巨大的所在），我们等下要安装的搜狗拼音、chrome 等不少软件都在这里维护。我们可以为 Manjaro 增加这个中文社区的源来方便我们安装一下软件。

在 `/etc/pacman.conf` 中添加 `archlinuxcn` 源，末尾加上：

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

> 重启生效

## pacman

```shell
sudo pacman -S 软件名　# 安装
sudo pacman -R 软件名　# 删除单个软件包，保留其全部已经安装的依赖关系
sudo pacman -Rs 软件名 # 除指定软件包，及其所有没有被其他已安装软件包使用的依赖关系
sudo pacman -Ss 软件名  # 查找软件
sudo pacman -Sc # 清空并且下载新数据
pacman -Syu　#更新
```

## yay

AUR的全称是Arch User Repository，是Arch Linux/Manjaro用户的社区驱动存储库，创建AUR的目的是使共享社区包的过程更容易和有条理。使用它可以在Arch Linux/Manjaro系统中安装和更新软件包

Yay是用Go编写的Arch Linux AUR帮助工具，它可以帮助你以自动方式从PKGBUILD安装软件包， yay有一个AUR Tab完成，具有高级依赖性解决方案，它基于yaourt、apacman和pacaur，同时能实现几乎没有依赖、为pacman提供界面、有像搜索一样的yaourt、最大限度地减少用户输入、知道git包何时升级等功能。

yay 的常用命令：

```shell
yay -S package # 从 AUR 安装软件包
yay -Rns package # 删除包
yay -Syu # 升级所有已安装的包
yay -Ps # 打印系统统计信息
yay -Qi package # 检查安装的版本
```

## zsh

```shell
sudo pacman -S zsh # 安装zsh
chsh -s /bin/zsh # 修改默认shell
wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh
sudo pacman -S autojump
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

> 需要重启生效

具体的 zsh 配置教程，可以阅读之前的总结：

- [zsh+on-my-zsh配置教程指南（程序员必备）](https://michael728.github.io/2018/03/11/tools-zsh-tutorial/)

## ss

```shell
sudo pacman -S nodejs
sudo pacman -S npm
```

## 常用软件

```shell
# 开发软件
sudo pacman -S pycharm-professional
sudo pacman -S intellij-idea-ultimate-edition
sudo pacman -S visual-studio-code-bin
sudo pacman -S postman-bin
sudo pacman -S insomnia #REST模拟工具
sudo pacman -S gitkraken #GIT管理工具
sudo pacman -S wireshark-qt #抓包
sudo pacman -S zeal
sudo pacman -S vim


# 办公软件
sudo pacman -S google-chrome
sudo pacman -S bookworm #电子书阅读
yay -S typora #markdown 编辑
yay -S electron-ssr
yay -S xmind

# 娱乐软件
sudo pacman -S netease-cloud-music
yay -s deepin-wine-wechat
yay -S deepin-wine-tim

# 下载软件
sudo pacman -S aria2
yay -S deepin-wine-baidupan
```

## 参考

- [标点符——Manjaro Linux的安装体验](https://www.biaodianfu.com/install-manjaro-linux.html#comment-161818)
- [rovo98——Manjaro linux 安装与配置](https://rovo98.coding.me/posts/a1898ce2/)
- [queensferry——Manjaro Linux + KDE 安装使用手记](https://queensferry.coding.me/2018/06/22/Manjaro-Linux-KDE-%E5%AE%89%E8%A3%85%E4%BD%BF%E7%94%A8%E6%89%8B%E8%AE%B0/)
- [完美脱离Windows!! Linux发行版第一系统 Manjaro 开箱教程 :)](https://www.cnblogs.com/demonxian3/p/9259912.html)
- [掘金——Manjaro安装以及美化教程](https://juejin.im/post/5a6b1b3651882573443cea61)
- [manjaro使用ss方法（需nodejs环境）](https://segmentfault.com/a/1190000019071980)
- [基于SHADOWSOCKS的科学上网](https://zenuo.github.io/blog/%E5%9F%BA%E4%BA%8Eshadowsocks%E7%9A%84%E7%A7%91%E5%AD%A6%E4%B8%8A%E7%BD%91/)
- [折腾之 Manjaro 安装使用指北](https://yqsas.com/2019/03/03/manjaro-in-t480s/)\
- [Manjaro个性化配置](https://xyz1001.xyz/articles/1418.html)
- [人生苦短，我用Manjaro！](http://www.iamlightsmile.com/articles/%E4%BA%BA%E7%94%9F%E8%8B%A6%E7%9F%AD%EF%BC%8C%E6%88%91%E7%94%A8Manjaro%EF%BC%81/)
- [人生苦短，我用Manjaro！](https://zhuanlan.zhihu.com/p/50918522)