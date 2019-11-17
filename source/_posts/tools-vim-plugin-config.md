---
title: Vim 插件及配置
date: 2018-12-02 20:25:42
tags: [Vim, Tools, Plugin]
categories: ToolsDev
keywords:
---

![](https://ws1.sinaimg.cn/large/6d9475f6ly1fy7rsa016rj20hs0bu76g.jpg)

{%cq%} 编辑器之神 —— Vim {%endcq%}

Vim 学习系列：

- [Vim 基础](https://michael728.github.io/2018/12/02/tools-vim-basic/)
- [Vim 插件及配置](https://michael728.github.io/2018/12/02/tools-vim-plugin-config/)
- [neovim 安装及插键配置](https://michael728.github.io/2019/11/17/tools-vim-plugin-neovim-plug/)

平日使用 vim 经常编辑文件，想想使用时的痛点，决定研究一下插件的使用。

<!-- more -->

> Vim的扩展通常也被成为bundle或插件。

软件版本：
- Mac OS X 10.14.1
- vim 8.1

## 插件安装-Vundle

众多文章中都提到[Vundle](https://github.com/VundleVim/Vundle.vim)，那我就选用它好了！

有一个 Vim 的插键网站，专门有相关插键的配置介绍：[VimAwesome](https://vimawesome.com/)

### 1.将Vundle下载到本地，后面下载的插件也将会下载到`~/.vim/bundle`路径下。
```
git clone https://github.com/gmarik/Vundle.vim.git ~/.vim/bundle/Vundle.vim
```
### 2.插件配置，将如下的内容粘贴到`~/.vimrc`的顶部(前提是，你本身`.vimrc`里一开始没有什么其他内容)。

```
set nocompatible              " be iMproved, required
filetype off                  " required

" set the runtime path to include Vundle and initialize
set rtp+=~/.vim/bundle/Vundle.vim
call vundle#begin()
" alternatively, pass a path where Vundle should install plugins
"call vundle#begin('~/some/path/here')

" let Vundle manage Vundle, required
Plugin 'VundleVim/Vundle.vim'

" The following are examples of different formats supported.
" Keep Plugin commands between vundle#begin/end.
" plugin on GitHub repo
" Plugin 'tpope/vim-fugitive'
" plugin from http://vim-scripts.org/vim/scripts.html
" Plugin 'L9'
" Git plugin not hosted on GitHub
" Plugin 'git://git.wincent.com/command-t.git'
" git repos on your local machine (i.e. when working on your own plugin)
" Plugin 'file:///home/gmarik/path/to/plugin'
" The sparkup vim script is in a subdirectory of this repo called vim.
" Pass the path to set the runtimepath properly.
" Plugin 'rstacruz/sparkup', {'rtp': 'vim/'}
" Install L9 and avoid a Naming conflict if you've already installed a
" different version somewhere else.
" Plugin 'ascenator/L9', {'name': 'newL9'}

" All of your Plugins must be added before the following line
call vundle#end()            " required
filetype plugin indent on    " required
" To ignore plugin indent changes, instead use:
"filetype plugin on
"
" Brief help
" :PluginList       - lists configured plugins
" :PluginInstall    - installs plugins; append `!` to update or just :PluginUpdate
" :PluginSearch foo - searches for foo; append `!` to refresh local cache
" :PluginClean      - confirms removal of unused plugins; append `!` to auto-approve removal
"
" see :h vundle for more details or wiki for FAQ
" Put your non-Plugin stuff after this line
```

### 3.安装插件

将需要安装的插键放入`.vimrc`文件中即可，例如：
```
Plugin 'scrooloose/nerdtree'
"Plugin 'scrooloose/nerdtree'  " 如果暂时不启用，就将该插件注释掉
```

打开vim，然后输入：

```
vim # 打开 vim
:PluginInstall
:PluginList # 查看已安装插键列表
```



### 4.升级与卸载插件

```
# 注释下面这段话
# Plugin 'Valloric/YouCompleteMe'
```

然后

```
:PluginUpdate # 这个命令本身可以一键升级所有插件
:PlugginClean
```

参考：
- [How to unInstall plugin?](https://github.com/VundleVim/Vundle.vim/issues/733)

### 5.帮助文档

```
:h vundle
```

## [NERDTree](https://github.com/scrooloose/nerdtree)

```
Plugin 'scrooloose/nerdtree'

"F2开启和关闭树"
map <F2> :NERDTreeToggle<CR>
let NERDTreeChDirMode=1
"显示书签"
let NERDTreeShowBookmarks=1
"设置忽略文件类型"
let NERDTreeIgnore=['\~$', '\.pyc$', '\.swp$']
"窗口大小"
let NERDTreeWinSize=25
```

## [python-mode](https://github.com/python-mode/python-mode)

```
Plugin 'klen/python-mode' " https://vimawesome.com/plugin/python-mode
```

编写 Python 文件保存时，就会进行语法检查了：
```
let g:pymode_rope = 1
let g:pymode_rope_completion = 1
let g:pymode_rope_completion_bind = '<C-p>'  "为了自动补全有效，需要将 set paste 设置不启用
let g:pymode_rope_goto_definition_bind = '<C-c>g'
let g:pymode_python = 'python' " 默认检查 Python2
"Autofix PEP8 errors
nnoremap <leader>f :PymodeLintAuto<CR>
```

快捷键：
```
K 显示内置函数文档
<leader>r 运行 python 文件 # let mapleader=", " " 设置 leader 为空格，那么`,+r`就可以运行 python 文件了
```

参考：
- [油管-python-mode](https://www.youtube.com/watch?v=67OZNp9Z0CQ)
- [Can't get the jedi-vim plugin to work](https://stackoverflow.com/questions/21628743/cant-get-the-jedi-vim-plugin-to-work/41574760) 解决了 python-mode 不自动补全的问题
- [python-mode/doc/pymode.txt](https://github.com/python-mode/python-mode/blob/develop/doc/pymode.txt)

## [vim-airline](https://github.com/vim-airline/vim-airline)

一个状态栏美化工具，颜控必备。附带功能可以一目了然的区分各种编辑状态。

```
Plugin 'vim-airline/vim-airline' "https://github.com/vim-airlin    e/vim-airline
Plugin 'vim-airline/vim-airline-themes' "  https://github.com/v    im-airline/vim-airline-themes https://github.com/vim-airline/vi    m-airline/wiki/Screenshots
```

[主题预览](https://github.com/vim-airline/vim-airline/wiki/Screenshots)

> To use solarized dark, set :AirlineTheme solarized and add the following to your .vimrc: let g:airline_solarized_bg='dark'

配置：

```
let g:airline_powerline_fonts = 1
let g:airline_theme='deus'
let g:Powerline_symbols='fancy'
let Powerline_symbols='fancy'
set t_Co=256 " 状态栏就有颜色了
```
## [Yggdroot/indentLine](https://github.com/Yggdroot/indentLine)

安装：
```
Plugin 'Yggdroot/indentLine'
```
配置：
```
let g:indentLine_enabled = 1
let g:indentLine_color_term = 239
```
为了这个插件能够有效果，也是折腾了半天。在 CentOS 平台是正常的，但是在 Mac 上的缩进线显示不正确，为何会这样呢？[SOF-Why is apple vim compiled WITHOUT conceal feature?](https://stackoverflow.com/questions/52452738/why-is-apple-vim-compiled-without-conceal-feature)，原来 Mac 上自带的 Vim 版本虽然是8.0版本，但是没有`concel`这个 Feature ，而`indentLine`插件要显示对齐线依赖这个，[坚线和星号在使用  conceal  功能](http://vimcdoc.sourceforge.net/doc/usr_01.html)。

那么该怎么添加这个特性呢？搜了一圈，可以重新安装 Vim，可以参考[这篇文章](https://segmentfault.com/a/1190000015885712#articleHeader35)安装 Vim。

经过安装设置之后，可以通过`vim --version|grep con`或者`:echo has("conceal")`查看是否已经具有 conceal 特性：
![](https://ws3.sinaimg.cn/large/006tNbRwly1fxrioguwosj30f902mdg7.jpg)

## [rking/ag.vim](https://github.com/rking/ag.vim)

ag 的语法：
```
ag [FILE-TYPE] [OPTIONS] PATTERN [PATH]
```

ag 这个 vim 插键主要是基于这个项目 [ggreer/the_silver_searcher](https://github.com/ggreer/the_silver_searcher)

```
ag --list-file-types # 查看支持自定义哪些文件类型
```

安装了这个插键后，在 vim  的命令模式下，可以使用`:Ag [options] {pattern} [{directory}]`搜索了。


## [majutsushi/tagbar](https://github.com/majutsushi/tagbar)

安装 vim 插键之前，机器本身需要`ctags`：
```
# ubuntu
sudo apt-get install ctags
# centos
sudo yum install ctags
# mac
brew install ctags
```

在这时使用 vim-tagbar 插件可以帮你快速了解当前文件中的类、方法等。
```
Plugin 'majutsushi/tagbar' " https://github.com/majutsushi/tagbar
```

配置：
```
nmap <F8> :TagbarToggle<CR>
```

关于 tagbar 的使用，看查看这篇文章 [wklken-大纲式导航](http://www.wklken.me/posts/2015/06/07/vim-plugin-tagbar.html)

## [YouCompleteMe](https://github.com/Valloric/YouCompleteMe#installation)

目前主要涉及的是 Python 开发，所以，YCM 目前没有配置，如下仅供参考。
```
Plugin 'Valloric/YouCompleteMe'
```

```
sudo apt-get install build-essential cmake
sudo apt-get install python-dev python3-dev
cd ~/.vim/bundle/YouCompleteMe
./install.py --all

cp ~/.vim/bundle/YouCompleteMe/third_party/ycmd/examples/.ycm_extra_conf.py ~/.vim/
```

```
"YouCompleteMe配置相关
let g:ycm_server_python_interpreter='/usr/bin/python'
let g:ycm_global_ycm_extra_conf='~/.vim/.ycm_extra_conf.py'
```

## 主题

安装主题的方法比较简单，例如需要安装`molokai`主题，手动安装则按照如下步骤操作：
- 从 https://github.com/fatih/molokai 下载`molokai.vim`文件，放入`~/.vim/colors`中
- 然后在`~/.vimrc`文件中加入行`colorscheme molokai`即可。

因为我在 [VSCode](https://michael728.github.io/2018/10/28/tools-vscode/) 和 Iterm2 中都采用了 [Dracula](https://draculatheme.com/vim/) 的主题，因此，vim 主题我也同样偏爱它，可以采用插键的方式安装：
```
Plugin 'dracula/vim'
:PluginInstall
```
- 然后在`~/.vimrc`文件中加入行`colorscheme dracula`即可。

主题相关的命令：

```
:colorscheme   "查看当前主题
:colorscheme space tab "列出所有主题
:colorscheme your-theme "切换主题
```

## 设置vim

为了让vim使用起来更加得心应手，先做一些简单的配置。

编辑VIM配置文件，可能一开始没有这个文件，不过没关系，直接`vi ~/.vimrc`保存这个文件即可。

今天学习到`<leader>` 这个概念，很强大，快捷键很方便！

- `nnoremap` 将一个组合快捷键映射为另一个快捷键。

关于`leader`以及其他`map`知识，可以查看如下文章：
- [提高 Vim 使用效率的 12 个技巧](http://blog.jobbole.com/87481/)
- [【Vim】使用map自定义快捷键](https://blog.csdn.net/JasonDing1354/article/details/45372007)

### 个人vim配置

参考多人的配置，打造属于自己的Vim配置，这个配置不涉及插件的设置，因为常常生产环境是网络不通的，要迅速配置能用：
```
let mapleader="," " 设置 leader
let g:mapleader = ','

" 分屏窗口移动, Smart way to move between windows
map <C-j> <C-W>j
map <C-k> <C-W>k
map <C-h> <C-W>h
map <C-l> <C-W>l

" Go to home and end using capitalized directions
" H和L跳转到行首行末, 实在不想按0和$, 太远
noremap H ^
noremap L $

" 命令行模式增强，ctrl - a到行首， -e 到行尾
cnoremap <C-a> <Home>
cnoremap <C-e> <End>

" 去掉搜索高亮
noremap <silent><leader>/ :nohls<CR>

" 快速保存和退出
" Quickly close the current window
nnoremap <leader>q :q<CR>
" Quickly save the current file
nnoremap <leader>w :w<CR>


syntax on " 自动语法高亮
set cursorline " 突出显示当前行
set encoding=utf-8
set fileencoding=utf-8
set fileformat=unix "从Win上复制文件时，避免换行符错误
set hlsearch " 搜索时高亮显示被找到的文本
set ignorecase smartcase " 搜索时忽略大小写，但在有一个或以上大写字母时仍保持对大小写敏感
set incsearch " 输入搜索内容时就显示搜索结果
set laststatus=2 " 显示状态栏 (默认值为 1, 无法显示状态栏)
set magic " 设置魔术
set nocompatible " 关闭 vi 兼容模式
set number " 显示行号
set paste  " 解决拷贝的时遇到注释会自动注释后续所有行的问题
set ruler " 打开状态栏标尺
set shiftwidth=4 " 设定 << 和 >> 命令移动时的宽度为 4
set softtabstop=4 " 使得按退格键时可以一次删掉 4 个空格
set smartindent " 开启新行时使用智能自动缩进
set tabstop=4 " 设定 tab 长度为 4
set ambiwidth=double " 设置为双字宽显示，否则无法完整显示如:☆
```

vim配置文件中的注释，末尾用`"`隔开即可。保留注释，对于了解配置内容有利。

### vim配置参考
- [Michael728/my-config-files](https://github.com/Michael728/my-config-files) 个人总结的配置文件
- [wklken/k-vim](https://github.com/wklken/k-vim)
- [Mac OS X 配置指南-vim](https://wild-flame.github.io/guides/docs/mac-os-x-setup-guide/install_vim) 同时也有关于 macOS 介绍
- [强大的vim配置文件，让编程更随意](http://www.cnblogs.com/ma6174/archive/2011/12/10/2283393.html) 有vim配置的中文注释

## FAQ

### 1. github访问速度慢，下载插件失败

- [ubuntu上解决访问github慢的方法](https://blog.csdn.net/Alicehzj/article/details/77679105)

### 2. mac上iterm2中, tmux里面鼠标复制, 无法选中一行内容

然后你会发现你想复制terminal上的东西的时候，死活复制不了，这时按住 `Option (Alt)`键就行了。

- [ubuntu tmux复制粘贴总结](https://blog.csdn.net/w5688414/article/details/82917582)

## 参考

- [pytlab-优雅的在终端中编写Python](http://pytlab.org/2017/03/26/%E4%BC%98%E9%9B%85%E7%9A%84%E5%9C%A8%E7%BB%88%E7%AB%AF%E4%B8%AD%E7%BC%96%E5%86%99Python/) 同时介绍了 Tmux 和 Vim 的配置
- [那些离了就活不了的 VIM 插件](http://www.zlovezl.cn/articles/vim-plugins-cannot-live-without/) 介绍了一些经典的插键
- [阮一峰-Vim 配置入门](http://www.ruanyifeng.com/blog/2018/09/vimrc.html)
- [YouCompleteMe 安装配置方法](https://zhuanlan.zhihu.com/p/35626421)
- [知乎-Vim 8.0 正式发布](https://zhuanlan.zhihu.com/p/22440607) 提到了 Vim 编译安装的一些好教程
