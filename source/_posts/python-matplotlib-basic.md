---
title: Python--matplotlib绘图可视化知识点整理
tags: [Matplotlib]
toc: true
date: 2016-05-14 15:50:02
categories: Python
thumbnail: https://matplotlib.org/_static/logo2.svg
---

**强烈推荐ipython**
无论你工作在什么项目上，IPython都是值得推荐的。利用`ipython --pylab`，可以进入PyLab模式，已经导入了matplotlib库与相关软件包（例如Numpy和Scipy)，额可以直接使用相关库的功能。

<!-- more -->
本文作为学习过程中对matplotlib一些常用知识点的整理，方便查找。



这样IPython配置为使用你所指定的matplotlib GUI后端（TK/wxPython/PyQt/Mac OS X native/GTK)。对于大部分用户而言，默认的后端就已经够用了。Pylab模式还会向IPython引入一大堆模块和函数以提供一种更接近MATLAB的界面。

# 参考
- [matplotlib-绘制精美的图表](http://old.sebug.net/paper/books/scipydoc/matplotlib_intro.html)
- [matplotlib.pyplot.plt参数介绍](http://www.360doc.com/content/15/0113/23/16740871_440559122.shtml)

```
import matplotlib.pyplot as plt
labels='frogs','hogs','dogs','logs'
sizes=15,20,45,10
colors='yellowgreen','gold','lightskyblue','lightcoral'
explode=0,0.1,0,0
plt.pie(sizes,explode=explode,labels=labels,colors=colors,autopct='%1.1f%%',shadow=True,startangle=50)
plt.axis('equal')
plt.show()
```
# matplotlib图标正常显示中文
为了在图表中能够显示中文和负号等，需要下面一段设置：
```
import matplotlib.pyplot as plt
plt.rcParams['font.sas-serig']=['SimHei'] #用来正常显示中文标签
plt.rcParams['axes.unicode_minus']=False #用来正常显示负号
```

# matplotlib inline和pylab inline
可以使用`ipython --pylab`打开ipython命名窗口。

```
%matplotlib inline  #notebook模式下
%pylab inline   #ipython模式下
```
这两个命令都可以在绘图时，将图片内嵌在交互窗口，而不是弹出一个图片窗口，但是，有一个缺陷：除非将代码一次执行，否则，无法叠加绘图，因为在这两种模式下，是要有`plt`出现，图片会立马`show`出来，因此：

**推荐在ipython notebook时使用，这样就能很方便的一次编辑完代码，绘图。**

# 为项目设置matplotlib参数
在代码执行过程中，有两种方式更改参数：

- 使用参数字典(rcParams)
- 调用matplotlib.rc()命令   通过传入关键字元祖，修改参数



如果不想每次使用matplotlib时都在代码部分进行配置，可以修改matplotlib的文件参数。可以用`matplot.get_config()`命令来找到当前用户的配置文件目录。

配置文件包括以下配置项：
> axex: 设置坐标轴边界和表面的颜色、坐标刻度值大小和网格的显示
> backend: 设置目标暑促TkAgg和GTKAgg
> figure: 控制dpi、边界颜色、图形大小、和子区( subplot)设置
> font: 字体集（font family）、字体大小和样式设置
> grid: 设置网格颜色和线性
> legend: 设置图例和其中的文本的显示
> line: 设置线条（颜色、线型、宽度等）和标记
> patch: 是填充2D空间的图形对象，如多边形和圆。控制线宽、颜色和抗锯齿设置等。
> savefig: 可以对保存的图形进行单独设置。例如，设置渲染的文件的背景为白色。
> verbose: 设置matplotlib在执行期间信息输出，如silent、helpful、debug和debug-annoying。
> xticks和yticks: 为x,y轴的主刻度和次刻度设置颜色、大小、方向，以及标签大小。
# 线条相关属性标记设置

## 用来该表线条的属性

线条风格linestyle或ls|描述|线条风格linestyle或ls|描述
----|----|---|----|
'-'|实线|':'|虚线|
'--'|破折线|'None',' ',''|什么都不画|
'-.'|点划线|


## 线条标记

标记maker|描述|标记|描述|
-----|----|
'o'|圆圈|'.'|点
'D'|菱形|'s'|正方形
'h'|六边形1|'*'|星号
'H'|六边形2|'d'|小菱形
'_'|水平线|'v'|一角朝下的三角形
'8'|八边形|'<'|一角朝左的三角形
'p'|五边形|'>'|一角朝右的三角形
','|像素|'^'|一角朝上的三角形
'+'|加号|'\|'|竖线
'None','',' '|无|'x'|X


## 颜色
可以通过调用`matplotlib.pyplot.colors()`得到matplotlib支持的所有颜色。

别名|颜色|别名|颜色|
---|---|
b|蓝色|g|绿色
r|红色|y|黄色
c|青色|k|黑色|
m|洋红色|w|白色


如果这两种颜色不够用，还可以通过两种其他方式来定义颜色值：

- 使用HTML十六进制字符串   `color='eeefff'` 使用合法的HTML颜色名字（'red','chartreuse'等）。
- 也可以传入一个归一化到[0,1]的RGB元祖。 `color=(0.3,0.3,0.4)`

很多方法可以介绍颜色参数，如title()。
`plt.tilte('Title in a custom color',color='#123456'）`

## 背景色
通过向如`matplotlib.pyplot.axes()`或者`matplotlib.pyplot.subplot()`这样的方法提供一个`axisbg`参数，可以指定坐标这的背景色。

`subplot(111,axisbg=(0.1843,0.3098,0.3098)`

# 基础
如果你向plot()指令提供了一维的数组或列表，那么matplotlib将默认它是一系列的y值，并自动为你生成x的值。默认的x向量从0开始并且具有和y同样的长度，因此x的数据是[0,1,2,3].

![](http://ww1.sinaimg.cn/large/6d9475f6jw1f3roxctsj9j20eo0d9q40.jpg)

图片来自:[绘图: matplotlib核心剖析](http://www.cnblogs.com/vamei/archive/2013/01/30/2879700.html#commentform)

## 确定坐标范围
- plt.axis([xmin, xmax, ymin, ymax])
上面例子里的axis()命令给定了坐标范围。
- xlim(xmin, xmax)和ylim(ymin, ymax)来调整x,y坐标范围
```
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt
from pylab import *

x = np.arange(-5.0, 5.0, 0.02)
y1 = np.sin(x)

plt.figure(1)
plt.subplot(211)
plt.plot(x, y1)

plt.subplot(212)
#设置x轴范围
xlim(-2.5, 2.5)
#设置y轴范围
ylim(-1, 1)
plt.plot(x, y1)
```
![](http://ww1.sinaimg.cn/large/6d9475f6jw1f3rgk720hfj20ag078jrp.jpg)

## 叠加图
用一条指令画多条不同格式的线。
```
import numpy as np
import matplotlib.pyplot as plt

# evenly sampled time at 200ms intervals
t = np.arange(0., 5., 0.2)

# red dashes, blue squares and green triangles
plt.plot(t, t, 'r--', t, t**2, 'bs', t, t**3, 'g^')
plt.show()
```
![](http://ww1.sinaimg.cn/large/6d9475f6jw1f3rggwh5q1j20cq078dg2.jpg)

## plt.figure()
你可以多次使用figure命令来产生多个图，其中，图片号按顺序增加。这里，要注意一个概念当前图和当前坐标。所有绘图操作仅对当前图和当前坐标有效。通常，你并不需要考虑这些事，下面的这个例子为大家演示这一细节。
```
import matplotlib.pyplot as plt
plt.figure(1)                # 第一张图
plt.subplot(211)             # 第一张图中的第一张子图
plt.plot([1,2,3])
plt.subplot(212)             # 第一张图中的第二张子图
plt.plot([4,5,6])


plt.figure(2)                # 第二张图
plt.plot([4,5,6])            # 默认创建子图subplot(111)

plt.figure(1)                # 切换到figure 1 ; 子图subplot(212)仍旧是当前图
plt.subplot(211)             # 令子图subplot(211)成为figure1的当前图
plt.title('Easy as 1,2,3')   # 添加subplot 211 的标题
```
![](http://ww1.sinaimg.cn/large/6d9475f6jw1f3rglix2v7j20b70erwf5.jpg)

figure感觉就是给图像ID，之后可以索引定位到它。

## plt.text()添加文字说明

- text()可以在图中的任意位置添加文字，并支持LaTex语法
- xlable(), ylable()用于添加x轴和y轴标签
- title()用于添加图的题目

```
import numpy as np
import matplotlib.pyplot as plt

mu, sigma = 100, 15
x = mu + sigma * np.random.randn(10000)

# 数据的直方图
n, bins, patches = plt.hist(x, 50, normed=1, facecolor='g', alpha=0.75)


plt.xlabel('Smarts')
plt.ylabel('Probability')
#添加标题
plt.title('Histogram of IQ')
#添加文字
plt.text(60, .025, r'$\mu=100,\ \sigma=15$')
plt.axis([40, 160, 0, 0.03])
plt.grid(True)
plt.show()
```
![](http://ww1.sinaimg.cn/large/6d9475f6jw1f3rgn3vpifj20c107agm9.jpg)
text中前两个参数感觉应该是文本出现的坐标位置。

## plt.annotate()文本注释
在数据可视化的过程中，图片中的文字经常被用来注释图中的一些特征。使用annotate()方法可以很方便地添加此类注释。在使用annotate时，要考虑两个点的坐标：被注释的地方xy(x, y)和插入文本的地方xytext(x, y)。[^1]

```
import numpy as np
import matplotlib.pyplot as plt

ax = plt.subplot(111)

t = np.arange(0.0, 5.0, 0.01)
s = np.cos(2*np.pi*t)
line, = plt.plot(t, s, lw=2)

plt.annotate('local max', xy=(2, 1), xytext=(3, 1.5),
            arrowprops=dict(facecolor='black', shrink=0.05),
            )

plt.ylim(-2,2)
plt.show()
```
![](http://ww3.sinaimg.cn/large/6d9475f6jw1f3rgr2m11cj20ao077t94.jpg)
[^1]:[DataHub-Python 数据可视化入门1](http://www.datahub.top/coursedetail/?id=29)

## plt.xticks()/plt.yticks()设置轴记号
现在是明白干嘛用的了，就是人为设置坐标轴的刻度显示的值。

```
# 导入 matplotlib 的所有内容（nympy 可以用 np 这个名字来使用）
from pylab import *

# 创建一个 8 * 6 点（point）的图，并设置分辨率为 80
figure(figsize=(8,6), dpi=80)

# 创建一个新的 1 * 1 的子图，接下来的图样绘制在其中的第 1 块（也是唯一的一块）
subplot(1,1,1)

X = np.linspace(-np.pi, np.pi, 256,endpoint=True)
C,S = np.cos(X), np.sin(X)

# 绘制余弦曲线，使用蓝色的、连续的、宽度为 1 （像素）的线条
plot(X, C, color="blue", linewidth=1.0, linestyle="-")

# 绘制正弦曲线，使用绿色的、连续的、宽度为 1 （像素）的线条
plot(X, S, color="r", lw=4.0, linestyle="-")

plt.axis([-4,4,-1.2,1.2])
# 设置轴记号

xticks([-np.pi, -np.pi/2, 0, np.pi/2, np.pi],
       [r'$-\pi$', r'$-\pi/2$', r'$0$', r'$+\pi/2$', r'$+\pi$'])

yticks([-1, 0, +1],
       [r'$-1$', r'$0$', r'$+1$'])
# 在屏幕上显示
show()
```
![](http://ww2.sinaimg.cn/large/6d9475f6jw1f3rh9xuqs6j20dx0acjs0.jpg)
当我们设置记号的时候，我们可以同时设置记号的标签。注意这里使用了 LaTeX。[^2]

[^2]:[Matplotlib 教程](http://liam0205.me/2014/09/11/matplotlib-tutorial-zh-cn/)

## 移动脊柱 坐标系
```
ax = gca()
ax.spines['right'].set_color('none')
ax.spines['top'].set_color('none')
ax.xaxis.set_ticks_position('bottom')
ax.spines['bottom'].set_position(('data',0))
ax.yaxis.set_ticks_position('left')
ax.spines['left'].set_position(('data',0))
```
这个地方确实没看懂，囧，以后再说吧，感觉就是移动了坐标轴的位置。


## plt.legend()添加图例
```
plot(X, C, color="blue", linewidth=2.5, linestyle="-", label="cosine")
plot(X, S, color="red",  linewidth=2.5, linestyle="-", label="sine")

legend(loc='upper left')
```
![](http://ww1.sinaimg.cn/large/6d9475f6jw1f3rhhjm875j20dk09uaaq.jpg)


# matplotlib.pyplot
使用`plt.style.use('ggplot')`命令，可以作出ggplot风格的图片。

```
# Import necessary packages
import pandas as pd
%matplotlib inline
import matplotlib.pyplot as plt
plt.style.use('ggplot')
from sklearn import datasets
from sklearn import linear_model
import numpy as np
# Load data
boston = datasets.load_boston()
yb = boston.target.reshape(-1, 1)
Xb = boston['data'][:,5].reshape(-1, 1)
# Plot data
plt.scatter(Xb,yb)
plt.ylabel('value of house /1000 ($)')
plt.xlabel('number of rooms')
# Create linear regression object
regr = linear_model.LinearRegression()
# Train the model using the training sets
regr.fit( Xb, yb)
# Plot outputs
plt.scatter(Xb, yb,  color='black')
plt.plot(Xb, regr.predict(Xb), color='blue',
         linewidth=3)
plt.show()

```
![](http://ww1.sinaimg.cn/large/6d9475f6jw1f3q6ylj1yjj20cw07qjrh.jpg)

## 给特殊点做注释
好吧，又是注释，多个例子参考一下！

我们希望在 2π/32π/3 的位置给两条函数曲线加上一个注释。首先，我们在对应的函数图像位置上画一个点；然后，向横轴引一条垂线，以虚线标记；最后，写上标签。
```
t = 2*np.pi/3
# 作一条垂直于x轴的线段，由数学知识可知，横坐标一致的两个点就在垂直于坐标轴的直线上了。这两个点是起始点。
plot([t,t],[0,np.cos(t)], color ='blue', linewidth=2.5, linestyle="--")
scatter([t,],[np.cos(t),], 50, color ='blue')

annotate(r'$\sin(\frac{2\pi}{3})=\frac{\sqrt{3}}{2}$',
         xy=(t, np.sin(t)), xycoords='data',
         xytext=(+10, +30), textcoords='offset points', fontsize=16,
         arrowprops=dict(arrowstyle="->", connectionstyle="arc3,rad=.2"))

plot([t,t],[0,np.sin(t)], color ='red', linewidth=2.5, linestyle="--")
scatter([t,],[np.sin(t),], 50, color ='red')

annotate(r'$\cos(\frac{2\pi}{3})=-\frac{1}{2}$',
         xy=(t, np.cos(t)), xycoords='data',
         xytext=(-90, -50), textcoords='offset points', fontsize=16,
         arrowprops=dict(arrowstyle="->", connectionstyle="arc3,rad=.2"))

```
![](http://ww4.sinaimg.cn/large/6d9475f6jw1f3ri9l7efkj20dm0anmy6.jpg)




# plt.subplot()
`plt.subplot(2,3,1)`表示把图标分割成2*3的网格。也可以简写`plt.subplot(231)`。其中，第一个参数是行数，第二个参数是列数，第三个参数表示图形的标号。



# plt.axes()
我们先来看什么是Figure和Axes对象。在matplotlib中，整个图像为一个Figure对象。在Figure对象中可以包含一个，或者多个Axes对象。每个Axes对象都是一个拥有自己坐标系统的绘图区域。其逻辑关系如下[^3][^4]：
![](http://ww4.sinaimg.cn/large/6d9475f6jw1f3rok0spuej20a506j74d.jpg)

[plt.axes-官方文档](http://matplotlib.org/api/pyplot_api.html#matplotlib.pyplot.axes)


- axes() by itself creates a default full subplot(111) window axis.
- axes(rect, axisbg='w') where rect = [left, bottom, width, height] in normalized (0, 1) units. axisbg is the background color for the axis, default white.
- axes(h) where h is an axes instance makes h the current axis. An Axes instance is returned.
> rect=[左, 下, 宽, 高] 规定的矩形区域,rect矩形简写，这里的数值都是以figure大小为比例，因此，若是要两个axes并排显示，那么axes[2]的左=axes[1].左+axes[1].宽，这样axes[2]才不会和axes[1]重叠。

show code:
```
http://matplotlib.org/examples/pylab_examples/axes_demo.html

import matplotlib.pyplot as plt
import numpy as np

# create some data to use for the plot
dt = 0.001
t = np.arange(0.0, 10.0, dt)
r = np.exp(-t[:1000]/0.05)               # impulse response
x = np.random.randn(len(t))
s = np.convolve(x, r)[:len(x)]*dt  # colored noise

# the main axes is subplot(111) by default
plt.plot(t, s)
plt.axis([0, 1, 1.1*np.amin(s), 2*np.amax(s)])
plt.xlabel('time (s)')
plt.ylabel('current (nA)')
plt.title('Gaussian colored noise')

# this is an inset axes over the main axes
a = plt.axes([.65, .6, .2, .2], axisbg='y')
n, bins, patches = plt.hist(s, 400, normed=1)
plt.title('Probability')
plt.xticks([])
plt.yticks([])

# this is another inset axes over the main axes
a = plt.axes([0.2, 0.6, .2, .2], axisbg='y')
plt.plot(t[:len(r)], r)
plt.title('Impulse response')
plt.xlim(0, 0.2)
plt.xticks([])
plt.yticks([])

plt.show()
```
![](http://ww3.sinaimg.cn/large/6d9475f6jw1f3row583k8j20bd07tt9e.jpg)



[^3]:[绘图: matplotlib核心剖析](http://www.cnblogs.com/vamei/archive/2013/01/30/2879700.html#commentform)
[^4]:[python如何调整子图的大小？](https://www.zhihu.com/question/21953954)


# pyplot.pie参数
- [matplotlib.pyplot.pie](http://matplotlib.org/api/pyplot_api.html#matplotlib.pyplot.pie)

## colors颜色
找出matpltlib.pyplot.plot中的colors可以取哪些值？

- [so-Named colors in matplotlib](http://stackoverflow.com/questions/22408237/named-colors-in-matplotlib)
- [CSDN-matplotlib学习之（四）设置线条颜色、形状](http://blog.csdn.net/kkxgx/article/details/6951959)
```
for name,hex in matplotlib.colors.cnames.iteritems():
    print name,hex
```
打印颜色值和对应的RGB值。

- `plt.axis('equal')`避免比例压缩为椭圆

## autopct
- [How do I use matplotlib autopct?](http://stackoverflow.com/questions/6170246/how-do-i-use-matplotlib-autopct)
```
autopct enables you to display the percent value using Python string formatting. For example, if autopct='%.2f', then for each pie wedge, the format string is '%.2f' and the numerical percent value for that wedge is pct, so the wedge label is set to the string '%.2f'%pct.
```



















<script src='//w.segmentfault.com/card/1030000002497141.js?w=0&3rd=0&bg=0&bd=DDDDDD&cl=333333&btn=009a61&noBtn=0'></script>

----


