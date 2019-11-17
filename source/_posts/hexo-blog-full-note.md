---
title: 利用 Hexo + Github Pages 搭建博客并优化 Next 主题教程
date: 2019-05-19 10:10:51
tags:
  - Hexo
  - Blog
  - Note
keywords:
  - blog
  - github
  - hexo
  - next
  - 优化
categories:
- ToolsDaily
---

## 概述

本文总结一下 Hexo 搭建博客的主要流程，能够在日后快速恢复博客环境。

Hexo 的官方文档写的已经很全面了，本文仅将一些注意点列出。

安装 Hexo 的文档参考：
- [官宣-Hexo 文档](https://hexo.io/zh-cn/docs/index.html)

## 前提

安装 Hexo 之前，你的电脑上需要有
- Node.js
- Git

如果是 Mac 用户， 您在编译时可能会遇到问题，请先到 App Store 安装 Xcode，Xcode 完成后，启动并进入 `Preferences -> Download -> Command Line Tools -> Install` 安装命令行工具

## 安装 Hexo

全局安装 `hexo-cli`：
```
npm install -g hexo-cli
```

如果遇到权限的问题，可以使用如下命令：
```
sudo chown -R $(whoami) /usr/local/lib/node_modules/
```

设置 `/usr/local/lib/node_modules/` 文件夹属于当前用户。

此外，如果你要使用 root 权限安装的话，可以使用 `sudo -s` 切为 root 用户进行相关操作。这么做的话，会将一些文件夹的默认属组设置为 root 了，不推荐。

npm 权限问题：
- [Global installs (sudo npm i -g) fail on Mac after 6.5 upgrade. Works fine after 6.4.1 downgrade](https://npm.community/t/global-installs-sudo-npm-i-g-fail-on-mac-after-6-5-upgrade-works-fine-after-6-4-1-downgrade/4082)
- [npm，yarn如何查看源和换源](https://zhuanlan.zhihu.com/p/35856841)

## 建站

```
hexo init MyBlog
cd MyBlog
npm install
```

执行完毕之后，博客其实已经 OK 了：

```
hexo g
hexo s
```

执行上面命令即可本地预览博客内容了。

## 部署

[官宣-站点配置文件](https://hexo.io/zh-cn/docs/configuration) 内容中介绍了很多字段的用途

Hexo 提供快速一键部署，将博客部署到 GIthub Pages。

安装 `hexo-deployer-git`：
```
$ cd MyBlog
$ npm install hexo-deployer-git --save
```

修改站点配置文件中的配置：
```
# npm install hexo-deployer-git --save
deploy:
- type: git
  repo: git@github.com:Michael728/michael728.github.io.git
  branch: master
```

执行部署：
```
hexo d
```

## 常用命令

[hexo 常用指令](https://hexo.io/zh-cn/docs/commands)

```
hexo version # 显示hexo版本
hexo new <title> # 创建新文章
hexo g/generate # 生成静态文件
hexo clean # 清除缓存文件和已生成的静态文件（public）
hexo server # 启动本地服务器
hexo d/deploy # 部署网站
hexo list <type> # 列出网站资料
```

npm 常用指令
```
npm install 模块名 -g --save # g全局安装 save安装包信息将加入到dependencies（生产阶段的依赖）
npm install gulp@3.9.1 # 指定版本
npm install # 该命令可以根据dependencies配置安装所有的依赖包
npm update [-g] 模块名
npm outdated 模块名 # 检查模块是否过时
npm ls -g # 查看全局安装的模块
npm uninstall 模块名
npm info hexo-cli # 查看hexo-cli模块的信息
```

- [npm常用命令](https://www.jianshu.com/p/087d839e1d0c)
- [【原】npm 常用命令详解](http://www.cnblogs.com/PeunZhang/p/5553574.html)

## Hexo 写作技巧
- [Sanarous-hexo博客Next主题进阶写作技巧](https://bestzuo.cn/posts/3147047336.html)
- [NexT 主题内置标签](https://blog.lihj.me/post/next-tag-plugin-instruction.html)
- [Hexo 搭建个人博客系列：写作技巧篇](http://yearito.cn/posts/hexo-writing-skills.html)

### Note

语法：
```
{% note [class] [no-icon] %}
这里写你需要写的内容
{% endnote %}

// 注意上面的class和no-icon属性是可以选择的
[class]   : default | primary | success | info | warning | danger.
[no-icon] : Disable icon in note.
```

示例：
```
{% note success no-icon %}
success形式的类别
{% endnote %}
```

效果：
{% note success no-icon %}
success形式的类别
{% endnote %}

这里使用 `no-icon` 表示不显示图标，可选，默认会带有图标。

### 使用主题自带 FontAwesome 图标

源码：
```
- <i class="fa fa-pencil"></i> 铅笔
- <i class="fa fa-cloud-upload"></i> 上传
- <i class="fa fa-download"></i> 下载
```

效果：
- <i class="fa fa-pencil"></i> 铅笔
- <i class="fa fa-cloud-upload"></i> 上传
- <i class="fa fa-download"></i> 下载

### 文本居中
源码：
```
{% cq %}
人生乃是一面镜子，
从镜子里认识自己，
我要称之为头等大事，
也只是我们追求的目的！
{% endcq %}
```

效果：

{% cq %}
人生乃是一面镜子，
从镜子里认识自己，
我要称之为头等大事，
也只是我们追求的目的！
{% endcq %}

### 主题自带 label 标签

首先在主题 `_config.xml` 配置：
```
# Label tag.
label: true
```

源码：
```
{% label primary@primary内容 %}
```

效果：
{% label primary@primary内容 %}

### 主题自带 tabs 标签

主题配置文件：

```
# Tabs tag
tabs:
  enable: true
  transition:
    tabs: true
    labels: true
  border_radius: 0
```

语法：
```
{% tabs [Unique name], [index] %}
<!-- tab [Tab caption]@[icon] -->
Any content (support inline tags too).
<!-- endtab -->
{% endtabs %}
```

- `Unique name` 为每个标签页组唯一的名称
- `index` 为初始激活的标签页
- `Tab caption` 为标签页名称，不指定时为所属标签页组名称加索引
- `icon` 为 `Font awesome图标`，可选

源码示例：
```
{% tabs 选项卡, 2 %}
<!-- tab -->
这是选项卡 1 内容
<!-- endtab -->
<!-- tab -->
这是选项卡 2 内容
<!-- endtab -->
<!-- tab -->
这是选项卡 3** 哇，你找到我了！φ(≧ω≦*)♪～
<!-- endtab -->
{% endtabs %}
```

效果：
{% tabs 选项卡, 2 %}
<!-- tab -->
这是选项卡 1 内容
<!-- endtab -->
<!-- tab -->
这是选项卡 2 内容
<!-- endtab -->
<!-- tab -->
这是选项卡 3** 哇，你找到我了！φ(≧ω≦*)♪～
<!-- endtab -->
{% endtabs %}

### 主题自带样式按钮

源码：
```
{% btn https://www.baidu.com, 点击下载百度, download fa-lg fa-fw %}
```

效果：
{% btn https://www.baidu.com, 点击下载百度, download fa-lg fa-fw %}

## Hexo 其他知识

### 图床

由于一直使用的是「微博图床」，渣浪微博和简书开启了图片防盗链，请大家在博客中加入以下标签来绕过：

```html
<meta name="referrer" content="never" /> ​​​​
```

Next 主题只需要在 `themes/next/layout/_partials/head/head.swig` 中添加上面一行内容即可。


## Next 主题

- [theme-next/hexo-theme-next](https://github.com/theme-next/hexo-theme-next/blob/master/docs/zh-CN/README.md)

### 安装 Next
```
$ cd MyBlog
$ git clone https://github.com/theme-next/hexo-theme-next themes/next
```

Next 扩展都安装在`themes/next/source/lib`文件夹下

```
cd MyBlog
git clone https://github.com/theme-next/hexo-theme-next themes/next
```

### 启动 Next 主题

编辑站点配置文件 `_config.yml`：
```
theme: next # 启用next主题
```

### 主题设置

- [next-开始使用](http://theme-next.iissnan.com/getting-started.html)

### 设置语言

站点配置文件：
```
language: zh-CN # 最新版中，不再使用zh-Hans
```

### 设置菜单

编辑主题配置文件：

```
menu:
  home: / || home
  #about: /about/ || user
  #tags: /tags/ || tags
  #categories: /categories/ || th
  archives: /archives/ || archive
  #schedule: /schedule/ || calendar
  #sitemap: /sitemap.xml || sitemap
  #commonweal: /404/ || heartbeat
```

菜单内容的设置格式是：`item name: link || icon`。其中 `item name 是一个名称，这个名称并不直接显示在页面上，她将用于匹配图标以及翻译。

设置菜单项的显示文本。在第一步中设置的菜单的名称并不直接用于界面上的展示。Hexo 在生成的时候将使用 这个名称查找对应的语言翻译，并提取显示文本。这些翻译文本放置在 NexT 主题目录下的 `languages/{language}.yml` （`{language}` 为你所使用的语言）。

icon 使用的是 [fontawesome](https://fontawesome.com/icons) 图标，严格区分大小写。

### 设置头像

将准备好的头像放置在主题目录下的 `source/images/` 中。

```
avatar:
  # In theme directory (source/images): /images/avatar.gif
  # In site directory (source/uploads): /uploads/avatar.gif
  # You can also use other linking images.
  url: /images/logo.jpg
```

### 百度统计

1. 登录[百度统计](http://tongji.baidu.com/)， 定位到站点的[代码获取页面](https://tongji.baidu.com/sc-web/18360470/home/site/getjs?siteId=12775404)
2. 复制 hm.js? 后面那串统计脚本 id，如：
3. 编辑 主题配置文件， 修改字段 `baidu_analytics` 字段，值设置成你的百度统计脚本 id

### 阅读次数

使用 [LeanCloud](https://leancloud.cn/dashboard/applist.html#/apps)

参考这个 [为NexT主题添加文章阅读量统计功能](https://notes.wanghao.work/2015-10-21-%E4%B8%BANexT%E4%B8%BB%E9%A2%98%E6%B7%BB%E5%8A%A0%E6%96%87%E7%AB%A0%E9%98%85%E8%AF%BB%E9%87%8F%E7%BB%9F%E8%AE%A1%E5%8A%9F%E8%83%BD.html#%E9%85%8D%E7%BD%AELeanCloud)

```
leancloud_visitors:
  enable: true # 启用了 valine，所以这里可以置为 false
  app_id: xxx
  app_key: xxx
```

### 设置 RSS

主题配置文件中，`rss` 字段设为空，启用，设为 `false` 则不启用。启用的话，需要安装插键：
```
npm install hexo-generator-feed --save
```

### 添加标签页面

1. 新建页面 `hexo new page tags`
2. 设置页面类型，编辑刚新建的页面，将页面的类型设置为 tags ，主题将自动为这个页面显示标签云
```
title: 标签
date: 2014-12-22 12:39:04
type: "tags"
---
```

如果有集成评论服务，页面也会带有评论。 若需要关闭的话，请添加字段 comments 并将值设置为 false，如：
```
title: 标签
date: 2014-12-22 12:39:04
type: "tags"
comments: false
---
```

3. 修改菜单，在菜单中添加链接。编辑 主题配置文件 ， 添加 tags 到 menu 中

标签示例：
```
title: 标签测试文章
tags:
  - Testing
  - Another Tag
```

请参阅 [Hexo 的分类与标签文档](https://hexo.io/zh-cn/docs/front-matter.html#%E5%88%86%E7%B1%BB%E5%92%8C%E6%A0%87%E7%AD%BE)，了解如何为文章添加标签或者分类

补充：
```
categories:
- Diary
tags:
- PS3
- Games
```

分类方法的分歧
如果您有过使用WordPress的经验，就很容易误解Hexo的分类方式。WordPress支持对一篇文章设置多个分类，而且这些分类可以是同级的，也可以是父子分类。但是Hexo不支持指定多个同级分类。下面的指定方法：
```
categories:
- Diary
- Life
```
会使分类Life成为Diary的子分类，而不是并列分类。因此，有必要为您的文章选择尽可能准确的分类。

并列分类，了解一下：
```
categories:
- [Linux]
- [Tools]
```

并列+子分类，再了解一下：
```
categories:
- [Linux, Hexo]
- [Tools, PHP]
```

同样的方法，添加分类页面：
```
$ hexo new page categories
```

### 设置字体

设置字体可参考:[官宣-字体](https://theme-next.iissnan.com/theme-settings.html#fonts-customization)

### 设置代码高亮主题

[代码高亮预览](https://theme-next.iissnan.com/theme-settings.html#syntax-highlight-scheme)

```
# Code Highlight theme
# Available value: normal | night | night eighties | night blue | night bright
# https://github.com/chriskempson/tomorrow-theme
highlight_theme: night eighties
```

### 侧边栏社交链接

主题配置文件：
```
social:
  GitHub: https://github.com/Michael728 || github
  微博: https://weibo.com/1838446070/profile?topnav=1&wvr=6 || weibo
  博客园: https://www.cnblogs.com/michael-xiang/ || globe
  知乎: https://www.zhihu.com/people/michaelXoX || globe
  Twitter: https://twitter.com/728_michael || twitter
  Telegram: https://t.me/michaelxiang || telegram
```

图标名称 是 Font Awesome 图标的名字（不必带 `fa-` 前缀）。 enable 选项用于控制是否显示图标，你可以设置成 false 来去掉图标

```
social_icons:
  enable: true
  icons_only: false
  transition: false
```

### 开启打赏功能

只需要 主题配置文件 中填入 微信 和 支付宝 收款二维码图片地址 即可开启该功能：
```
reward_settings:
  # If true, reward would be displayed in every article by default.
  # You can show or hide reward in a specific article throuth `reward: true | false` in Front-matter.
  enable: true
  animation: false
  comment: 我知道是不会有人点的，但万一有人想不开呢👇

reward:
  wechatpay: /images/wechatpay.jpg
  alipay: /images/alipay.png
```

### 友情链接

```
# Blog rolls
links_icon: link
links_title: 关注列表 #Links
links_layout: block
#links_layout: inline
links:
  #Title: http://example.com
  Blog List: https://michael728.github.io/2018/09/16/blog-interesting/
  ZeeCoder: https://zcheng.ren/posts/
  ehlxr: https://ehlxr.me/
  卡瓦邦噶: https://www.kawabangga.com/
  David Dai: https://blog.stdioa.com/
  建站日志: https://michael728.github.io/2016/09/03/blog-logfile/
```

### 站点建立时间

这个时间将在站点的底部显示，例如 `©2013 - 2015`。 编辑 主题配置文件，新增字段 since：

### 订阅微信公众号

```
# Wechat Subscriber
wechat_subscriber:
  enabled: true
  qcode: /uploads/wechat-qcode.jpg
  description: 欢迎您扫一扫上面的微信公众号，订阅我的博客！
```

### 设置动画效果

主题配置文件设置。比较在乎速度，可以关闭动画。

```
# Use velocity to animate everything.
motion:
  enable: true
  async: false
  transition:
    # Transition variants:
    # fadeIn | fadeOut | flipXIn | flipXOut | flipYIn | flipYOut | flipBounceXIn | flipBounceXOut | flipBounceYIn | flipBounceYOut
    # swoopIn | swoopOut | whirlIn | whirlOut | shrinkIn | shrinkOut | expandIn | expandOut
    # bounceIn | bounceOut | bounceUpIn | bounceUpOut | bounceDownIn | bounceDownOut | bounceLeftIn | bounceLeftOut | bounceRightIn | bounceRightOut
    # slideUpIn | slideUpOut | slideDownIn | slideDownOut | slideLeftIn | slideLeftOut | slideRightIn | slideRightOut
    # slideUpBigIn | slideUpBigOut | slideDownBigIn | slideDownBigOut | slideLeftBigIn | slideLeftBigOut | slideRightBigIn | slideRightBigOut
    # perspectiveUpIn | perspectiveUpOut | perspectiveDownIn | perspectiveDownOut | perspectiveLeftIn | perspectiveLeftOut | perspectiveRightIn | perspectiveRightOut
    post_block: fadeIn
    post_header: slideDownIn
    post_body: slideDownIn
    coll_header: slideLeftIn
    # Only for Pisces | Gemini.
    sidebar: slideUpIn
```

### 设置背景动画
编辑 主题配置文件， 搜索 `canvas_nest` 或 `three_waves`，根据您的需求设置值为 true 或者 false 即可

我只启用了 canvas_nest

```
  canvas_nest: //cdn.jsdelivr.net/gh/theme-next/theme-next-canvas-nest@1.0.0/canvas-nest.min.js
  canvas_nest_nomobile: //cdn.jsdelivr.net/gh/theme-next/theme-next-canvas-nest@1/canvas-nest-nomobile.min.js
```

### 搜索--Local Search

```
$ npm install hexo-generator-searchdb --save
```

站点配置文件，新增如下内容：
```
search:
  path: search.xml
  field: post
  format: html
  limit: 10000
```

编辑主题配置文件，启用本地搜索：
```
# Local search
local_search:
  enable: true
```

### 不蒜子统计

编辑 主题配置文件 中的 `busuanzi_count` 的配置项：
```
busuanzi_count:
  enable: true
  total_visitors: true
  total_visitors_icon: user
  total_views: true
  total_views_icon: eye
  # 文章阅读次数，关闭，避免与leanclound_visitors冲突
  post_views: false
  post_views_icon: eye
```

### fancybox
{%  note info %}
fancyBox is a tool that offers a nice and elegant way to add zooming functionality for images, html content and multi-media on your webpages
{% endnote %}


- 检查`_config.yml` 中是否开启了 fancybox
- 找到 vendors，把 `fancybox` 和 `fancybox_css` 设置成推荐值

参考：
- [文章里面的图片点击没有反应，也没有放大效果 [solved]](https://github.com/theme-next/hexo-theme-next/issues/593)

### 内容分享

[theme-next-needmoreshare2](https://github.com/theme-next/theme-next-needmoreshare2)：
```
cd themes/next
git clone https://github.com/theme-next/theme-next-needmoreshare2 source/lib/needsharebutton
```

主题配置文件配置：
```
needmoreshare2:
  enable: true
  postbottom:
    enable: true
    options:
      iconStyle: box
      boxForm: horizontal
      position: bottomCenter
      networks: Weibo,Wechat,Douban,QQZone,Twitter,Facebook
  float:
    enable: false
    options:
      iconStyle: box
      boxForm: horizontal
      position: middleRight
      networks: Weibo,Wechat,Douban,QQZone,Twitter,Facebook
```

### 评论

Next 评论配置很简单，支持很丰富。

`front matter` 中设置 `comments: false`，当前页就不会显示评论框了。

#### Disqus 评论启用，但是得网络畅通才能看到：
```
disqus:
  enable: true
  shortname: xxx
  count: true
```

编辑 主题配置文件， 将 disqus 下的 enable 设定为 true，同时提供您的 shortname。count 用于指定是否显示评论数量

#### Valine

[Valine](https://valine.js.org/avatar.html)这个评论系统也是用到了 [leancloud](https://leancloud.cn/dashboard/app.html?appid=7CDtrbb4U7nQzOdctmXXnMqt-gzGzoHsz#/general)

- 点击登录或注册Leancloud;
- 创建应用(如果已经创建了博客应用，就不用再创建了)
- 获取 APP ID 和 APP KEY: 刚刚创建的应用 > 设置 > 应用 KEY

```
valine:
  enable: false # When enable is set to be true, leancloud_visitors is recommended to be closed for the re-initialization problem within different leancloud adk version.
  appid:  xxx # your leancloud application appid
  appkey:  xxx # your leancloud application appkey
  notify: true # mail notifier , https://github.com/xCss/Valine/wiki https://valine.js.org/notify.html
  verify: true # Verification code
  placeholder: ヾﾉ≧∀≦)o来啊，快活啊!  # comment box placeholder
  avatar: identicon # wavatar retro gravatar style https://github.com/xCss/Valine/wiki/avatar-setting-for-valine
  guest_info: nick,mail,link # custom comment header
  pageSize: 10 # pagination size
  visitor: true # leancloud-counter-security is not supported for now. When visitor is set to be true, appid and appkey are recommended to be the same as leancloud_visitors' for counter compatibility. Article reading statistic https://valine.js.org/visitor.html
```

- visitor 文章访问量统计。
- recordIP，默认是 fasle，是否记录评论者IP
- [valine 配置项查看](https://valine.js.org/configuration.html)

头像设置，可以用你对应的邮箱设置 [Gravatar](http://cn.gravatar.com/)，这样你评论时，就是注册时使用的邮箱了。

邮件提醒，进入Leancloud>选择你的评论所存放的应用>设置>邮件模板，按下图设置好用于重置密码的邮件主题>然后保存:
```
<p>Hi, {{username}}</p>
<p>
你在 {{appname}} 的评论收到了新的回复，请点击查看：
</p>
<p><a href="https://michael728.github.io/" style="display: inline-block; padding: 10px 20px; border-radius: 4px; background-color: #3090e4; color: #fff; text-decoration: none;">马上查看</a></p>
```

参考：
- [valine-admin](https://github.com/zhaojun1998/Valine-Admin)
- [在 NexT 中使用 Valine 评论系统](https://io-oi.me/technology/computer/computer-aided-art/2018/07/15/use-valine-in-theme-next.html)

### hexo next 新增阅读排行页面

参考这两个教程：

- [hexo next 新增阅读排行页面](https://hoxis.github.io/hexo-next-read-rank.html)
- [打造个性超赞博客Hexo+NexT+GitHubPages的超深度优化](https://io-oi.me/technology/computer/computer-aided-art/2017/06/09/hexo-next-optimization.html)
- [在 NexT 中使用 Valine 评论系统](https://io-oi.me/technology/computer/computer-aided-art/2018/07/15/use-valine-in-theme-next.html)

### 在Hexo博客中加入豆瓣读书页面

[hexo-douban](https://github.com/mythsman/hexo-douban) 可以实现生成豆瓣读书、电影、游戏的页面。具体的使用方法可以看项目的主页或者这篇文章
- [在Hexo博客中加入豆瓣读书页面](https://bestzuo.cn/posts/hexo-douban.html)

```
hexo clean && hexo douban -bm && hexo g &&hexo deploy
```

### 看板娘

- [EYHN/hexo-helper-live2d](https://github.com/EYHN/hexo-helper-live2d) Hexo 博客照着配置就行
- [live2d-看板娘预览](https://huaji8.top/post/live2d-plugin-2.0/)
- [用Live2D让看板喵入住你的Hexo博客吧\(^o^)/~](https://11.tt/posts/2018/how-to-play-with-live2d-on-hexo/)
- [给博客添加能动的看板娘(Live2D)-关于模型的二三事](https://imjad.cn/archives/lab/add-dynamic-poster-girl-with-live2d-to-your-blog-01) Hexo 博客看板娘配置进阶
- [博客园添加Live2d看板娘](https://www.cnblogs.com/barryzhang/p/10535960.html) 博客园增加看板娘

### SEO 优化
- [Hexo博客Next主题SEO优化方法](https://hoxis.github.io/Hexo+Next%20SEO%E4%BC%98%E5%8C%96.html)
- [Hexo-Next搭建个人博客（SEO优化）](https://yfzhou.coding.me/2018/08/29/Hexo-Next%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%EF%BC%88SEO%E4%BC%98%E5%8C%96%EF%BC%89/)
- [Hexo+Next主题搭建博客安装美化及SEO优化指南](https://mtianyan.gitee.io/post/a625fa82.html)

## 最后

博客建完的地址：https://michael728.github.io/

## 参考

- [hexo：（三）hexo Next 主题下载和配置](https://chentging.github.io/2018/05/14/hexo%EF%BC%9A%EF%BC%88%E4%B8%89%EF%BC%89hexo-Next-%E4%B8%BB%E9%A2%98%E4%B8%8B%E8%BD%BD%E5%92%8C%E9%85%8D%E7%BD%AE/)
- [打造个性超赞博客Hexo+NexT+GitHubPages的超深度优化](https://reuixiy.github.io/technology/computer/computer-aided-art/2017/06/09/hexo-next-optimization.html)
- [手摸手教你用github和hexo搭建个人博客（五）](https://blog.runningcoder.me/2018/04/26/create-personal-blog-with-github-and-hexo-V/)
- [hexo的next主题个性化配置教程](https://segmentfault.com/a/1190000009544924#articleHeader23)
- [NexT 主题的扩展以及设置](https://iuok.me/posts/2911810154/)
- [Sanarous-使用Hexo+Next主题搭建自己的个人博客](https://bestzuo.cn/posts/blog-establish.html)