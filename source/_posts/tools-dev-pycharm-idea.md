---
title: JetBrains PyCharm/IDEA 使用技巧总结
date: 2019-05-11 16:46:02
tags:
  - IDE
  - IDEA
  - Pycharm
categories: ToolsDev
keywords: [pycharm,idea]
---

> 工欲善其事必先利其器

## 基本概念

IDEA 没有类似 Eclipse 的工作空间的概念（workspace），最大单元就是 Project。这里可以把 Project 理解为 Eclipse 中的 workspace。Module 可以理解为 Eclipse 中的 project。一个 工程（Project） 下可以创建多个 模块（Moudle）。

- src 目录，用于存放代码
- .idea 目录和 project01.iml 文件都是 IDEA 工程特有的。
- 删除 module ，这样不会删除代码文件，只是从 Project 中移除组织关系，如下图：

![](https://gitee.com/michael_xiang/images/raw/master/open-module.jpg)

## 系统设置

### 设置显示收藏栏等工具栏

![](https://gitee.com/michael_xiang/images/raw/master/view-toolbar.jpg)

### 取消更新

取消 IDEA 自动更新，避免引入不稳定的问题：

![](https://gitee.com/michael_xiang/images/raw/master/idea-no-update.jpg)

### 设置Tab为4个空格

Editor->Code Style->Java/Python，不要勾选`Use Tab character`

### 支持滚轮调节字体大小

Editor->General，勾选 `Change font size(Zoom) with Command+Mouse Wheel`

### 鼠标滑过，显示文档

Editor->General，`Show quick documentation on mouse move`，输入延迟时间。鼠标滑过类、方法时，显示文档说明

### 显示行号和方法分隔符

在 Editor->General->Apperance 中，勾选：

- `Show line numbers` 显示行号
- `Show method separators` 这个是显示方法分隔符的，我没勾选
- `Show whitespace` 显示空格符号，针对 Python，「游标卡尺」的名号不是白叫的，所以，必须设置显示空格、缩进等

参考：

- [舒服的pycharm设置](http://brucedone.com/archives/38)

### 给提示时忽略大小写

Java 是严格区分大小写的，未设置时，输入 `str` 时，它不会提示 `String`，要想实现忽略大小写的情况它也能智能提示，可以进行如下设置，而且，这样设置它也不是完全按照首字母进行匹配了，比如你输入 `hmap` 时，也会看到 `HashMap` 的智能提示，相当方便。Editor->Genreral->Code Completion：

![](https://gitee.com/michael_xiang/images/raw/master/idea-code-completion.jpg)

{% note success no-icon %}
这个设置是非常有帮助的，强烈推荐设置。可以顺带勾选上 `Show the documentation`，这样在补充时，也会显示文档。
{% endnote %}

### 自动导包 `import popup`

`Editor->General->Auto Import->Python->Show import popup`

![](https://gitee.com/michael_xiang/images/raw/master/idea-auto-import.jpg)

手动导包的快捷键是 `Alt+Enter`

### 设置换行符默认为LF

`Editor->Code Style` 页面不仅能设置换行符，还可以设置 `wrap columns`。

![](https://gitee.com/michael_xiang/images/raw/master/lf-wrap.jpg)

参考：

- [IntelliJ换行CRLF, LF, CR的解释和默认设置](https://blog.csdn.net/ECHO_FOLLOW_HEART/article/details/48314523)

### 设置 `File Encodings` 文件编码

`Settings->Editor->File Encodings`，编码全都设为 `UTF-8`。

不建议勾选 `Transparent native-to-ascii convention`

### 设置头信息

`Settings->Editor->File and Code Templates-Files->Includes->File Header` 设置如下模板

Python 头信息设置：

```java
#!/usr/bin/env python
# -*- coding: utf-8 -*-
# @Date    : ${DATE} ${TIME}
# @Author  : Michael
# @File    : ${NAME}.py
# @Software: ${PRODUCT_NAME}
```

Java 头信息设置：

```java
/**
 * description: 
 * 
 * @author Michael
 * @date ${DATE}
 * @time ${TIME}
 */
```

- [PyCharm创建.py自动添加文件头注释](https://blog.csdn.net/qq_36482772/article/details/67218214)
- [IntelliJ IDEA文件头注释模板](https://blog.csdn.net/totally123/article/details/53466011)

### 自动编译

有时候我们需要操作 class 文件时，却忘记了对 java 类文件进行编译，从而还是对旧的文件进行了操作。

![](https://gitee.com/michael_xiang/images/raw/master/idea-compile.jpg)

### import 每个类而非整个包

settings—Editor—Colors Style—Java—Imports—Class count to use import with ‘*’ 设置大一点。

当 Java 类中 import 某个包下的类超过这里设置的个数，就会换成用星号来代替，比如 `import java.util.*`

### 设置 Tab 标签页

`File->Editor->General->EditorTable->Tab closing Policy->Tab limit`

在 `Windows->Editor Tabs` 可以设置标签页的相关内容，比如：

- `Tabs Placement` 放置位置，我勾选了 `Top`
- 勾选了 `Sort Tabs By Filename`
- 勾选了 `Open New Tabs At The End`
- 取消勾选 `Show tab in single row`，避免打开类文件多时，选择不方便

我设置最多打开标签页10个，同时将位置移到了右边，通过`alt+←/→`可以切换工作区。默认是10，即最多打开十个窗口，再打开新的就会把之前的窗口T掉！

- [IntelliJ IDEA设置打开窗口的数量](https://blog.csdn.net/SJZYLC/article/details/49078261)

### 自定义 Terminal

`settings->Tools->Terminal`

`Shell path` 原来的值是:

- `C:\Windows\System32\cmd.exe` Windows

修改为：

- `C:\Program Files\Git\bin\bash.exe` 修改为 `Git bash` 的终端；
- `"C:\Windows\System32\cmd.exe" /k ""%CMDER_ROOT%\vendor\init.bat""` 修改为 `Cmder` 的终端，这种方式就会弹出 Cmder 的窗口，需要配置系统变量 `CMDER_ROOT`，值为：`D:\Green-SF\cmder`。

参考：

- [Pycharm官宣-Terminal](https://www.jetbrains.com/help/pycharm/settings-tools-terminal.html)
- [Github-Run cmder inside IntelliJ idea](https://github.com/cmderdev/cmder/issues/282)
- [Windows 上切换 Jetbrains IDE 的 terminal 为 PowerShell 或 cmder](https://blog.qxzzf.com/363)

### 版本控制下文件变化的显示

settings—Version Control—Show directories with changed descendants

开启后若想调整文件夹的显示颜色：`settings—Version Control—File Status Color`

### 打开内存使用状态

settings—Appearance & Behavior—Appearance—Show memory indicator

参考：

- [玄玉-idea配置小结](https://jadyer.cn/2016/04/20/idea-config/) 博主介绍的很详细

### 用 * 标识编辑过的文件

这样被修改的文件会以 `*` 号标识出来：

Editor –> General –> Editor Tabs，勾选 `Mark modifyied(*)`

### 脚本运行结束后留在命令行

这样脚本运行中的变量在脚本结束后还可以调用，调试时还是挺好用

![](https://ws2.sinaimg.cn/large/006tNbRwly1fya556wvjbj310c0fkdj8.jpg)

- [pycharm常用设置和技巧](https://segmentfault.com/a/1190000010783422)

### 主题

- [IDEA主题-](http://riaway.com/) Settings->Editor->Color Scheme->Import Scheme，导入 jar 包；
- [Dracula](https://draculatheme.com/)

### 添加 JavaDOC 注释

- 把光标停在类名或者方法名上，然后 `Alt+enter`，出现几个选项，选择 `Add Javadoc` 就 OK 了
- 默认 `Fix doc comment` 是没有设置快捷键的，我这里把他设置成 `Alt + M`

参考：

- [开源中国-IDEA添加JavaDOC注释 方法 快捷键](https://my.oschina.net/zjllovecode/blog/1510055)

### 生成 JavaDOC

Javadoc 用于描述类或者方法的作用，具体介绍，可查看[CSDN-Javadoc 使用详解](https://blog.csdn.net/vbirdbest/article/details/80296136)

![](https://ws3.sinaimg.cn/large/006tNbRwly1fyq6zthfcpj309q0b9q71.jpg)

```shell
Locale：输入语言类型：zh_CN
Other command line arguments：-encoding UTF-8 -charset UTF-8
```

![](https://ws4.sinaimg.cn/large/006tNbRwly1fyq729i5jnj30i70fmdhs.jpg)

### 缓存和索引的清理

IDEA 首次加载项目时，都会创建索引。创建索引的过程，暂时不要去编辑代码，避免遇到未知问题。有时候意外断电或蓝屏，出现索引损坏情况，可以清除缓存和索引，还原成默认状态。

File-Invalidate Caches

![](https://ws3.sinaimg.cn/large/006tNbRwly1fyq7f37wkyj30bz0b97aw.jpg)

![](https://ws4.sinaimg.cn/large/006tNbRwly1fyq7sdy4chj30gk04g0td.jpg)

这个会将本地修改历史也会清理掉，如果想要将这个数据保留，LocalHistory 需要提前备份：

```shell
# system  目录，系统文件目录，是 IDEA 与开发项目一个桥梁目录，里边主要有缓存、索引、容器文件输出等等
/Users/michael/Library/Caches/IntelliJIdea2018.2/LocalHistory
# 顺带看一下 config 目录，是 IDEA 个性化设置目录，是整个 IDE 设置目录
/Users/michael/Library/Preferences/IntelliJIdea2018.2
```

参考：

- [Where is IntelliJ IDEA config stored in OSX?](https://stackoverflow.com/questions/23115091/where-is-intellij-idea-config-stored-in-osx/23141889)
- [官宣-Directories used by the IDE to store settings, caches, plugins and logs](https://intellij-support.jetbrains.com/hc/en-us/articles/206544519-Directories-used-by-the-IDE-to-store-settings-caches-plugins-and-logs)

### 显示窗口 split

有时候可以将当前窗口 split，可以垂直或者水平分割，方便阅读代码

![split](https://gitee.com/michael_xiang/images/raw/master/gAxEFu.png)

### 省电模式

在菜单栏 `File` 下面有一个选项是 `Power Save Mode`，这个叫「省电模式」，如果不小心勾选了这个选项，那么会关掉代码检查和代码提示等功能，所以需要注意。

## 快捷菜单

Mac 的 `Command` 键一般是对应了 Win 的 `Ctrl` 键，在 Win 上，还可以用 `Alt+<n>` 打开相关窗口，在 Mac 上同样要使用 `Command+<n>`。

除此以外，还可以在 `Settings-Keymap` 中，设置快捷键、输入快捷键查看对应的功能。

### 快捷键设置

- `alt+r`：打开最近项目
- `ctrl+shift+o`：打开项目
- 自动补全快捷键，默认是 `ctrl+空格`，这个和一些中文输入法的切换快捷键冲突了，于是，我多设置了一个 `ctrl+/` ：

![](https://ws1.sinaimg.cn/large/006tNbRwgy1fyp6mdh7h6j31jm0mgwit.jpg)

### 常用快捷键

在介绍常用快捷键之前，先看看这个快捷键 `ctrl+shift+a`，Find Action 快捷键，输入关键词，不会的就问它，超级赞！！！★★★

- `alt+↑/↓`: 快速在方法间跳转 ★★
- `alt+p`：打开Python控制台 ★★★
- `alt+insert` 生成代码(如 get, set 方法,构造函数等) 或者右键（Generate），在 mac 上是 `ctrl+n` ★★★
- `alt+enter` 万能键，任何地方都可以尝试使用，不同场景有不同的表现方式，比如自动导包等功能，不管出错了不知道怎么办，知道怎么办想这么办时都按下这个快捷键，有惊喜，万能纠错键 ★★★
- `alt+f7` 找当前变量、方法在哪些地方用了 `find usaged` ★★★
- `alt+f12` 打开命令行终端(view->tool window) ★★★
- `ctrl+space` 万能提示键，基本代码补全
- `ctrl+f4`：关闭当前页
- `ctrl+f12`: 结构视图 File Structure，查看 class method field，阅读源码查看类方法时很方便 ★★★
- `ctrl+home`、`ctrl+end` 回到页首/页尾，关键字 `Move Caret to Text End` 或者 开头 `Move Caret to Text Start`，[参考](https://segmentfault.com/q/1010000007099956)
- 查看接口继承关系：
  - `ctrl+h` 查看类型继承关系 `type hierarchy`,只能查看向上向下继承关系，而不能看实现了哪些接口。查看继承关系，右击，点击 Digram 查看；
  - `ctrl+alt+h` 查看函数调用层次结构关系 `call hierarchy`，可以看到函数被哪些地方调用了，但是需要谨慎，部分人不会注意到后面的调用次数！为了防止遗漏，建议使用 `alt+f7` 这个快捷键 ★★★
  - `ctrl+alt+b` 查找接口的实现类
- 注释
  - `ctrl+/`  注释
  - `ctrl+shift+/` 多行注释
- `ctrl+u` 查看当前方法的父方法 `super method`，比如 service 的实现类中，跳转到覆盖的接口方法
- `ctrl+d` 复制当前行 ★★
- `ctrl+e` 打开最近文件 ★★★
- `ctrl+p` 有时候敲了方法，忘记方法可以使用什么参数，那么这个快捷键可以显示 `parameter info`
- 删除当前行
  - `ctrl+y` 删除当前行 ★★★
  - `ctrl+x` 剪切（会删除当前行）★★★
- `ctrl+w` 不断按下这个快捷键，选择代码块范围逐步扩大；★★★
- 换行
  - `shift+enter` 向下另起一行 ★★★
  - `ctrl+alt+enter` 向上另起一行 ★★★
- `crtl+q` 查询documentation
- `ctrl+鼠标点击` 查看内置函数啥的
- `ctrl+alt+←/→` 快速跳转光标刚刚所在行 ★★★
- `ctrl+alt+b` 所有的实现类（Implementation）★★★
- `ctrl+alt+h` 谁调用了这个方法，查看调用层次（Call Hierarchy）★★
- `ctrl+alt+L`，格式化代码（Reformat Code） ★★★
- `ctrl+alt+o` 优化 imports `Optimize Imports`，优化导入的类和包 ★
- `ctrl+shift+enter` 补全语句，`complete current statement` 不管光标是不是在行尾，可以补全必要的字符，比如行尾的分号 ★★
- `ctrl+shift+b` 查看接口中方法的实现方法；
- `ctrl+shift+u`：大小写转换，在 `Edit->Toggle Case` 中能看到选项 ★
- `ctrl+shift+v`：从最近的缓冲区粘贴
- `ctrl+shift+backspace` 快速跳转到最后修改的地方 ★★★
- `ctrl+shift+v` 在 Pycharm 中，访问历史粘贴板 ★★
- `ctrl+shift+/-` 代码块折叠★
- `ctrl+alt+shit+j` 多目标选择选择的词，或者用`alt+鼠标点击`也可以实现效果，`Select All occurrences`★★
- `ctrl+alt+shit+t` 重构利器
- `shift+f6`：文件重命名 ★★★
- `shift+f2`：跳转到上一个高亮错误 或 警告位置
- 搜索
  - `shift` 双击，搜索一切，不管是 IDE 功能、快捷键、文件、方法、变量，它都能搜索，使用频率很高的一个快捷键！可以按最近使用过的文件名, 类名, 方法名等去搜索定位! ★★★
  - `ctrl＋n`：快速按照名字搜索类
  - `ctrl＋shift＋n`：快速搜索文件
  - `ctrl+shift+f`: 在整个项目中 或 指定目录里 进行全局搜索 ★★★
  - `ctrl＋alt+shift＋n`：快速搜索函数

参考：

- [极客学院-IntelliJ IDEA 使用教程-快捷键](http://wiki.jikexueyuan.com/project/intellij-idea-tutorial/keymap-introduce.html)

### alt+enter  快捷键的妙用

万能接错/生成返回值变量。创建对象时，可以先 `new` 后`alter+enter`，IDEA 会自动补全，直接让你输入对象名：

![](https://ws1.sinaimg.cn/large/6d9475f6ly1fyq399jc4eg20lm06xdy8.gif)

### 其他

- `scroll from source`，点击这个按钮，可以快速跳转到打开的文件所在的文件夹中，非常有用提高效率的按钮 ★★★

![](https://gitee.com/michael_xiang/images/raw/master/VB32tM.png)

## 模板

模板：

- Editor->Live Templetes，可以做一些用户的定义，可修改
  - 关注 `iteration`
  - 关注 `other`
  - 关注 `output`
  - 关注 `plain`
- Editor->General->Postfix Completion 不可修改

### 常用模板

- `psvm` 再按 Tab 生成 main 方法，或者，输入 main 之后，再按 `ctrl+j`
- `sout` 输出函数
  - `soutp` 打印函数的形参
  - `soutv` 打印变量值，就近的打印变量的值；
  - `soutm` 打印方法的名字
  - `xxx.out` 打印某变量 xxx 的值；
- `fori` 循环，按 `tab/enter` 进入下一处修改
  - `itar` 循环控制语句是 `xxx.length`
  - `iter` 增强 for 循环（`for each`）
  - `users.for+tab` 快速生成 users 的增强 for 循环
- `list.for` 集合
  - `list.fori`
  - `list.forr` 逆序遍历，倒序遍历
- `ifn` 条件判断，判断等于 `null`
  - `inn` 不等于 `null`
- `prsf` 私有静态的常量
  - `psf` 生成 `public static final`
  - `psfs` 生成 `public static final String`

快捷键 `ctrl/command+j` 可以查看有哪些缩写字符。

更多关于模板的知识，点击[官宣-live templates](https://www.jetbrains.com/help/idea/using-live-templates.html)

参考：

- [IntelliJ 创建main函数快捷](https://blog.csdn.net/tiantiandjava/article/details/42269173)

### 修改模板

在 Live Templates 中修改模板

- `abbreviation` 模板的缩略名称
- template text` 模板的 diamante 片段
- 应用范围，点解 Define，选择 Java

![修改](https://gitee.com/michael_xiang/images/raw/master/Twxx9l.png)

添加一个 String 类型的带注释的模板，叫 `prsc`：

```java
/**
 * $VAR1$
 */
 private String $VAR2$;
 $END$
```

## 版本控制 VCS

![VCS](https://ws3.sinaimg.cn/large/006tNbRwly1fyq1fwyvdij30ek0bfdlz.jpg)

### 添加 Github 账户

- Settings->Version Control->GitHub

为何要添加这个账户呢？

因为你就可以直接在 VCS 菜单栏中克隆代码库，多种方式：

- `Checkout from Version Control` -> Git
- Git -> Clone 可以选择待下载的代码库
- File -> Project from Version Control -> Git

### 在 Github 创建仓库

VCS -> Import into Version Control -> Share Porject on GitHub

会将代码仓库提交到 GitHub 上，新建一个仓库

### 使用 Git 工具

- 查找背锅侠：Annotate/Blame
- 选中 Annotate 的 Commit，点击 Show Diff 观察差异（`Ctrl+D`）
- 查看当前文件的历史版本：工具栏有个时间标志，Show History，查看最近是否有人修改过，通过 `Ctrl+D` 查看对比——`Alt+H`
- 高级筛选方式查找 commit 记录
- 显示差异： Show Diff —— `Ctrl+D`
- 右键菜单中有一个 Open On Github，跳转到正在浏览的行所在的文件，方便分享讨论具体问题

## 调试

![](https://ws4.sinaimg.cn/large/006tNbRwly1fyq27kq7glj30hs0950th.jpg)

1. `step over` F8 下一步，当前断点如果是一个方法，不会进入方法体内；
2. `step into` F7 下一步，当前断点如果是一个方法，则进入方法体内，不过默认情情况下并不会进入 `java.*` 等类的方法中。如果需要进入，可以在 Setting -> Stepping 中设置,排除即可
3. `force step into` Alt+Shift+F7 下一步，当前行如果是一个方法，强制进入方法体内；
4. `step out` Shift+F8 跳出
5. `run to curser` Alt+F9 运行到鼠标所在行，临时设置断点，有用
6. `resume program` F9 运行到下一个断点处，是不是有点像 pdb 中的 c 快捷键；
7. `stop` Command+F2 停止
8. `view breakpoints` 查看所有断点，可以在运行时选择哪些断点暂停略过；
9. `mute breakpoints` 所有断点失效/生效的切换；

### 调试器是什么

debugger 调试器，“除虫器”，调试器是用来告诉 JVM，请你在具体的哪个断点处停下来

- 堆 Heap: 所有对象都在堆上分配
- 栈 Stack：先进后出 LIFO 后进先出，`Last In First Out`
- 线程：每个线程会有一个方法栈，出栈，后进先出，你自己的是 main 线程，观察这个方法栈即可；
- 栈帧 Stack Frame：方法栈中的每一个小方块，局部变量仅存在于方法的栈帧中，当方法运行完，栈帧就会被丢弃


可以修改执行结果，修改执行流程中的值：
- 右击调试器中的变量，`View/Edit Text`
- 右击调试器中的变量，`Set Value`

跳转到源代码
- 右击调试器中的变量，`Jump to Type Source`

### 条件断点

在循环里增加条件判断，具体操作：在断点处右击，在某个条件下，实施断点。

![](https://ws4.sinaimg.cn/large/006tNbRwly1fyq3mbopc0j30g206jaar.jpg)

查看表达式的值（可以查看已有变量的值）：

![](https://ws4.sinaimg.cn/large/006tNbRwly1fyq3sydbgpj30j10dj76c.jpg)

### 线程断点

我们可以在断点上使用鼠标右键弹出断点设置框，在 Suspend 选择 Thread 选项。断点的多线程属性可以设置默认属性，点击 Make Default 按钮，设置默认 Suspend 属性

### 调用栈

`ctrl+alt+h` 快捷键

参考：

- [IDEA的查询引用、调用关系图的功能](https://www.cnblogs.com/ghj1976/p/5382455.html)
- [Intellij IDEA神器常用技巧八-2018版本新增快捷键](https://my.oschina.net/Mkeeper/blog/1837432)
- [挖掘IntelliJ IDEA的调试功能](http://qinghua.github.io/intellij-idea-debug/)

### DEBUG 模式下提前返回，而不终止程序

在 Frames 窗口下，选择当前的 Frame，右击，选择 `Force Return`。

这个功能在调试的时候十分有用，既可节约运行的时间，避免程序重启，又可以避免程序走到后面不想调试的步骤去。

参考：
- [Intellij IDEA 在DEBUG模式下如何不执行后续代码直接停止程序？](https://segmentfault.com/q/1010000011000876)

### 查看继承关系

快捷键：`Ctrl+H`

- [IDEA查看接口或类的继承实现关系图](https://blog.csdn.net/Jae_Wang/article/details/80058541)
- [IntelliJ IDEA 中如何查看一个类的所有继承关系(当前类的所有继承关系图)](https://blog.csdn.net/qq_27093465/article/details/52857307)

### 设置程序环境变量

Run->Edit Configurations->Environment->Environment variables

![](https://ws2.sinaimg.cn/large/006tNbRwly1fya63mb1dzj30xq0c6whi.jpg)

```java
System.getenv("HOME_TEST");
```

## Maven 设置

不推荐使用 IDEA 自带的 Maven 配置。在 Settings -> Build Excution Deployment -> Build Tools -> Maven 中修改：

- `Maven home directory` maven 安装目录
- `User settings file` 勾选 Override，改为自己的 settings.xml 文件
- `Local repository` 依赖包存储位置

在 Settings -> Build Excution Deployment -> Build Tools -> Maven -> Importing 中：

- 勾选 `Import Maven projects automatically` pom 文件增加了新依赖，会自动下载；

## 高效定位代码-无处不在的跳转

### project之间跳转

`ctrl+alt+左右方括号`：

![](https://ws3.sinaimg.cn/large/006tNbRwly1fya57dy739j309u07wn05.jpg)

### 查看快捷键

`ctrl+shift+a`：搜搜快捷键

![](https://ws2.sinaimg.cn/large/006tNbRwly1fya59cv3pwj30bu05iq5q.jpg)

### 查看最近文件

`ctrl+e`
可以在`ctrl+shift+a`：搜索reccent ...

### 快速跳转到最后修改的地方

`ctrl+shift+backspace` 快速跳转到最后修改的地方

![](https://ws3.sinaimg.cn/large/006tNbRwly1fya5ck67t0j309b0d6gry.jpg)

### 利用书签跳转

![](https://ws3.sinaimg.cn/large/006tNbRwly1fya5e3cuvmj30ik07i3yz.jpg)

![](https://ws4.sinaimg.cn/large/006tNbRwly1fya5f37d10j305u07vgls.jpg)

- `F11`：添加、取消书签
- `ctrl+f11`：添加、取消有标记的书签
- `ctrl+<n>`：跳转到有标记的书签处

### 添加收藏 Favorites

`alt+shift+f`：add to favorities

![](https://ws3.sinaimg.cn/large/006tNbRwly1fya5h1gwvhj30if059wes.jpg)

### 编辑区和文件区的跳转

`alt+1`进入文件区
`esc`：进入编辑区

## 精准搜索

### 类搜索

`ctrl+n`：快速搜索类

![](https://ws4.sinaimg.cn/large/006tNbRwly1fya5iswva8j309606qad5.jpg)

### 文件搜索

快速打开指定的文件，方便★★★：
`ctrl+shift+n`

### 函数搜索-symbol

`ctrl+shift+alt+n`

### 搜索 Maven 依赖包中的内容

有时候可能需要在源码分析时想要搜索 Maven 依赖包中的内容，只要如下设置即可扩大搜索范围：

`ctrl+shift+f` ->`scope`->`All Places`

![](https://ws1.sinaimg.cn/large/6d9475f6ly1g34o5yp49vj211a0g0wix.jpg)

## 重构

- `Shift+F6`  重命名
所有的文件，类名，函数名，属性名都可以重命名，值得点赞的是，只要你使用 `Shift+F6` 重命名，所有使用过这个名称的地方都会跟着改变；

- 函数的重构，`Ctrl+F6` 重构函数
当你需要重构方法时，无论是增加参数，修改返回值，还是更改函数实现，只需要Ctrl+F6，就可以把所有用到此函数的地方一起重构

参考：

- [你们都在用IntelliJ IDEA吗？或许你们需要看一下这篇博文](http://www.cnblogs.com/clwydjgs/p/9390488.html#!comments)

## 列操作

- 选中单词转大写

![](https://ws4.sinaimg.cn/large/006tNbRwly1fya5neqiudj30a00f0dnp.jpg)

### 多光标选中

- 多光标
![](https://ws2.sinaimg.cn/large/006tNbRwly1fya5oimsitetj30lj0hq180.jpg)

按住 `Alt` 键，可以快速实现多光标、块选择。

## 界面中的基本功能

如果没有如老师那样的窗口，可点击`view->tool buttons`打开

### `alt+<n>`快捷键 ★★★

如下图圈出的数字 n，可以使用`alt+<n>`的方式快捷使用，替代鼠标操作。

注意，Mac  使用的是 `command+<n>`方式；

![](https://ws2.sinaimg.cn/large/006tNbRwly1fya5prmf14j30gz0fnabn.jpg)

#### 打开收藏 ★★★

在一些调试的函数那里打断点，然后`alt+7`收藏中就会方便的查看；

#### `TODO`

在项目中使用`# TODO`，可以在`Alt+6`中看到哪些待开发的备注

## 必备插件

官网插键库：https://plugins.jetbrains.com/

插键说明：

- Install JetBrains plugin：弹出IntelliJ IDEA 公司自行开发的插件仓库列表，供下载安装。
- Browse repositories：弹出插键仓库中所有插键列表供下载安装。
- Install plugin from disk：浏览本地的插键文件进行安装。

推荐插键：

- BashSupport
- [Dash](https://zealdocs.org/): `ctrl+shift+h`
- [Key Promoter X](https://plugins.jetbrains.com/plugin/9792-key-promoter-x) 快捷键提示
- [Statistic](https://plugins.jetbrains.com/plugin/4509-statistic) 代码统计
- [CheckStyle-IDEA](https://plugins.jetbrains.com/plugin/1065-checkstyle-idea) 代码样式检查
- Lombok plugin
- [FindBugs-IDEA](https://plugins.jetbrains.com/plugin/3847-findbugs-idea) 代码 Bug 检查
- Mongo
- [Material Theme](https://plugins.jetbrains.com/plugin/8006-material-theme-ui) 主题插键
- CamelCase 将不是驼峰格式的名称，快速转成驼峰格式，安装好后，选中要修改的名称，按快捷键 `shift+alt+u`。
- Rainbow Brackets 彩虹括号
- [Alibaba Java Coding Guidelines](https://www.cnblogs.com/jajian/p/8081658.html)
- Maven Helper：安装之后，直接打开pom文件，即可查看依赖数，还能自动分析是否存在 jar 包冲突，查看[idea 中解决maven 包冲突的问题](https://blog.csdn.net/sunpeng_sp/article/details/77393348)
- String Manipulation：字符串转换工具，安装好插件后，选中需要处理的内容后，按快捷键 `Alt+m`，即可弹出工具功能列表
- AceJump：允许您快速将光标导航到编辑器中可见的任何位置，只需点击`ctrl +;`，然后输入一个你想要跳转到的字符，之后键入匹配的字符就跳转到你想要挑战的地方了
- activate-power-mode：写代码的时候，就会附加一些狂拽炫酷屌炸天的效果

参考

- [hollis-IntelliJ IDEA 18 周岁，吐血推进珍藏已久的必装插件](https://www.hollischuang.com/archives/3220)

## FAQ

### Q1: intellj Idea中给新建的项目导入jar包？

- [推荐intellij idea Project Structure 讲解](https://www.cnblogs.com/zadomn0920/p/6196962.html)

### Q2: IDEA如何快速生成Get/Set方法？

- [Intellij IDEA 生成Get/Set方法快捷键](http://blog.csdn.net/qq_34273222/article/details/60577679)

`Alt + Insert` 组合，可唤出选择面板

### Q3: 找不到提示

[IntelliJ cannot find any declarations](https://stackoverflow.com/questions/37282285/intellij-cannot-find-any-declarations)

- Right-click src folder
- Mark Directory as > Sources Root

### Q4: IDEA编译通过能运行但是出现红色下划线

原因就是可能没有清除原来的历史缓存，导致一些错误，解决方法是：File-Invalidate Caches，然后重启IDEA

## 最后

近期由于微博图床挂了，所以，截图都看不了了，点击原文链接，可查看图片：

- [PyCharm/IDEA 使用技巧总结](https://michael728.github.io/2019/05/11/tools-dev-pycharm-idea/)

## 参考

- [IntelliJ IDEA 使用教程](http://wiki.jikexueyuan.com/project/intellij-idea-tutorial/keymap-introduce.html)
- [简书-IntelliJ IDEA 教程 技巧篇](https://www.jianshu.com/p/8b4ee9499a79) 作者写了一系列的总结
- [你有哪些想要分享的 PyCharm 使用技巧？](https://www.zhihu.com/question/37787004)
- [IntelliJ IDEA神器使用技巧](http://www.imooc.com/learn/924 )
- [Java人员正确使用 IntelliJ IDEA的方式](http://tengj.top/2017/02/22/idea1-1/)
- [挖掘IntelliJ IDEA的调试功能](http://qinghua.github.io/intellij-idea-debug/)
- [IDEA 注册-lanyu](http://idea.lanyus.com/)
- [Github-judasn/IntelliJ-IDEA-Tutorial](https://github.com/judasn/IntelliJ-IDEA-Tutorial/blob/master/keymap-introduce.md)
