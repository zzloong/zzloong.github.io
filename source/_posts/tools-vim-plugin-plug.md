---
title: NeoVim 安装及配置
date: 2019-11-17 16:56:31
tags: [Vim, Tools, NeoVim]
categories: [ToolsDev]
keywords:
---

![](https://ws1.sinaimg.cn/mw690/6d9475f6ly1fxsp8yn8bdj20hs0bu42h.jpg)

Vim 学习系列：

- [Vim 基础](https://michael728.github.io/2018/12/02/tools-vim-basic/)
- [Vim 插件及配置](https://michael728.github.io/2018/12/02/tools-vim-plugin-config/)

<!-- more -->

## 安装 NeoVim

neovim 是目前流行的一个 vim 的加强版本。

[安装](https://github.com/neovim/neovim/wiki/Installing-Neovim)：

```
brew install neovim
```

neovim 的官方配置文件在 `/Users/michael/.config/nvim/init.vim`

```
# edit ~/.zshrc
alias vim='nvim'
alias vi='nvim'
```

检查：
```
nvim +checkhealth
```

支持 Python 语言：

```
pip install neovim --upgrade
```

## [插键 vim-plug](https://github.com/junegunn/vim-plug)

### 安装 vim-plug

插键没使用 Vundle，而是采用 vim-plug

- Vundle是之前Vim插件管理比较流行的工具。转向使用Vim-Plug，最大的原因还在于相比Vundle，所有的插件更新和安装都是并行的，这样比Vundle效率提升了不是一点半点。
- 此外，最令人心动的，是Vim-Plug的杀手级特性：按需加载控件，可以让你根据不同的需求，决定某些插件在什么时机开始加载，从而大大提升Vim/NeoVim的启动速度。

[安装 vim-plug 的脚本](https://github.com/junegunn/vim-plug)：

```
curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

### 配置 vim-plug

```
# 将已有的 Vim 配置，用于 nvim
# .vim 文件夹会存放插键相关内容，比如 vim-plug 的内容
ln -s ~/.vim ~/.config/nvim
ln -s ~/.vimrc ~/.config/nvim/init.vim
```

常用命令：
```
# 安装插键
:PlugInstall
# 检查状态
:PlugStatus
# 删除插键（需要先将 ~/.config/neovim/init.vim` 中注释掉相关插键）
:PlugClean
# 更新插键
: PlugUpdate
# 升级 vim-plug
:PlugUpgrade
```

将需要安装的插键写入 `~/.vimrc` 的如下命令之间，然后执行 `:PlugInstall`：
```
call plug#begin()
# 这里写上需要安装的插键
Plug 'tpope/vim-sensible'
call plug#end()
```

## 常用插键
- [vim-airline/vim-airline](https://github.com/vim-airline/vim-airline) 状态栏插件 vim-airine
- [vim-airline/vim-airline-themes](https://github.com/vim-airline/vim-airline-themes) 状态来主题，[预览](https://github.com/vim-airline/vim-airline/wiki/Screenshots)
- [jiangmiao/auto-pairs](https://github.com/jiangmiao/auto-pairs) 自动引号&括号补全

> 以引号输入为例，说明如何使用这个插件。按下 "，会自动变成双引号""，此时光标位于双引号的中间，等待插入文本，文本插入结束以后，通常我们希望把光标置于右边引号的后面继续输入，此时，再按一次 "，光标就会跳转到右边引号的后面，等待我们继续输入文本

- [scrooloose/nerdtree](https://github.com/scrooloose/nerdtree) 文件管理器 nerdtree

> - :NERDTree 即可打开当前编辑文件所在的目录
> - 按住 Ctrl, 双击 w 可以在两个窗口之间切换
> - 把光标移动到该文件，然后按 o，即可在右边窗口打开该文件
> - 在该窗口直接按 q 即可退出

- [Shougo/deoplete.nvim](https://github.com/Shougo/deoplete.nvim) 自动补全插键

参考：

- [Mac的VIM中delete键失效的原因和解决方案](http://cenalulu.github.io/linux/why-my-backspace-not-work-in-vim/)
- [Vim 插键及配置](https://michael728.github.io/2018/12/02/tools-vim-plugin-config/)
- [Linux 下 Neovim 安装与配置 Python 开发环境指南](https://jdhao.github.io/2018/09/05/centos_nvim_install_use_guide/)