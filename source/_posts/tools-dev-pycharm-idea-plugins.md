---
title: JetBrains PyCharm/IDEA 常用插键总结
date: 2020-05-05 22:30:02
tags:
  IDE
  IDEA
  Pycharm
categories: ToolsDev
keywords: [pycharm, idea, plugin]
---

> 工欲善其事必先利其器

## 前言

IDEA 有用一套非常强大的[插件市场](https://plugins.jetbrains.com/)，提供了丰富的插键帮助开发者提升开发效率。之前总结过一篇[博文](https://michael728.github.io/2019/05/11/tools-dev-pycharm-idea/)，渐渐觉得有必要写一篇文章单独罗列一下 IDEA 常用的插键！

## 主题

[IDEA主题-](http://riaway.com/) `Settings->Editor->Color Scheme->Import Scheme`，导入 jar 包即可。这种方式安装主题略微麻烦，其实在应用市场里提供了很多主题可以直接安装的。
[Dracula](https://draculatheme.com/) 在 IDEA 中，可以像安装插键一样搜索该主题进行安装。吸血鬼主题插件，各 IDE 都有适配的主题，非常推荐！
[Material Theme](https://plugins.jetbrains.com/plugin/8006-material-theme-ui) 主题插键

## 安装说明

`Install JetBrains plugin`：弹出IntelliJ IDEA 公司自行开发的插件仓库列表，供下载安装。
`Browse repositories`：弹出插键仓库中所有插键列表供下载安装。
`Install plugin from disk`：浏览本地的插键文件进行安装。

## 插键列表

Lombok 当你使用 lombok 包进行开发时，要同时安装该插键才行！笔者近期就因为升级 IDEA 重装之后忘记安装该插键，导致代码报红问题，半天才发现因为该插键忘记重新安装了。

---

[TestMe](https://github.com/wrdv/testme-idea) Junit 等测试工具的生成插件,支持 `Junit4/5,TestNG,Spock` 等测试框架代码的生成

### 数据库相关

[Free MyBatis plugin](https://plugins.jetbrains.com/plugin/8321-free-mybatis-plugin) 快速从代码跳转到 mapper 及从 mapper 返回代码

---

[Mybatis Log](https://blog.csdn.net/qq_22194659/article/details/89011988) 
 在控制台打印mybatis的脚本日志，需要配置才能在控制台输出，[参考](https://blog.csdn.net/VICHOU_FA/article/details/79285749)


----

Mongo

### 开发辅助

[Codota](https://www.codota.com/) 可以搜索知名开源项目中该类或者方法的使用案例.写代码的时候，某个类不熟悉用法，右键选择”Get relevant examples“

比如我们想了解：SerializationUtils 工具类的用法，也可以直接在网址上搜索即可，网址：https://www.codota.com/code，选择对应的包名

---

[Dash](https://zealdocs.org/): `ctrl+shift+h`

---

CamelCase：将不是驼峰格式的名称，快速转成驼峰格式，安装好后，选中要修改的名称，按快捷键 `Shift+Alt+U`

---

String Manipulation：字符串转换工具，安装好插件后，选中需要处理的内容后，按快捷键 `Alt+m`，即可弹出工具功能列表

### 编码规范

[Alibaba Java Coding Guidelines](https://www.cnblogs.com/jajian/p/8081658.html) 编码规范检查

`Command + F1` 会更完整的告诉你为什么不可以，同时还给出你应该怎样编写的样例

---

[CheckStyle-IDEA](https://plugins.jetbrains.com/plugin/1065-checkstyle-idea) 代码样式检查

---

[FindBugs-IDEA](https://plugins.jetbrains.com/plugin/3847-findbugs-idea) 代码 Bug 检查

### 调试

[Java Stream Debugger](https://plugins.jetbrains.com/plugin/9696-java-stream-debugger) 可以查看stream中间操作的数据状态

---

[jclasslib](https://my.oschina.net/leon1314/blog/1817046) IDEA 如何使用jclasslib 插件查看 Java 字节码

---

VisualVM Launcher 对于经常实用 visualvm 进行性能调优和 debug 的场景，这个快速启动 visualvm 并打开正在调试的应用的按钮。

### 使用技巧

[Key Promoter X](https://plugins.jetbrains.com/plugin/9792-key-promoter-x) 快捷键提示

---

IDE Feature Trainer 安装之后，可以在 `View > Tool Windows > Learn` 里打开练习页面，了解 IDEA 的快捷键。


### 界面优化

Rainbow Brackets 彩虹括号，这个插件方便查看括号的匹配
- ctrl+鼠标右键点击 所在区域高亮
- alt+鼠标右键点击 非所选区域外不高亮

---

CodeGlance 可以在编辑区的右边增加一个类似 VSCode 的缩略图的效果，拖动比较方便。

### 辅助功能

[Statistic](https://plugins.jetbrains.com/plugin/4509-statistic) 代码统计

---

Maven Helper：安装之后，直接打开pom文件，即可查看依赖数，还能自动分析是否存在 jar 包冲突，查看[idea 中解决maven 包冲突的问题](https://blog.csdn.net/sunpeng_sp/article/details/77393348)

----

[GitToolBox](https://github.com/zielu/GitToolBox) Git 功能增强，比如可以在状态栏显示当前光标所在行的修改者

---

Restful Tookit：编写 RESTful 接口必不可少的插件，编写完接口当然还需要调试，可以搭配HTTP Client 一起使用

### 语言支持

BashSupport



### 花里胡哨

activate-power-mode：写代码的时候，就会附加一些狂拽炫酷屌炸天的效果

---

AceJump：允许您快速将光标导航到编辑器中可见的任何位置，只需点击`ctrl +;`，然后输入一个你想要跳转到的字符，之后键入匹配的字符就跳转到你想要挑战的地方了

## 参考

[那些提高效率的Idea插件](https://blog.imcompany.cn/post/na-xie-ti-gao-xiao-lu-de-ideacha-jian/)


