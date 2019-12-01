---
title: Mac 环境对 Github Homebrew 等终端工具的加速设置
date: 2019-11-13 20:29:13
tags: [Mac, Tools]
categories: ToolsDev
keywords:
  - mac
  - config
  - github
  - homebrew
  - tutorial
---

## 背景

最近 Mac 升级系统之后，导致软件都得重装。发现 Homebrew 安装软件很多都是去 Github 上下载，怎么加速呢？简要做个备忘吧。

## 步骤

首先，我是有一个 VPS 代理的，如何配置，可以看我之前的总结[ss+vps+mac](https://michael728.github.io/2019/04/27/tools-ss-vps/) PS：记得用我的链接注册呀！我们都会有返利！

在将 SS 软件打开全局模式下，速度依然很慢，这时候你需要设置终端的代理才会加速。终端代理分为：
- http_proxy
- https_proxy
- all_proxy 这个是针对终端所有的连接都走代理

SS 软件，支持 socks5 协议，也支持 http 协议，打开 偏好设置 --> 高级 选项，可以看到 socks5 协议的 ip 以及端口，在 http 选项卡，可以看到 http 代理的信息。

![](https://gitee.com/michael_xiang/images/raw/master/Jietu20191114-230908.jpg)

我是这么配置的：
```
export all_proxy="socks5://127.0.0.1:1086"
# or
export all_proxy="http://127.0.0.1:1087"
```

上面的端口号 `1086` 取决于你 SS 软件中配置的端口号。除了 `socks5` 协议之外，你也可以配置 `http` 代理。

如果为了避免每次都要执行设置代理，可以将上面的配置加到你的终端配置文件中，比如我是 `~/.zshrc`：

```
export all_proxy="socks5://127.0.0.1:1086" >> ~/.zshrc
source ~/.zshrc
```

这时候，homebrew 安装就不会太慢了。

设置好代理以后，可以使用 `curl cip.cc` 查看当前自己的 ip 信息，确认代理是否设置成功。

为了加速 Git 下载，我在 `~/.gitconfig` 中加入了如下的设置：

```
[https]
    proxy = http://127.0.0.1:1087
    sslVerify = false
[http]
    proxy = http://127.0.0.1:1087
    sslVerify = false
```

还有一种方法，只给部分域名配置代理，这样可以避免克隆国内仓库时，速度不会受到影响：

```
git config --global http.https://github.com.proxy socks5://127.0.0.1:1087
git config --global https.https://github.com.proxy socks5://127.0.0.1:1087
```

取消 Git 代理设置：

```
git config --global --unset http.proxy
git config --global --unset https.proxy
```

## 参考

- [使用代理加速 Mac 终端下载速度](https://jdhao.github.io/2019/10/10/mac_proxy_in_terminal/)
- [GitHub克隆clone太慢添加代理加速访问](https://blog.gobyte.cn/post/1a22163b.html)

<a href="https://www.vultr.com/?ref=7488919"><img src="https://www.vultr.com/media/banners/banner_728x90.png" width="728" height="90"></a>