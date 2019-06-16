title: hexo之github\gitcafe多线路托管博客deploy及SSH设置问题
tags: [Hexo]
toc: true
date: 2015-11-30 14:21:00
categories: Tools
---

github时常抽风，访问不了，速度和国内的gitcafe相比，也稍微慢一点。那么通过将博客文件部属同时部署到这两个网站，之后通过DNS域名等设置，国内访问gitcafe，国外访问github，这样就ok啦！
<!-- more -->

# 检查SSH keys的设置
github官方参考文档：[generating ssh keys](https://help.github.com/articles/generating-ssh-keys/)
检查电脑上现有的SSH，简单点单来说，SSH就是和秘钥有关的文件，可以看这篇[《理解git,github和SSH Keys》](http://www.tuicool.com/articles/jiU36j)
```
cd ~/.ssh
```
咱们要是配置过，ssh一般都在`C:\Users\xiang_000\.ssh`这样的文件加下。如果检查到有这样的文件，咱们如果现在是在重新搭建的话，可以把这个备份到别处，防止有用，然后把它们删除，然后重新生成。

# 双SSH生成
通过Git bash窗口，生成新的SSH

## 生成github的SSH
```
ssh-keygen -t rsa -C "github的email“
```
会看到很多需要确认的，咱们一路`Enter`到底。

## github账户设置中添加SSH
在`C:\Users\xiang_000\.ssh`文件夹下就有了`github_rsa`、`github_ras.pub`,用编辑器打开pub文件，将公钥添加到github accounting->add key

## 配置账户信息
现在你已经可以通过SSH链接到GitHub了，还有一些个人信息需要完善的。Git会根据用户的名字和邮箱来记录提交。GitHub也是用这些信息来做权限的处理，输入下面的代码进行个人信息的设置，把名称和邮箱替换成自己的。

```
git config --global user.name "github账户名，不是昵称"
git config --global user.email "github邮箱名"

```

## 测试
```
ssh -T git@github.com
```
看到`successfully`就代表上述配置就没问题，可以正常连接到github啦！

## 生成gitcafe的SSH

官方的参考文档就很详细了，参考：[如何同时使用多个公秘钥](https://gitcafe.com/GitCafe/Help/wiki/%E5%A6%82%E4%BD%95%E5%90%8C%E6%97%B6%E4%BD%BF%E7%94%A8%E5%A4%9A%E4%B8%AA%E5%85%AC%E7%A7%98%E9%92%A5)
```
ssh-keygen -t rsa -C "YOUR_EMAIL@YOUREMAIL.COM" -f ~/.ssh/gitcafe

```
这样生成的gitcafe的ssh在`C:\Users\xiang_000\.ssh`目录下就有别名，和github生成就有区分。然后步骤差不多，将gitcafe的pub添加到gitcafe中。

# Github\Gitcafe双Deploy部属
双部属参考文章：[hexo博客搭建时遇到的问题](http://chitanda.me/2015/06/11/tips-for-setup-hexo/)
在 SSH 用户配置文件 ~/.ssh/config 中指定对应服务所使用的公秘钥名称(config文件的作用就是告诉git程序在同步认证的时候该使用哪个密钥），如果没有 config 文件的话就新建一个，并输入以下内容：
```
Host github.com
User git
Hostname ssh.github.com
PreferredAuthentications publickey
IdentityFile ~/.ssh/id_rsa
Port 443
Host gitcafe.com www.gitcafe.com
IdentityFile ~/.ssh/gitcafe
```
注:新建config的两种方式：
+ 新建`txt文件`,添加完内容之后，重命名，把`txt`格式名去掉。

+ `touch config`命令新建，再用编辑器打开


# hexo站点deploy配置

```
deploy:
type: git
message: "xxxxx"
repo:
github: git@github.com:michael728/michael728.github.io.git,master
gitcafe: git@gitcafe.com:michael233/michael233.git,gitcafe-pages
```
注意：repo的格式要这么写，因为我们部属时，是利用的ssh方式，所以千万别写成:
`repo: https://github.com/michael728/michael728.github.io.git`

# 部属，同步到gitcafe\github上
```
hexo clean
hexo d
```
关于之后的DNS域名等设置，看下一篇文章。

----


