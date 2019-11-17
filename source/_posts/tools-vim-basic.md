---
title: Vim 基础
date: 2018-12-02 20:56:31
tags: [Vim, Tools]
categories: [ToolsDev]
keywords:
---

![](https://ws1.sinaimg.cn/mw690/6d9475f6ly1fxsp8yn8bdj20hs0bu42h.jpg)

Vim 学习系列：

- [Vim 基础](https://michael728.github.io/2018/12/02/tools-vim-basic/)
- [Vim 插件及配置](https://michael728.github.io/2018/12/02/tools-vim-plugin-config/)
- [neovim 安装及插键配置](https://michael728.github.io/2019/11/17/tools-vim-plugin-neovim-plug/)

<!-- more -->

主要分为三种模式：
- 一般模式
- 编辑模式
- 命令行模式

## 光标的移动

### 单词级

比单纯的逐个字符的移动，效率要高

- `w` or `W` 向移动到下一单词开头  ★★
- `b` or `B` 向左移动到单词开头	★★

### 块级

- `gg`文档第一行，相当于1G 	★★★
- `G`文档最后一行,`<n>G`移动到你n行    ★★★
- `0` or `^` or`home`到行首（第1列）	★★
- `$` or `end`到行尾	★★★
- `:<N>` or `<N>gg`跳转到第 N 行	★★
- `<N>j` or `<N>↓`，向下移动 N 行，同理，也可以实现左右移动 ★★
- `v` or `V`，字符选择或者行选择 ★★
- `ctrl-v` 长方形选择，牛逼了 ★★
- `ctrl-f` 屏幕向下移动一页
- `ctrl-b` 屏幕向上移动一页

> 注意，所有命令都可以加一个数字`N`，表示对后面的命令执行N次，比如`<N>G`表示移动到第 N 行。

### 高级移动

- `'.` 跳到最后修改的那一行
- `gd` 跳到当前变量在当前文件的定义处，其实是跳转到当前变量在此文件中第一次出现的地方，不过一般来说，第一次出现的地方也就是变量定义的地方 ★★★
- `ma` 在当前位置做标记，用字母a标记当前光标所在位置，这里`a`可以是任意字母
- `:'a` 跳到标记a处 ★★
- `:'.` 跳转到最后一次修改的地方(.代表最后一次修改的地方)
- `:'"` 上一次编辑文件的地方
- `:''` 跳转到上次跳转之前的位置

## 打开文件、查找内容

### vim中打开文件

- `:e <filename>`，在vim中打开名为filename的文件，如果没有，则创建；

### 文档内查找

- `*` 向后查找光标当前所在单词
- `#` 向前查找光标当前所在单词	★★★
- `/<search>` 向后查找指定字符
- `?<search>` 向后查找指定字符串
- `n` 继续查找下一个	★★★
- `N` 继续查找上一个

### 匹配查找

vim 中可以使用` %` 对 `( `和 `)`，`[` 和 `]`，`{ `和 `}` 进行匹配查找，当光标位于其中一个 符号上时，按下` %`，光标会跳到与之匹配的另外一个符号上。

> 括号匹配，程序员必备



## 文档的修改与保存

### 插入

- `ctrl-p` 插入模式下进行单词补齐，比如有一个变量为`michael`，那么你只需要敲入部分名之后，就可以按下`ctrl-p`自动补全了。★★★
- `a` 当前字符后插入	★★★
- `I` 行首插入
- `A` 行尾插入
- `o` 在下一行插入	★★★
- `O` 在上一行插入	★★★


### 删除

- `x` 删除当前字符
- `X` 向前删除一个字符，相当于键盘的`Back Space`。
- `dd` 删除当前行，并将删除内容保存在vim剪贴板 `ndd`表示删除光标所在的向下n行。★★
- `dw` 删除光标所在位置到下个字的第一个字母
- `daw` 删除一个单词，包括词尾空格，实用，不用将光标移动到单词第一个字母，`aw`表示`a word`
- `d<X>` 删除指定内容，保存在剪贴板
- `c<X>` 删除指定内容，保存在剪贴板，同时进入insert模式

> 说明，<X>部分是对操作内容的描述，比如，删除一个单词，可以dw或者de，要复制当前位置到行尾内容，可以输入`y$`，要删除后面3个字符并插入，就输入`c3l`。

### 复制

- `yy` 复制当前行到vim剪贴板	`nyy`复制光标向下n行 ★★★
- `y<X>` 复制指定内容到剪贴板

### 粘贴

- `p` 当前位置后粘贴	★★★
- `P` 在当前位置前粘贴

### 合并

- `J` 当前行与下一行合并

### 替换

- `r<X>` 将当前字符替换为X  ★★★
-  `:%s/search>/<replace>/` 查找search内容并替换为replace内容，正则表达来替换，这个命令可以消除所有行位多余的空格：`:%s/\s\+$//`	★★★
- `<n1>,<n2>s/word1/word2/gc` n1/n2都是数字，在`n1`行和`n2`行之间寻找`word1`，替换为`word2`。`c`代表`confirm`，替换前需要你确认，不加就默认全部替换。`n2`用`$`表示时，表示搜索到最后一行。★★★


### 撤销、重做

- `u` 撤销	★★★
- `ctrl-r` 重做 ★★★
- `.` 重复前一个操作的意思 ★★★

### 保存文件
- `:wq` or `ZZ` 保存并退出
- `:x` 保存退出
- `:q!` or `ZQ` 强制推出，不保存
- `saveas <newfilename>` 文件另存为

### 编辑
- `ctrl-n` or `ctrl-p` Vim自带的补全（按照全文已有输入）★★★
- `d$` 从光标处删至当前行尾部

## 多窗口
- `:sp` 切割窗口
- `ctrl-w-j`或者`ctrl-w-↓` 跳转窗口
- `:q`或者`ctrl-w-q` 关闭当前窗口

### 重复上一次命令
vim有一个特殊的命令`.`，你可以用它重复执行上一个命令。我感觉有点像EXCEL中的`F4`命令。

### 缩进
- `>>` 向右缩进当前行
- `<<` 向左缩进当前行

## 分屏与标签页
### 分屏方式
- `:vsplit` 缩写`vs` or `ctrl-w v`左右分屏
- `:split` 缩写`sp` or `ctrl-w s`上下分屏
- `:diffsplit` 缩写`:diffs` diff模式打开一个分屏，后面可以加上{filename}

### 窗口跳转
- `ctrl-w w` 激活下一个窗口
- `ctrl-w j` 激活下方窗口
- `ctrl-w k` 激活上方窗口
- `ctrl-w h` 激活左侧窗口
- `ctrl-w l` 激活右侧窗口

### 关闭分屏

```
ctrl+w c # 关闭当前窗口 如果是最后一个窗口，无法关闭
ctrl+w q # 关闭当前窗口 如果只剩最后一个，则退出Vim
```

参考：
- [VIM的分屏功能](https://coolshell.cn/articles/1679.html)

## FAQ

### Q:配置VIM，安装vim-plug插件之后，想要生效通过`source ~/.vimrc`命令生效配置，就会报错：`E492: Not an editor command: Plug`

```
[root@localhost ~]# source .vimrc
-bash: .vimrc: line 2: syntax error near unexpected token `('
-bash: .vimrc: line 2: `call plug#begin('~/.vim/plugged')'
```

- [error while running “source .vimrc”](https://stackoverflow.com/questions/21651114/error-while-running-source-vimrc)
原因是，我们`.vimrc`本身并不是shell文件，而`source ~/.vimrc`等价于`./.vimrc`，当然后校验shell语法了。看了StackOverflow上的解答才清楚的。

- 可以通过输入`$vim`直接进入vim的命令模式，执行下面命令
```
:source ~/.vimrc
```
注意，我这里`.vimrc`的位置就是位于`~`目录下，你可以`:source /path/to/.vimrc`

- 或者编辑完`.vimrc`文件，直接：
```
:so $MYVIMRC
```

### Q:从Win上复制文件时，常常因为换行符出错

```
:set fileformat=unix
```

linux的文件换行符为`\n`，但windows却非要把`\r\n`作为换行符，所以，vim在解析从windows拷贝到linux的的vimrc时，因为遇到无法解析的\r，所以报错。
- [vim E492: Not an editor command: ^M(使用VIM打开文件一直提示错误)](http://blog.csdn.net/yin_pengpeng/article/details/51674064)

## 最后

周末放假好好折腾了一下，安装插键可以参考这篇文章：
- [Vim 插键及配置](https://michael728.github.io/2018/12/02/tools-vim-plugin-config/)

附上我的 [Vim 配置文件链接](https://github.com/Michael728/my-config-files)

## 参考

- [wklken一些vim个性化配置](https://zhuanlan.zhihu.com/p/27169393)
- [dofy-Github-Vim 实操教程（Learn Vim）](https://github.com/dofy/learn-vim) 系列基础入门
- [wxnacy-Vim 练级手册](https://vim.wxnacy.com/)
- [wxnacy-Vim 专辑](https://wxnacy.com/vim/)
- [archlinux-Vim (简体中文)](https://wiki.archlinux.org/index.php/Vim_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))
- [kawabangga-Vim系列教程](https://www.kawabangga.com/vim%E7%B3%BB%E5%88%97) 进阶用法