---
title: Linux 必备命令利器 —— sed
date: 2020-02-18 22:15:28
tags: [command,linux]
categories: Linux
keywords: Linux
---

虽然之前对常用的 Linux 命令有过一个总结 [工作中常用的 Linux 命令](https://michael728.github.io/2018/07/05/linux-useful-commands-in-work/)，但是有一些命令的用法确实值得为她单独写一篇总结。今天我们就来认识一下 sed。

<!-- more -->

## 概述

`sed` 是一个流编辑器（`stream editor`，或许，`sed` 缩写的含义就是这个）。流编辑器用于对输入流（文件或来自管道的输入）执行基本的文本转换。 sed在管道中过滤文本的能力，这使其与其他类型的编辑器特别有区别。

## 地址定界符

今天看到一个 `sed` 的写法，`sed 's#^key##'`，一开始没看明白，因为之前都是类似 `sed 's/^//'` 这样写的。后来查了资料才发现，`/、#、@` 都是地址定界符，效果是一样的。


## 替换

```shell
[root@607bac0e5f20 demo]# cat demo.txt
hello michael
hello qq
[root@607bac0e5f20 demo]# sed 's/michael/xiang/' demo.txt
hello xiang
hello qq
[root@607bac0e5f20 demo]# cat demo.txt|sed 's/michael/xiang/'
hello xiang
hello qq
```

## 增加

```shell
[root@607bac0e5f20 demo]# sed -i '1i demo learn' demo.txt
[root@607bac0e5f20 demo]# cat demo.txt
demo learn
hello michael
hello qq
[root@607bac0e5f20 demo]# sed -i '2a sunny smile' demo.txt
[root@607bac0e5f20 demo]# cat demo.txt
sed demo learn
hello michael
sunny smile
hello qq
[root@607bac0e5f20 demo]# sed '$a end' demo.txt
sed demo learn
hello michael
sunny smile
hello qq
end
[root@607bac0e5f20 demo]# cat demo.txt
sed demo learn
hello michael
sunny smile
hello qq
```

说明：
- `-i` 参数加上之后，会去「就地」修改 `demo.txt` 这个文件，不加的话，`demo.txt` 的内容不会被修改； 
- `1i` 表示文件第1行内容加上内容，`i` 效果是读取第一行之前增加 `include` 记录。有点 `VIM` 的 `i` 的效果；
- `2a` 表示文件第2行捏偶人后面加上内容，`a` 效果就是读取此行之后增加 `append` 记录。优点 `VIM` 的 `a` 的效果；
- `$` 表示文件尾，在最后一行增加，则可用 `$a` 表示；

如果需要在匹配的地方增加一行，可以：
```shell
[root@607bac0e5f20 demo]# sed -i '/michael/i michael is micahel' demo.txt
[root@607bac0e5f20 demo]# cat demo.txt
sed demo learn
michael is micahel
hello michael
sunny smile
hello qq
```

说明：
- 上面这里的 `/michael/i` 这里的 `i` 当然也可以换成 `a`，那么，插入的位置会有不同；
- 如果插入的行以空格开头，可用 `\` 来转义这个空格
- 如果多行都满足匹配条件，那么就会多出插入内容；
- 如果插入的内容要换行，可用 `\n` 表示换行符，可以实现插入2行的效果


## 参考
- [官宣-sed, a stream editor](http://www.gnu.org/software/sed/manual/sed.html) 权威文档
- [CoolShell-SED 简明教程](https://coolshell.cn/articles/9104.html)
- [看例子学sed](http://qinghua.github.io/sed/)
- [growing-up/doc/三十分钟学会SED.md](https://github.com/mylxsw/growing-up/blob/master/doc/%E4%B8%89%E5%8D%81%E5%88%86%E9%92%9F%E5%AD%A6%E4%BC%9ASED.md)