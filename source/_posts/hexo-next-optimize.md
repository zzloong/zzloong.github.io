---
title: Hexo之Next主题优化整理
tags: [Hexo]
toc: true
updated: 2017-05-25
date: 2015-11-30 16:01:18
categories: ToolsDev
---

搭建hexo的教程实在太多了，折腾了几天，总算搞定了，现在去看参考文章也是能看懂，不过，官方文档也写的相当详细：
+ [Hexo](https://hexo.io/)
+ [Next](http://theme-next.iissnan.com/)
+ [Yml语法验证](http://www.yamllint.com/)

<!-- more -->
## hexo主题
+ [hexo-theme](https://hexo.io/themes/)
+ [hexo-github-theme-list](https://github.com/hexojs/hexo/wiki/Themes)
+ [有那些好看的hexo主题？](http://www.zhihu.com/question/24422335)

## Next主题添加多说
新建站点时会创建多说域名：
创建站点完成后在 站点配置文件（不是主题配置文件）中新增 duoshuo_shortname 字段，值设置成上一步中的值。
duoshuo_shortname: michael-xox
其他主题主题添加多说，参考：
1. http://dev.duoshuo.com/threads/541d3b2b40b5abcd2e4df0e9
2. http://dev.duoshuo.com/docs/5016427f77cf5fa30500000e



## hexo命令
### 更新hexo：
`npm update -g hexo`

### 更新主题：
cd 到主题文件夹，执行命令：
`git pull`

### 更新插件：
`npm update`



因为重装系统的缘故，重新要配置一下环境。简要做个笔记。

## Windows下npm安装Hexo失败的解放方案
因为国外源网速不好的原因，安装hexo失败，可以采用如下方案：
**命令搞定HEXO!!!**
```
# 添加淘宝源
npm install -g cnpm --registry=https://registry.npm.taobao.org
# nrm类似包管理器
cnpm install nrm -g
nrm ls
# 使用淘宝
nrm use taobao
npm install -g hexo-cli

```
大功告成！

http://www.codes51.com/itwd/1327882.html
http://www.thinksaas.cn/ask/question/21934/

## 安装NEXT
```
git clone https://github.com/iissnan/hexo-theme-next themes/next
```
参考：[Next配置](http://zhiho.github.io/categories/)

## 优化
### 头像设置
编辑 站点配置文件，新增字段 avatar， 值设置成头像的链接地址。
avatar: http://i5.tietuku.com/0d972d2b106fc7ea.jpg

### 网站logo设置
1. 通过网站[favicon在线制作](http://tool.lu/favicon/)制作favicon图片，logo最好设置32*32。
2. next主题：将图片放在next主题source/images目录下
3. 在next主题配置文件中添加：`favicon: images/favicon.ico`

### 添加`关于`页面
`hexo new page "about"`
在source文件夹下就会有about文件夹，编辑index.md，然后进入主题的_config.yml中，menu下的#about注释去掉

### 添加`目录云`、`标签云`页面
```
hexo new page "tags"
hexo new page "categories"
```
然后设置同上，去掉主题配置文件中的注释，调整菜单顺序

### 设置侧边栏头像
在站点配置文件，不是主题配置文件中，添加：
```
avatar: url
#avatar: /uploads/avatar.jpg
```

### 设置网站图标Logo
1. 通过网站[favicon在线制作](http://tool.lu/favicon/)制作favicon图片，logo最好设置32*32。
2. next主题：将图片放在next主题source/images目录下
3. 在next主题配置文件中添加：`favicon: images/favicon.ico`
或者把`favicon.ico`放到主题文件夹source文件夹下就可以了-》`favicon: /favicon.ico`
```
# Put your favicon.ico into `hexo-site/source/` directory.
favicon: /images/favicon.ico
```

### 增加打赏功能
- [hexo博客Next主题添加打赏功能](http://thejojo87.com/2016/03/26/hexo%E5%8D%9A%E5%AE%A2Next%E4%B8%BB%E9%A2%98%E6%B7%BB%E5%8A%A0%E6%89%93%E8%B5%8F%E5%8A%9F%E8%83%BD/)
- [hexo next 主题添加打赏功能](https://huabuyu.net/2016/04/02/hexo-next-%E4%B8%BB%E9%A2%98%E6%B7%BB%E5%8A%A0%E6%89%93%E8%B5%8F%E5%8A%9F%E8%83%BD/)
- [实现网站的打赏功能](http://icehe.me/web/donate/)
- [为Hexo博客添加版权说明和打赏功能](http://colin1994.github.io/2016/06/02/hexo-copyright-and-donate/)

### 设置阅读次数
- [为NexT主题添加文章阅读量统计功能](https://notes.wanghao.work/2015-10-21-%E4%B8%BANexT%E4%B8%BB%E9%A2%98%E6%B7%BB%E5%8A%A0%E6%96%87%E7%AB%A0%E9%98%85%E8%AF%BB%E9%87%8F%E7%BB%9F%E8%AE%A1%E5%8A%9F%E8%83%BD.html#%E9%85%8D%E7%BD%AELeanCloud)
- [为博客文章添加阅读量统计功能-LeanCloud](https://notes.wanghao.work/2015-10-21-%E4%B8%BANexT%E4%B8%BB%E9%A2%98%E6%B7%BB%E5%8A%A0%E6%96%87%E7%AB%A0%E9%98%85%E8%AF%BB%E9%87%8F%E7%BB%9F%E8%AE%A1%E5%8A%9F%E8%83%BD.html#%E9%85%8D%E7%BD%AELeanCloud)

### 添加最近访客和多说
- [动动手指，NexT主题与Hexo更搭哦（基础篇）](http://www.arao.me/2015/hexo-next-theme-optimize-base/)
- [动动手指，给你的Hexo站点添加最近访客（多说篇）](http://www.arao.me/2015/hexo-next-theme-optimize-duoshuo/)

### SEO
- [Hexo NexT 主题SEO优化指南](http://lancelot_lewis.coding.me/2016/08/16/blog/Hexo-NexT-SEO/)
- [个人博客SEO实践](http://wanghuanming.com/2014/12/blog-seo/)
- [Hexo教程(四)-hexo博客被搜索引擎收录](http://www.troylc.cc/hexo/2016/06/16/Hexo-4.html)
- [Hexo+nexT主题搭建个人博客](http://www.wuxubj.cn/2016/08/Hexo-nexT-build-personal-blog/)

- 安装`sitemap`扩展：
```
npm install hexo-generator-sitemap --save
npm install hexo-generator-baidu-sitemap --save
```
- 在你的hexo站点的_config.yml添加下面的代码:
```
# hexo sitemap网站地图
sitemap:
  path: sitemap.xml
baidusitemap:
  path: baidusitemap.xml
```
注意：这个地方的空格要符合语法规范！
- [提交sitemap](https://www.google.com/webmasters/tools/message-view?hl=zh-CN&authuser=0&message-filter=all&grid.r=0&msgid=AB9YKzLzOOnXJLqMW25nn_Sp8ml0kwGpeV7HTAQeXsn2oVIP21PIqUTTTIRXj4UKbtPA26NVzS8aBHn8ka-A8h1USO5326aRuQ)
参考next主题官方解答：[添加 Google Webmaster tools 验证](https://github.com/iissnan/hexo-theme-next/wiki/%E6%B7%BB%E5%8A%A0-Google-Webmaster-tools-%E9%AA%8C%E8%AF%81)
- 配置成功后，hexo编译时会在hexo站点根目录生成sitemap.xml和baidusitemap.xml
其中sitemap.xml适合提交给谷歌搜素引擎，baidusitemap.xml适合提交百度搜索引擎。
其次，在robots.txt中添加下面的一段代码：
```
Sitemap: http://www.arao.me/sitemap.xml
Sitemap: http://www.arao.me/baidusitemap.xml
```
- 参考这篇文章[hexo干货系列：（六）hexo提交搜索引擎（百度+谷歌）](http://www.cnblogs.com/tengj/p/5357879.html) 提交sitemap.xml

### 添加友链
在主题配置文件 `_config.yml`中`Sidebar Settings`部分添加字段：
```
# Blogrolls
links_title: 友情链接
links_layout: inline
links_icon: link  # 设置图标
links:
  Michael翔: http://michaelxiang.me
```

### 添加搜索
- 安装`npm install generarot-search`
- 在站点配置文件，不是主题配置文件中，添加：
```
# 添加搜索
search:
  path: search.xml
  field: all
```
**注意：需要在站点配置文件中设置：`url: http://michaelxiang.me/`，否则搜索的结果点击链接，会跳转到错误页面。**

### 添加背景
- 参考[hexo引用自定义js文件和css样式](http://longhaoteng.com/2016/08/01/hexo%E5%BC%95%E7%94%A8%E8%87%AA%E5%AE%9A%E4%B9%89js%E6%96%87%E4%BB%B6%E5%92%8Ccss%E6%A0%B7%E5%BC%8F/
)，在[github资源中](https://github.com/LongHaoTeng/longhaoteng.github.io/tree/master/js/src)找到`particle.js`，将其下载到本地主题文件夹`D:\03TBP\TBP\BLOG\themes\next\source\js\src`类似这样的文件夹下。
- 然后在主题`layout/_layout.swig`中的最后`body`标签上添加`<script type="text/javascript" src="/js/src/particle.js"></script>`

## 参考
+ [Hexo博客优化 - Next主题个性化定制-推荐！！！](http://www.selfrebuild.net/2015/06/24/Github-Hexo-Next%E4%B8%BB%E9%A2%98%E4%B8%AA%E6%80%A7%E5%8C%96%E5%AE%9A%E5%88%B6/)
+ [hexo下新建页面下如何放多个文章？](http://www.zhihu.com/question/33324071/answer/58775540?group_id=654307162210365440#comment-106092511)
+ [玩转Hexo博客之Next](http://www.jianshu.com/p/f869d1940985)
+ [动动手指，不限于NexT主题的Hexo优化（SEO篇）](http://www.arao.me/2015/hexo-next-theme-optimize-seo/)
+ [博客推广——提交搜索引擎SEO](http://segmentfault.com/a/1190000002436096)
- [Next主题常见问题](https://github.com/iissnan/hexo-theme-next/wiki)
- [主题配置](http://theme-next.iissnan.com/theme-settings.html#author-sites)

----

2017-05-25更新：
# Hexo搭建个人博客-资料整理

标签（空格分隔）： 软件

---
## 安装准备
### Git配置SSH
- [git下载](https://git-scm.com/downloads)

安装完成之后，配置git环境[^1] [^2]：
```
git config --global user.name "username"
git config --global user.email "username@example.com"
```
[^1]:[Hexo 3.1.1 静态博客搭建指南](http://lovenight.github.io/2015/11/10/Hexo-3-1-1-%E9%9D%99%E6%80%81%E5%8D%9A%E5%AE%A2%E6%90%AD%E5%BB%BA%E6%8C%87%E5%8D%97/)

[^2]:[GitHub Pages + Hexo搭建博客](http://crazymilk.github.io/2015/12/28/GitHub-Pages-Hexo%E6%90%AD%E5%BB%BA%E5%8D%9A%E5%AE%A2/)

生成公钥：
```
# C:\Users\xiang\.ssh
ssh-keygen -t rsa -C "username@example.com"
```
然后在`C:\Users\michael\.ssh`文件夹下查看`id_rsa.pub`,复制全部内容，添加到github账户中：
![](http://ww1.sinaimg.cn/large/6d9475f6ly1fez0wzpondj20rj0l50ux.jpg)

验证成功
```
ssh -T git@github.com
```
设置，下次部属时不用密码：
```
git init
git remote set-url origin SSH对应的url（去github对应博客的download查看）
```
![](http://ww1.sinaimg.cn/large/6d9475f6ly1feuqyw1msnj20t40b5q4e.jpg)

在github创建博客项目：
创建Github Repository：Repository名字必须是你的Github名.github.io，比如我是`michael728.github.io`

### Node下载安装
- [Node官方地址](https://nodejs.org/en/)

## 部署Hexo
- [Hexo官方文档](https://hexo.io/zh-cn/docs/)

新建一个文件夹，比如，Blog，然后进入该文件夹下：

```
npm install hexo-cli -g
hexo version
```

安装依赖包
```
npm install
```
### 配置站点文件
为了能够使Hexo部署到GitHub上，需要安装一个插件：
```
npm install hexo-deployer-git --save
```
部署博客的配置：
```
# Deployment
## Docs: http://hexo.io/docs/deployment.html
# deploy:
#   type: git
#   repo: git@github.com:michael728/michael728.github.io.git
  # branch: master
deploy:
  type: git
  repo: git@github.com:Michael728/michael728.github.io.git  #从github博客项目复制，切换为ssh地址
  branch: master
```

### Front-matter
Front-matter 是文件最上方以 --- 分隔的区域，用于指定个别文件的变量，举例来说：
```
title: Hello World
date: 2013/7/13 20:46:25
---
```
比如，在[material主题](https://material.viosey.com/compose/#Front-matter)中，在这儿可以通过关键字`thumbnail:`，填上图片的url地址，设置每篇文章的[缩略图](https://material.viosey.com/compose/#缩略图功能)



## Next主题
首先区分两个概念：

1. 主题配置文件
2. 站点配置文件

Next主题下的_config.yml就是主题配置文件，而站点目录下的_config.yml则是站点配置文件

### 安装：
- [Next-github](https://github.com/iissnan/hexo-theme-next)
- [Next文档](http://theme-next.iissnan.com/)
- [Next作者blog](http://notes.iissnan.com/)
- [动动手指，NexT主题与Hexo更搭哦（基础篇）](http://www.arao.me/2015/hexo-next-theme-optimize-base/)

进入theme文件夹下,执行：
```
git clone https://github.com/iissnan/hexo-theme-next.git
```
执行完成之后，在theme文件夹下则会出现next文件夹。

启用Next主题，在站点配置文件中，设置：
```
theme: next
```

### 评论：
- [disqus](https://michaelxiang.disqus.com/admin/)

主题配置文件中，设置了：
```
disqus_shortname: michaelxiang
```
注意：如果有集成评论服务，页面也会带有评论。 若需要关闭的话，请添加字段 comments 并将值设置为 false，如：
```
title: 标签
date: 2014-12-22 12:39:04
type: "tags"
comments: false
```



### 搜索

http://theme-next.iissnan.com/third-party-services.html#search-system
添加百度/谷歌/本地 自定义站点内容搜索

1.安装 `hexo-generator-searchdb`，在站点的根目录下执行以下命令：
```
$ npm install hexo-generator-searchdb --save
```
2.编辑 站点配置文件，新增以下内容到任意位置：
```
search:
  path: search.xml
  field: post
  format: html
  limit: 10000
```
3.编辑 主题配置文件，启用本地搜索功能：
```
# Local search
local_search:
  enable: true
```

### 新建页面
举例，新建一个“工具”页面。
先执行命令：
```
hexo new page "tools"
```
然后在主题配置文件中的`menu`添加相应的内容：
```
menu:
  home: /
  categories: /categories
  archives: /archives
  tags: /tags
  books: /categories/books  #hexo new page "books"要修改三个地方，去language下面，修改zh-hans中才菜单，添加中文
  diary: /diary  #如果是上面这种结构，则显示该文件夹下所有文章，这后面的英文，要是new page "xx"保持一致
  nav: /nav #导航 别忘了添加图标和中文
  tools: /tools
  about: /about
```
接着，添加对应的中文名字，在next-languages-zh-Hans文件中：
```
menu:
  home: 首页
  archives: 归档
  categories: 分类
  tags: 标签
  about: 关于
  search: 搜索
  books: 书单
  diary: 札记
  nav: 书签
  tools: 工具
  commonweal: 公益404
```
最后，还要设置相应的图标，主题配置文件中：
```
menu_icons:
  enable: true
  #KeyMapsToMenuItemKey: NameOfTheIconFromFontAwesome
  home: home
  about: user
  categories: th
  tags: tags
  archives: archive
  books: book  #参考fontawsomehttp://fontawesome.io/icons/网站图标，还要去修改language中zh-Hans.yml,添加中文说明
  diary: leaf
  nav: bookmark  #别忘了去添加相应中文
  tools: paper-plane
  commonweal: heartbeat
```
图标链接：

### 不蒜子统计
http://theme-next.iissnan.com/third-party-services.html#analytics-busuanzi

编辑 主题配置文件 中的`busuanzi_count`的配置项。

当`enable: true`时，代表开启全局开关。若`site_uv`、`site_pv`、`page_pv`的值均为false时，不蒜子仅作记录而不会在页面上显示。

### 分享服务
http://theme-next.iissnan.com/third-party-services.html#share-system

主题配置文件中：JiaThis 分享服务
```
jiathis: true
```

### 给next主题添加打赏
http://theme-next.iissnan.com/theme-settings.html#reward
```
reward_comment: 坚持原创技术分享，您的支持将鼓励我继续创作！
wechatpay: /path/to/wechat-reward-image
alipay: /path/to/alipay-reward-image
```

### 添加文章阅读统计
- [为NexT主题添加文章阅读量统计功能](https://notes.wanghao.work/2015-10-21-%E4%B8%BANexT%E4%B8%BB%E9%A2%98%E6%B7%BB%E5%8A%A0%E6%96%87%E7%AB%A0%E9%98%85%E8%AF%BB%E9%87%8F%E7%BB%9F%E8%AE%A1%E5%8A%9F%E8%83%BD.html#%E9%85%8D%E7%BD%AELeanCloud)

### 设置RSS
https://github.com/hexojs/hexo-generator-feed
安装扩展：
```
npm install hexo-generator-feed --save
```
然后在站点配置文件中设置：
```
feed:
  type: atom
  path: atom.xml
  limit: 20
  hub:
  content:
```

### 设置侧边栏头像旋转
http://leeyom.top/2016/09/29/hexo%E5%8D%9A%E5%AE%A2%E8%BF%9B%E9%98%B6%E6%95%99%E7%A8%8B/

主要是修改 Hexo 目录下 `\themes\next\source\css\_common\components\sidebar\sidebar-author.styl `文件
```
.site-author-image {
  display: block;
  margin: 0 auto;
  padding: $site-author-image-padding;
  max-width: $site-author-image-width;
  height: $site-author-image-height;
  border: $site-author-image-border-width solid $site-author-image-border-color;

  /* 头像圆形 */
  border-radius: 80px;
  -webkit-border-radius: 80px;
  -moz-border-radius: 80px;
  box-shadow: inset 0 -1px 0 #333sf;

  /* 设置循环动画 [animation: (play)动画名称 (2s)动画播放时长单位秒或微秒 (ase-out)动画播放的速度曲线为以低速结束
    (1s)等待1秒然后开始动画 (1)动画播放次数(infinite为循环播放) ]*/
  -webkit-animation: play 2s ease-out 1s 1;
  -moz-animation: play 2s ease-out 1s 1;
  animation: play 2s ease-out 1s 1;

  /* 鼠标经过头像旋转360度 */
  -webkit-transition: -webkit-transform 1.5s ease-out;
  -moz-transition: -moz-transform 1.5s ease-out;
  transition: transform 1.5s ease-out;
}

img:hover {
  /* 鼠标经过停止头像旋转
  -webkit-animation-play-state:paused;
  animation-play-state:paused;*/

  /* 鼠标经过头像旋转360度 */
  -webkit-transform: rotateZ(360deg);
  -moz-transform: rotateZ(360deg);
  transform: rotateZ(360deg);
}

/* Z 轴旋转动画 */
@-webkit-keyframes play {
  0% {
    -webkit-transform: rotateZ(0deg);
  }
  100% {
    -webkit-transform: rotateZ(-360deg);
  }
}
@-moz-keyframes play {
  0% {
    -moz-transform: rotateZ(0deg);
  }
  100% {
    -moz-transform: rotateZ(-360deg);
  }
}
@keyframes play {
  0% {
    transform: rotateZ(0deg);
  }
  100% {
    transform: rotateZ(-360deg);
  }
}

.site-author-name {
  margin: $site-author-name-margin;
  text-align: $site-author-name-align;
  color: $site-author-name-color;
  font-weight: $site-author-name-weight;
}

.site-description {
  margin-top: $site-description-margin-top;
  text-align: $site-description-align;
  font-size: $site-description-font-size;
  color: $site-description-color;
}
```
### 站点建立时间
个时间将在站点的底部显示，例如` © 2013 - 2015`。 编辑 主题配置文件，新增字段 `since`。
```
since: 2015
```

### SEO优化
给你的hexo站点添加sitemap网站地图
安装hexo的sitemap网站地图生成插件

```
npm install hexo-generator-sitemap --save
npm install hexo-generator-baidu-sitemap --save
在你的hexo站点的_config.yml添加下面的代码
```

hexo sitemap网站地图
```
sitemap:
path: sitemap.xml
baidusitemap:
path: baidusitemap.xml
```
配置成功后，hexo编译时会在hexo站点根目录生成`sitemap.xml`和`baidusitemap.xml`
其中`sitemap.xml`适合提交给谷歌搜素引擎，`baidusitemap.xml`适合提交百度搜索引擎。
其次，在robots.txt中添加下面的一段代码：
```
Sitemap: http://www.arao.me/sitemap.xml
Sitemap: http://www.arao.me/baidusitemap.xml
```
参考： http://www.arao.me/2015/hexo-next-theme-optimize-seo/


### 给next主题添加背景图片
- [给hexo个人博客 next主题添加背景图片](http://blog.csdn.net/wang631106979/article/details/51375184)


## Materail主题
### 安装
- [hexo-theme-material-github](https://github.com/viosey/hexo-theme-material)
- [material文档](https://material.viosey.com/)
- [material作者个人blog](https://blog.viosey.com/)

进入theme文件夹下,执行：
```
git clone https://github.com/viosey/hexo-theme-material.git
```
下载完成之后，修改文件夹名为：material，同时将主题配置文件`_config.template.yml`修改为：`_config.yml`
设置站点配置文件中的语言：`language: zh-CN`，这个和Next主题的`zh-Hans`是有区别的。

然后测试：
```
hexo clean
hexo g
hexo s
```
访问地址：http://localhost:4000/

按照[material文档](https://material.viosey.com/)文档，可以设置主题的背景等，按照说明文档一步一步设置即可。

### dropdown-侧边栏邮箱
https://material.viosey.com/intro/#dropdown
下拉菜单这里，如果不去language设置dropdown对应的中文，那么，就会显示undefined。

### 设置独立页面
- [Material icons](https://material.io/icons/)

- [主题文档-Pages](https://material.viosey.com/intro/#pages)
```
hexo new page "about"  #新建关于我的页面，其实这条命令就是在根目录source文件夹下新建了一个about文件夹
pages:
    About: #这里可以把它改为关于我
        link: "#about"
        icon: person
        divider: false
```
作为一个单位。
Name 是该独立页面的名称，请自行修改。
link 的参数为相对路径，对应 hexo 目录下的 source 文件夹内的相应文件夹。
icon 的参数为自定义的 Material 图标，可用图标可在 Material icons 查询。
divider 设置成 true 后会在该条目底部增加一条分割线

PS:菜单显示中文,只需要将“About”设置为“关于我”，因为它的链接link设置对应的文件夹了，比Next主题添加稍微简单点，那个还需要去主题的language中添加对应的中文
- [创建友链](https://material.viosey.com/compose/#创建「友情链接」页面)

### 设置RSS
- [说明](https://material.viosey.com/services/#RSS)
- [插件github地址](https://github.com/hexojs/hexo-generator-feed)

```
$ npm install hexo-generator-feed --save
#You can configure this plugin in _config.yml.

feed:
  type: atom
  path: atom.xml
  limit: 20
  hub:
  content:
```

### Qrcode-设置二维码，手机扫描阅读
```
npm install hexo-helper-qrcode --save
```
用于在文章页中显示二维码，扫描二维码即可直接打开文章。
`qrcode: true`

### topPost置顶文章
使用该插件可以将指定文章置顶。
如果您需要这个功能，请使用 `npm install hexo-helper-post-top --save `安装支持插件。
之后在您需要置顶文章的 `front-matter `中，添加 `top: true` 即可置顶。

### Hexo插入音乐/视频
- [通过 Hexo 插件插入音乐/视频](http://login926.github.io/2016/07/20/HexoMedia/)

## Maupassant主题-大道至简
- [Maupassant-github](https://github.com/tufu9441/maupassant-hexo)
- [Maupassant-中文文档](https://www.haomwei.com/technology/maupassant-hexo.html)


## 命令
```
hexo help #查看帮助
hexo init #初始化一个目录
hexo new "postName" #新建文章
hexo new page "pageName" #新建页面，其实作用就是在根目录source目录下，添加一个about的文件夹
hexo generate #生成网页, 可以在 public 目录查看整个网站的文件
hexo server #本地预览, 'Ctrl+C'关闭
hexo deploy #部署.deploy目录
hexo clean #清除缓存,
```




## 多台机器同步
每次上传之前，删除同步文件夹下的`.deploy_git`文件夹。


## 参考文章
- [hexo的next主题个性化配置教程-推荐](https://segmentfault.com/a/1190000009544924)
- [SF-自己-Hexo+Github Page部署个人博客资源整理](https://segmentfault.com/a/1190000006058212)
- [解决用Hexo和GitHub搭建博客时hexo d命令报错问题](http://blog.csdn.net/Greenovia/article/details/60576985)
- [Hexo搭建静态博客](http://huangjihua.com.cn/2015/10/15/hexo%E6%90%AD%E5%BB%BA%E9%9D%99%E6%80%81%E5%8D%9A%E5%AE%A2/)
- [Hexo+nexT主题搭建个人博客](http://www.wuxubj.cn/2016/08/Hexo-nexT-build-personal-blog/)
- [hexo博客进阶教程](http://leeyom.top/2016/09/29/hexo%E5%8D%9A%E5%AE%A2%E8%BF%9B%E9%98%B6%E6%95%99%E7%A8%8B/)


-----






