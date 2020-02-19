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
[root@607bac0e5f20 demo]# sed -i '/michael/i michael is michael' demo.txt
[root@607bac0e5f20 demo]# cat demo.txt
sed demo learn
michael is michael
hello michael
sunny smile
hello qq
```

说明：
- 上面这里的 `/michael/i` 这里的 `i` 当然也可以换成 `a`，那么，插入的位置会有不同；
- 如果插入的行以空格开头，可用 `\` 来转义这个空格
- 如果多行都满足匹配条件，那么就会多出插入内容；
- 如果插入的内容要换行，可用 `\n` 表示换行符，可以实现插入2行的效果

## 删

删除和增加差不多，只需要把 `i` 或 `a` 替换为 `d`。

示例文本内容如下：
```shell
[root@607bac0e5f20 demo]# cat -n demo.txt
     1	sed demo learn
     2	michael is michael
     3	hello michael
     4	sunny smile
     5	hello qq
```

把第1行删除：
```shell
[root@607bac0e5f20 demo]# sed '1d' demo.txt
michael is michael
hello michael
sunny smile
hello qq
```

将和 `michael` 匹配的行删除：
```shell
[root@607bac0e5f20 demo]# sed '/michael/d' demo.txt
sed demo learn
sunny smile
hello qq
```

将 `smile` 匹配行和其下一行内容删除：
```shell
[root@607bac0e5f20 demo]# sed '/smile/{N;d}' demo.txt
sed demo learn
michael is michael
hello michael
```

说明：
- 这里的 `N` 表示 `next line` 的意思。

如果不想删除 `smile` 这行，仅仅是删除 `smile` 匹配行的下一行呢？

```shell
[root@607bac0e5f20 demo]# sed '/smile/{N;s/\n.*//}' demo.txt
sed demo learn
michael is michael
hello michael
sunny smile
```

说明：
- `s/\n.*//` 这里表示匹配了下一行的内容，然后用空字符替换掉了这一行，即实现了删除下一行的效果

## 改、替换

示例文本内容如下：
```shell
[root@607bac0e5f20 demo]# cat -n demo.txt
     1	sed demo learn
     2	michael is michael
     3	hello michael
     4	sunny smile
     5	hello wheel
```

每行开头增加 `China,` 的内容：
```shell
[root@607bac0e5f20 demo]# sed 's/^/China,/' demo.txt
China,sed demo learn
China,michael is michael
China,hello michael
China,sunny smile
China,hello wheel
```

说明：
- `s` 表示 `substitute`，替换的意思；
- `^` 表示行首，行尾用 `$` 表示，又和 `VIM` 的操作一样；

将所有的 `michael` 替换为 `Michael：
```shell
[root@607bac0e5f20 demo]# sed 's/michael/Michael/g' demo.txt
sed demo learn
Michael is Michael
hello Michael
sunny smile
hello wheel
```

说明：
- `g` 代表整行范围内的所有匹配全部替换，如果不加，那么只会匹配每一行第一个匹配的内容，比如，文本第二行，有两个 `michael`，如果不加 `g`，那么，第二个 `michael` 将不会 -> `Michael`。如果用 `2` 的话，则表示只替换第 2 个匹配项。还可以用 `i` 忽略大小写

如果想让匹配的内容后面加上指定字符呢，比如，上面文本中， `el` 匹配的后面加上 `-dev` 的字符：
```shell
[root@607bac0e5f20 demo]# sed 's/el/&-dev/g' demo.txt
sed demo learn
michael-dev is michael-dev
hel-devlo michael-dev
sunny smile
hel-devlo wheel-dev
[root@607bac0e5f20 demo]# sed -r 's/(el)/\1-dev/g' demo.txt
sed demo learn
michael-dev is michael-dev
hel-devlo michael-dev
sunny smile
hel-devlo wheel-dev
```

说明：
- 第一种方法中，`&` 表示匹配上的内容；
- 第二种方法，`-r` 表示扩展的正则表达式，圆括号表示分组，第一个圆括号是第一组，替换的时候则用 `\1` 表示，一次类推；

将2-3行的 `michael` 替换为 `xiang`：

```shell
[root@607bac0e5f20 demo]# sed '2,3s/michael/xiang/g' demo.txt
sed demo learn
xiang is xiang
hello xiang
sunny smile
hello wheel
```

将第2行的内容替换为 `hello mike`：
```shell
[root@607bac0e5f20 demo]# sed '2s/.*/hello mike/g' demo.txt
sed demo learn
hello mike
hello michael
sunny smile
hello wheel
```

使用变量的值用于替换的表达式中，需要用**双引号**：
```shell
[root@607bac0e5f20 demo]# name=Michael
[root@607bac0e5f20 demo]# echo $name
Michael
[root@607bac0e5f20 demo]# sed "s/wheel/${name}/g" demo.txt
sed demo learn
michael is michael
hello michael
sunny smile
hello Michael
```



说明：
- 如果不用双引号，那么，文本最后一行将会是这样的 `hello ${name}`;

删除所有的符号：

```shell
[root@607bac0e5f20 demo]# cat demo.txt
First, sed demo learn
Second, michael is michael
Third, hello michael
Fourth, sunny smile
Fifth, hello wheel
[root@607bac0e5f20 demo]# sed 's/[[:punct:]]//g' demo.txt
First sed demo learn
Second michael is michael
Third hello michael
Fourth sunny smile
Fifth hello wheel
```

说明：
- `[[:punct:]]` 是正则表达式中预先定义的子字符类（`character classes`），代表所有的标点符号。sed 支持的[子字符类](http://www.gnu.org/software/grep/manual/html_node/Character-Classes-and-Bracket-Expressions.html)如下：

```
:alnum:]：[0-9A-Za-z]
[:alpha:]：[A-Za-z]
[:blank:]：空格和TAB
[:cntrl:]：控制字符（Control characters），ASCII码为000~037和177 (DEL)
[:digit:]：[0-9]
[:graph:]：[:alnum:]和[:punct:]
[:lower:]：[a-z]
[:print:]：[:alnum:]、[:punct:]和空格
[:punct:]：符号 ! “ # $ % & ‘ ( ) * + , - . / : ; < = > ? @ [ \ ] ^ _ ` { | } ~
[:space:]：[:blank:]和回车、换行等
[:upper:]：[A-Z]
[:xdigit:]：16进制 [0-9A-Fa-f]
```

## 参考
- [官宣-sed, a stream editor](http://www.gnu.org/software/sed/manual/sed.html) 权威文档
- [CoolShell-SED 简明教程](https://coolshell.cn/articles/9104.html)
- [看例子学sed](http://qinghua.github.io/sed/)
- [growing-up/doc/三十分钟学会SED.md](https://github.com/mylxsw/growing-up/blob/master/doc/%E4%B8%89%E5%8D%81%E5%88%86%E9%92%9F%E5%AD%A6%E4%BC%9ASED.md)