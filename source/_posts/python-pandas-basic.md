---
title: Python数据分析入门之pandas总结基础
tags: [Python, Pandas, 数据分析]
toc: true
date: 2015-12-23 18:44:29
categories: Python
thumbnail: https://www.enthought.com/images/pandas_logo.png
---

#  Pandas--“大熊猫”基础
## Series

Series: pandas的长枪(数据表中的一列或一行,观测向量,一维数组...)

```

Series1 = pd.Series(np.random.randn(4))

print Series1,type(Series1) 

print Series1.index

print Series1.values

```

<!-- more -->

输出结果：

```

0   -0.676256

1    0.533014

2   -0.935212

3   -0.940822

dtype: float64 <class 'pandas.core.series.Series'>

Int64Index([0, 1, 2, 3], dtype='int64')

[-0.67625578  0.53301431 -0.93521212 -0.94082195]

```

+ np.random.randn()   正态分布相关。[函数说明](http://docs.scipy.org/doc/numpy-1.10.1/reference/generated/numpy.random.rand.html#numpy.random.rand)



### Series⽀持过滤的原理就如同NumPy

```

print Series1>0 

print Series1[Series1>0]

```

输出结果如下：

```

0 0.030480

1 0.072746

2 -0.186607

3 -1.412244

dtype: float64 <class 'pandas.core.series.Series'>

Int64Index([0, 1, 2, 3], dtype='int64')

[ 0.03048042 0.07274621 -0.18660749 -1.41224432]

```

我发现，逻辑表达式，获得的值就是True或者False。要先取得值，还是要X[y]的形式。

### 当然也支持广播Broadcasting

什么是`broadcasting`,暂时我也不太清楚，看个栗子：

```

print Series1*2 

print Series1+5

```

输出结果如下：

```
0 0.06096

1 1 0.145492 

2 -0.373215 

3 -2.824489 

dtype: float64 

0 5.030480 

1 5.072746 

2 4.813393 

3 3.587756 

dtype: float64

```

### 以及Universal Function

numpy.frompyfunc(out,nin,nout)    返回的是一个函数，nin是输入的参数个数，nout是函数返回的对象的个数[函数说明](http://docs.scipy.org/doc/numpy/reference/generated/numpy.frompyfunc.html#numpy.frompyfunc)

### 在序列上就使用行标，而不是创建1个2列的数据表，能够轻松辨别哪是数据，哪是元数据

这句话的意思，我的理解是序列尽量是一列，不用去创建2列，这样子，使用index就能指定数据了

```

Series2 = pd.Series(Series1.values,index=['norm_'+unicode(i) for i in xrange(4)])

print Series2,type(Series2)

print Series2.index

print type(Series2.index)

print Series2.values

```

输出结果如下，可以看到，它是通过修改了`index`值的样式，并没有创建2列。

```

norm_0   -0.676256

norm_1    0.533014

norm_2   -0.935212

norm_3   -0.940822

dtype: float64 <class 'pandas.core.series.Series'>

Index([u'norm_0', u'norm_1', u'norm_2', u'norm_3'], dtype='object')

<class 'pandas.core.index.Index'>

[-0.67625578  0.53301431 -0.93521212 -0.94082195]

```

虽然行是有顺序的，但是仍然能够通过行级的index来访问到数据：

（当然也不尽然像Ordered Dict，因为⾏索引甚⾄可以重复，不推荐重复的行索引不代表不能用）

```

print Series2[['norm_0','norm_3']]

```

可以看到，读取数据时，确实要采用X[y]的格式。这里X[[y]]是因为，它要读取两个数据，指定的是这两个数据的`index`值，将`index`值存放进`list`中，然后读取。输出结果如下：

```

norm_0   -0.676256

norm_3   -0.940822

dtype: float64

```

再比如：

```

print 'norm_0' in Series2

print 'norm_6' in Series2

```

输出结果：

```

True

False

```

逻辑表达式的输出结果，布尔型值。

### 从Key不重复的Ordered Dict或者从Dict来定义Series就不需要担心行索引重复：

```

Series3_Dict = {"Japan":"Tokyo","S.Korea":"Seoul","China":"Beijing"}

Series3_pdSeries = pd.Series(Series3_Dict)

print Series3_pdSeries

print Series3_pdSeries.values

print Series3_pdSeries.index

```

输出结果：

```

China Beijing

Japan Tokyo

S.Korea Seoul

dtype: object

['Beijing' 'Tokyo' 'Seoul']

Index([u'China', u'Japan', u'S.Korea'], dtype='object')

```

通过上面的输出结果就知道了，输出结果是无序的，和输入顺序无关。 

想让序列按你的排序⽅式保存？就算有缺失值都毫无问题

```

Series4_IndexList = ["Japan","China","Singapore","S.Korea"]

Series4_pdSeries = pd.Series( Series3_Dict ,index = Series4_IndexList)

print Series4_pdSeries

print Series4_pdSeries.values

print Series4_pdSeries.index

print Series4_pdSeries.isnull()

print Series4_pdSeries.notnull()

```

上面这样的输出就会按照`list`中定义的顺序输出结果。



> 整个序列级别的元数据信息：name

> 当数据序列以及index本身有了名字，就可以更方便的进行后续的数据关联啦！



这里我感觉就是列名的作用。下面举例：

```

print Series4_pdSeries.name

print Series4_pdSeries.index.name

```

很显然，输出的结果都是`None`，因为我们还没指定`name`嘛！

```

Series4_pdSeries.name = "Capital Series"

Series4_pdSeries.index.name = "Nation"

print Series4_pdSeries

```

输出结果：

```

Nation

Japan Tokyo

China Beijing

Singapore NaN

S.Korea Seoul

Name: Capital Series, dtype: object

```

"字典"？不是的，⾏index可以重复，尽管不推荐。

```

Series5_IndexList = ['A','B','B','C']

Series5 = pd.Series(Series1.values,index = Series5_IndexList)

print Series5

print Series5[['B','A']]

```

输出结果：

```

A 0.030480

B 0.072746

B -0.186607

C -1.412244

dtype: float64

B 0.072746

B -0.186607

A 0.030480

dtype: float64

```

我们可以看出，Series['B']输出了两个值，所以index值尽量不要重复呀！

## DataFrame

DataFrame：pandas的战锤(数据表，⼆维数组)

Series的有序集合，就像R的DataFrame一样方便。

仔细想想，绝大部分的数据形式都可以表现为DataFrame。

### 从NumPy二维数组、从文件或者从数据库定义：数据虽好，勿忘列名

```

dataNumPy = np.asarray([('Japan','Tokyo',4000),('S.Korea','Seoul',1300),('China','Beijing',9100)])

DF1 = pd.DataFrame(dataNumPy,columns=['nation','capital','GDP'])

DF1

```

这里DataFrame中的`columns`应该就是列名的意思。现在看`print`的结果，是不是很舒服啊！Excel的样式嘛

### 等长的列数据保存在一个字典里（JSON）：很不幸，字典key是无序的

```

dataDict = {'nation':['Japan','S.Korea','China'],'capital':['Tokyo','Seoul','Beijing'],'GDP':[4900,1300,9100]}

DF2 = pd.DataFrame(dataDict)

DF2

```

输出结果可以发现，**无序的！**

```
    GDP capital nation

0   4900    Tokyo   Japan

1   1300    Seoul   S.Korea

2   9100    Beijing China
```

PS:由于懒得截图放过来，这里没有了边框线。

### 从另一个DataFrame定义DataFrame：啊，强迫症犯了！

```

DF21 = pd.DataFrame(DF2,columns=['nation','capital','GDP'])

DF21

```

很明显，这里是利用`DF2`定义DF21，还通过指定`cloumns`改变了列名的顺序。

```

DF22 = pd.DataFrame(DF2,columns=['nation','capital','GDP'],index = [2,0,1])

DF22

```

很明显，这里定义了`columns`的顺序，还定义了`index`的顺序。

```

nation capital GDP

2 China Beijing 9100

0 Japan Tokyo 4900

1 S.Korea Seoul 1300

```

### 从DataFrame中取出列？两种方法（与JavaScript完全一致！）

OMG，囧，我竟然都快忘了js语法了，现在想起了，但是对象的属性既可以`obj.x`也可以`obj[x]`。

+ '.'的写法容易与其他预留关键字产生冲突

+ '[ ]'的写法最安全。

### 从DataFrame中取出行？（至少）两种⽅法：

+ 方法1和方法2：

```

print DF22[0:1] #给出的实际是DataFrame

print DF22.ix[0] #通过对应Index给出⾏,**ix**好爽。

```

输出结果：

```

 nation  capital   GDP

2  China  Beijing  9100

nation     Japan

capital    Tokyo

GDP         4900

Name: 0, dtype: object

```

+ 方法3 **像NumPy切片一样的终极招式：iloc ** ：

```

print DF22.iloc[0,:]    #第一个参数是第几行，第二个参数是列。这里呢，就是第0行，全部列

print DF22.iloc[:,0]    #根据上面的描述，这里是全部行，第0列

```

输出结果，验证一下：

```

nation       China

capital    Beijing

GDP           9100

Name: 2, dtype: object

2      China

0      Japan

1    S.Korea

Name: nation, dtype: object

```

### 动态增加列列，但是无法用"."的方式，只能用"[]"

举个栗子说明一下就明白了：

```

DF22['population'] = [1600,130,55]

DF22

```

输出结果：

```

nation  capital GDP population

2   China   Beijing 9100    1600

0   Japan   Tokyo   4900    130

1   S.Korea Seoul   1300    55

```



## Index：行级索引

Index：pandas进⾏数据操纵的鬼牌（行级索引）

⾏级索引是：

+ 元数据

+ 可能由真实数据产生，因此可以视作数据

+ 可以由多重索引也就是多个列组合而成

+ 可以和列名进行交换，也可以进行堆叠和展开，达到Excel透视表效果

Index有四种...哦不，很多种写法，⼀些重要的索引类型包括：

+ pd.Index（普通）

+ Int64Index（数值型索引）

+ MultiIndex（多重索引，在数据操纵中更详细描述）

+ DatetimeIndex（以时间格式作为索引）

+ PeriodIndex （含周期的时间格式作为索引）

### 直接定义普通索引，长得就和普通的Series⼀样

```

index_names = ['a','b','c']

Series_for_Index = pd.Series(index_names)

print pd.Index(index_names)

print pd.Index(Series_for_Index)

```

输出结果：

```

Index([u'a', u'b', u'c'], dtype='object')

Index([u'a', u'b', u'c'], dtype='object')

```

**可惜Immutable，牢记！**  不可变！举例如下：此处挖坑啊。不明白……

```

index_names = ['a','b','c'] 

index0 = pd.Index(index_names) 

print index0.get_values() 

index0[2] = 'd'

```

输出结果如下：

```

['a' 'b' 'c']

---------------------------------------------------------------------------

TypeError                                 Traceback (most recent call last)

<ipython-input-36-f34da0a8623c> in <module>()

      2 index0 = pd.Index(index_names)

      3 print index0.get_values()

----> 4 index0[2] = 'd'



C:\Anaconda\lib\site-packages\pandas\core\index.pyc in __setitem__(self, key, value)

   1055 

   1056     def __setitem__(self, key, value):

-> 1057         raise TypeError("Indexes does not support mutable operations")

   1058 

   1059     def __getitem__(self, key):



TypeError: Indexes does not support mutable operations

```

### 扔进去一个含有多元组的List，就有了MultiIndex

可惜，如果这个List Comprehension改成小括号，就不对了。

```

multi1 = pd.Index([('Row_'+str(x+1),'Col_'+str(y+1)) for x in xrange(4) for y in xrange(4)])

multi1.name = ['index1','index2']

print multi1

```

输出结果：

```

MultiIndex(levels=[[u'Row_1', u'Row_2', u'Row_3', u'Row_4'], [u'Col_1', u'Col_2', u'Col_3', u'Col_4']],

           labels=[[0, 0, 0, 0, 1, 1, 1, 1, 2, 2, 2, 2, 3, 3, 3, 3], [0, 1, 2, 3, 0, 1, 2, 3, 0, 1, 2, 3, 0, 1, 2, 3]])

```

### 对于Series来说，如果拥有了多重Index，数据，变形！

下列代码说明：

+ 二重MultiIndex的Series可以unstack()成DataFrame

+ DataFrame可以stack成拥有⼆重MultiIndex的Series

```

data_for_multi1 = pd.Series(xrange(0,16),index=multi1)

data_for_multi1

```

输出结果：

```
Row_1  Col_1     0
       Col_2     1

       Col_3     2

       Col_4     3
Row_2  Col_1     4

       Col_2     5

       Col_3     6

       Col_4     7
Row_3  Col_1     8

       Col_2     9

       Col_3    10

       Col_4    11

Row_4  Col_1    12
       Col_2    13

       Col_3    14

       Col_4    15

dtype: int32
```


看到输出结果，好像明白了点，有点类似Excel汇总一样。不过，日后还得查点[资料](http://pandas.pydata.org/pandas-docs/stable/generated/pandas.Index.html#pandas.Index)

#### 二重MultiIndex的Series可以unstack()成DataFrame

```

data_for_multi1.unstack()

```

![unstack结果](http://ww4.sinaimg.cn/large/6d9475f6jw1ez8nly6izyj209w05b74r.jpg)

#### DataFrame可以stack成拥有⼆重MultiIndex的Series

```

data_for_multi1.unstack().stack()

```

输出结果：

```

Row_1  Col_1     0

       Col_2     1

       Col_3     2

       Col_4     3

Row_2  Col_1     4

       Col_2     5

       Col_3     6

       Col_4     7

Row_3  Col_1     8

       Col_2     9

       Col_3    10

       Col_4    11

Row_4  Col_1    12

       Col_2    13

       Col_3    14

       Col_4    15

dtype: int32

```

### 非平衡数据的例子：

```

multi2 = pd.Index([('Row_'+str(x+1),'Col_'+str(y+1)) for x in xrange(5) for y in xrange(x)])

multi2

```

输出结果：

```

MultiIndex(levels=[[u'Row_2', u'Row_3', u'Row_4', u'Row_5'], [u'Col_1', u'Col_2', u'Col_3', u'Col_4']],

           labels=[[0, 1, 1, 2, 2, 2, 3, 3, 3, 3], [0, 0, 1, 0, 1, 2, 0, 1, 2, 3]])

```

```

data_for_multi2 = pd.Series(np.arange(10),index = multi2) data_for_multi2

```

输出结果：

```

Row_2  Col_1    0

Row_3  Col_1    1

       Col_2    2

Row_4  Col_1    3

       Col_2    4

       Col_3    5

Row_5  Col_1    6

       Col_2    7

       Col_3    8

       Col_4    9

dtype: int32

```

### DateTime标准库如此好⽤，你值得拥有

```

import datetime

dates = [datetime.datetime(2015,1,1),datetime.datetime(2015,1,8),datetime.datetime(2015,1,30)]

pd.DatetimeIndex(dates)

```

输出结果：

```

DatetimeIndex(['2015-01-01', '2015-01-08', '2015-01-30'], dtype='datetime64[ns]', freq=None, tz=None)
```

#### 如果你不仅需要时间格式统一，时间频率也要统一的话

```

periodindex1 = pd.period_range('2015-01','2015-04',freq='M')

print periodindex1

```

输出结果：

```

PeriodIndex(['2015-01', '2015-02', '2015-03', '2015-04'], dtype='int64', freq='M')

```

#### 月级精度和日级精度如何转换？

有的公司统⼀以1号代表当月，有的公司统一以最后1天代表当⽉，转化起来很麻烦，可以`asfreq`

```

print periodindex1.asfreq('D',how='start')

print periodindex1.asfreq('D',how='end')

```

输出结果：

```

PeriodIndex(['2015-01-01', '2015-02-01', '2015-03-01', '2015-04-01'], dtype='int64', freq='D')

PeriodIndex(['2015-01-31', '2015-02-28', '2015-03-31', '2015-04-30'], dtype='int64', freq='D')

```

#### 最后的最后，我要真正把两种频率的时间精度匹配上？

```

periodindex_mon = pd.period_range('2015-01','2015-03',freq='M').asfreq('D',how='start')

periodindex_day = pd.period_range('2015-01-01','2015-03-31',freq='D')

print periodindex_mon

print periodindex_day

```

输出结果：

```

PeriodIndex(['2015-01-01', '2015-02-01', '2015-03-01'], dtype='int64', freq='D')

PeriodIndex(['2015-01-01', '2015-01-02', '2015-01-03', '2015-01-04',

             '2015-01-05', '2015-01-06', '2015-01-07', '2015-01-08',

             '2015-01-09', '2015-01-10', '2015-01-11', '2015-01-12',

             '2015-01-13', '2015-01-14', '2015-01-15', '2015-01-16',

             '2015-01-17', '2015-01-18', '2015-01-19', '2015-01-20',

             '2015-01-21', '2015-01-22', '2015-01-23', '2015-01-24',

             '2015-01-25', '2015-01-26', '2015-01-27', '2015-01-28',

             '2015-01-29', '2015-01-30', '2015-01-31', '2015-02-01',

             '2015-02-02', '2015-02-03', '2015-02-04', '2015-02-05',

             '2015-02-06', '2015-02-07', '2015-02-08', '2015-02-09',

             '2015-02-10', '2015-02-11', '2015-02-12', '2015-02-13',

             '2015-02-14', '2015-02-15', '2015-02-16', '2015-02-17',

             '2015-02-18', '2015-02-19', '2015-02-20', '2015-02-21',

             '2015-02-22', '2015-02-23', '2015-02-24', '2015-02-25',

             '2015-02-26', '2015-02-27', '2015-02-28', '2015-03-01',

             '2015-03-02', '2015-03-03', '2015-03-04', '2015-03-05',

             '2015-03-06', '2015-03-07', '2015-03-08', '2015-03-09',

             '2015-03-10', '2015-03-11', '2015-03-12', '2015-03-13',

             '2015-03-14', '2015-03-15', '2015-03-16', '2015-03-17',

             '2015-03-18', '2015-03-19', '2015-03-20', '2015-03-21',

             '2015-03-22', '2015-03-23', '2015-03-24', '2015-03-25',

             '2015-03-26', '2015-03-27', '2015-03-28', '2015-03-29',

             '2015-03-30', '2015-03-31'],

            dtype='int64', freq='D')

```



#### 粗粒度数据＋`reindex`＋`ffill/bfill`

```

full_ts = pd.Series(periodindex_mon,index=periodindex_mon).reindex(periodindex_day,method='ffill')

full_ts

```

![粒度数据](http://ww4.sinaimg.cn/large/6d9475f6jw1ez8obdrmi6j20ob0aqgn0.jpg)

### 关于索引，⽅便的操作有？

前⾯描述过了，索引有序，重复，但⼀定程度上⼜能通过key来访问，也就是说，某些集合操作都是可以⽀持的。

```

index1 = pd.Index(['A','B','B','C','C'])

index2 = pd.Index(['C','D','E','E','F'])

index3 = pd.Index(['B','C','A'])

print index1.append(index2)

print index1.difference(index2)

print index1.intersection(index2)

print index1.union(index2) # Support unique-value Index well

print index1.isin(index2)

print index1.delete(2)

print index1.insert(0,'K') # Not suggested

print index3.drop('A') # Support unique-value Index well

print index1.is_monotonic,index2.is_monotonic,index3.is_monotonic

print index1.is_unique,index2.is_unique,index3.is_unique

```

输出结果：

```

Index([u'A', u'B', u'B', u'C', u'C', u'C', u'D', u'E', u'E', u'F'], dtype='object')

Index([u'A', u'B'], dtype='object')

Index([u'C', u'C'], dtype='object')

Index([u'A', u'B', u'B', u'C', u'C', u'D', u'E', u'E', u'F'], dtype='object')

[False False False  True  True]

Index([u'A', u'B', u'C', u'C'], dtype='object')

Index([u'K', u'A', u'B', u'B', u'C', u'C'], dtype='object')

Index([u'B', u'C'], dtype='object')

True True False

False False True

```




# 大熊猫世界来去自如：Pandas的I/O

老生常谈，从基础来看，我们仍然关心pandas对于与外部数据是如何交互的。

## 结构化数据输入输出

+ read_csv与to_csv 是⼀对输⼊输出的⼯具，read_csv直接返回pandas.DataFrame，⽽to_csv只要执行命令即可写文件

    + read_table：功能类似

    + read_fwf：操作fixed width file

+ read_excel与to_excel方便的与excel交互

+ header 表⽰数据中是否存在列名，如果在第0行就写就写0，并且开始读数据时跳过相应的行数，不存在可以写none

+ names 表示要用给定的列名来作为最终的列名

+ encoding 表⽰数据集的字符编码，通常而言一份数据为了⽅便的进⾏⽂件传输都以utf-8作为标准

这里用的是自己的一个`csv`数据，因为找不到参考的这个pdf中的数据。

```

cnames=['经度','纬度']

taxidata2 = pd.read_csv('20140401.csv',header = 4,names=cnames,encoding='utf-8')

taxidata2

```

![header=0](http://ww2.sinaimg.cn/large/6d9475f6jw1ez9lm7fzejj20em09pq4t.jpg)

![header=4](http://ww4.sinaimg.cn/large/6d9475f6jw1ez9lmxnonvj20ix0a3jtj.jpg)

全部参数的请移步API：

http://pandas.pydata.org/pandas-docs/stable/generated/pandas.read_csv.html#pandas.read_csv

这里介绍一些常用的参数：

读取处理：

+ skiprows：跳过⼀定的⾏数

+ nrows：仅读取⼀定的⾏数

+ skipfooter：尾部有固定的⾏数永不读取

+ skip_blank_lines：空⾏跳过



内容处理：

+ sep/delimiter：分隔符很重要，常⻅的有逗号，空格和Tab('\t')

+ na_values：指定应该被当作na_values的数值

+ thousands：处理数值类型时，每千位分隔符并不统⼀ (1.234.567,89或者1,234,567.89都可能)，此时要把字符串转化为

数字需要指明千位分隔符

收尾处理：

+ index_col：将真实的某列（列的数⺫，甚⾄列名）当作index

+ squeeze：仅读到⼀列时，不再保存为pandas.DataFrame⽽是pandas.Series

## Excel ... ?

对于存储着极为规整数据的Excel而言，其实是没必要一定用Excel来存，尽管Pandas也十分友好的提供了I/O接口。

```

taxidata.to_excel('t0401.xlsx',encoding='utf-8')

taxidata_from_excel = pd.read_excel('t0401.xlsx',header=0, encoding='utf-8')

taxidata_from_excel

```

注意：当你的xls文件行数很多超过65536时，就会遇到错误，解决办法是将写入的格式变为`xlsx`。[excel函数受限制问题](http://stackoverflow.com/questions/17879805/xlwt-limiting-the-number-of-rows)

唯一重要的参数：sheetname=k，标志着一个excel的第k个sheet页将会被取出。（从0开始）

## 半结构化数据

JSON：网络传输中常⽤的⼀种数据格式。

仔细看一下，实际上这就是我们平时收集到异源数据的风格是一致的：

+ 列名不能完全匹配

+ key可能并不唯一

+ 元数据被保存在数据里

```

import json

json_data = [{'name':'Wang','sal':50000,'job':'VP'},\

 {'name':'Zhang','job':'Manager','report':'VP'},\

 {'name':'Li','sal':5000,'report':'IT'}]

data_employee = pd.read_json(json.dumps(json_data))

data_employee_ri = data_employee.reindex(columns=['name','job','sal','report'])

data_employee_ri

```

输出结果：

![](http://ww2.sinaimg.cn/large/6d9475f6jw1ez9mm97wqqj20if07egn7.jpg)



# 深入Pandas数据操纵

在前面部分的基础上，数据会有更多种操纵方式：

+ 通过列名、行index来取数据，结合ix、iloc灵活的获取数据的一个子集（第一部分已经介绍）

+ 按记录拼接（就像Union All）或者关联（join）

+ 方便的统计函数与⾃定义函数映射

+ 排序

+ 缺失值处理

+ 与Excel一样灵活的数据透视表（在第四部分更详细介绍）

## 数据集整合

### 横向拼接：直接DataFrame

```

pd.DataFrame([np.random.rand(2),np.random.rand(2),np.random.rand(2)],columns=['C1','C2'])

```

### 横向拼接：Concatenate

```

pd.concat([data_employee_ri,data_employee_ri,data_employee_ri])

```

输出结果

![](http://ww4.sinaimg.cn/large/6d9475f6jw1ez9mxf9uulj20he09pwg0.jpg)



 ### 纵向拼接：Merge


根据数据列关联，使用on关键字

+ 可以指定一列或多列

+ 可以使⽤left_on和right_on

```

pd.merge(data_employee_ri,data_employee_ri,on='name')

```

![](http://ww1.sinaimg.cn/large/6d9475f6jw1ez9mzcvkabj20g104dt9s.jpg)


![](http://ww4.sinaimg.cn/large/6d9475f6jw1ez9n2n6czpj20g904ejs9.jpg)

根据index关联，可以直接使用left_index和right_index


TIPS: 增加how关键字，并指定

+ how = 'inner'

+ how = 'left'

+ how = 'right'

+ how = 'outer'

结合how，可以看到merge基本再现了SQL应有的功能，并保持代码整洁

## 自定义函数映射

```

dataNumPy32 = np.asarray([('Japan','Tokyo',4000),('S.Korea','Seoul',1300),('China','Beijing',9100)])

DF32 = pd.DataFrame(dataNumPy32,columns=['nation','capital','GDP'])

DF32

```

![](http://ww3.sinaimg.cn/large/6d9475f6jw1ez9nnizyfkj20lq05d0tu.jpg)

### map: 以相同规则将1列数据作1个映射，也就是进行相同函数的处理

```

def GDP_Factorize(v):

    fv = np.float64(v)

    if fv > 6000.0:

         return 'High'

    elif fv < 2000.0:

         return 'Low'

    else:

         return 'Medium'



DF32['GDP_Level'] = DF32['GDP'].map(GDP_Factorize)

DF32['NATION'] = DF32.nation.map(str.upper)

DF32

```

![map](http://ww2.sinaimg.cn/large/6d9475f6jw1ez9ns3fprvj20dy03ljs3.jpg)



## 排序



+ sort: 按⼀列或者多列的值进行行级排序

+ sort_index: 根据index⾥的取值进行排序，而且可以根据axis决定是重排行还是列

### sort

```

dataNumPy33 = np.asarray([('Japan','Tokyo',4000),('S.Korea','Seoul',1300),('China','Beijing',9100)])

DF33 = pd.DataFrame(dataNumPy33,columns=['nation','capital','GDP'])

DF33

```

![](http://ww2.sinaimg.cn/large/6d9475f6jw1ez9o299de1j20nd05bgmp.jpg)

```

DF33.sort(['capital','nation'],ascending=False)

```

![](http://ww2.sinaimg.cn/large/6d9475f6jw1ez9o3xykzej20dj04et98.jpg)

`ascending`是降序的意思。

### sort_index

```

DF33.sort_index(axis=1,ascending=True)

```

![](http://ww2.sinaimg.cn/large/6d9475f6jw1ez9o77u4oej20de04jt97.jpg)

### 一个好用的功能：Rank

```

DF33.rank()

```

![](http://ww3.sinaimg.cn/large/6d9475f6jw1ez9oa7xelij20dx04k0sy.jpg)



## 缺失数据处理

![](http://ww2.sinaimg.cn/large/6d9475f6jw1ez9oh1x2a8j20d209n3zp.jpg)

### 忽略缺失值：

```

DF34.mean(skipna=True)

```

不忽略缺失值的话，估计就不能计算均值了吧。

如果不想忽略缺失值的话，就需要祭出fillna了：

![](http://ww2.sinaimg.cn/large/6d9475f6jw1ez9ojdw89tj20f70dtwg4.jpg)



注：这里我在猜想，`axis=1`是不是就代表从`行`的角度呢？还是得多读书查资料呀。



# “一组”大熊猫：Pandas的groupby

groupby的功能类似SQL的group by关键字：

Split-Apply-Combine

+ Split，就是按照规则分组

+ Apply，通过⼀定的agg函数来获得输⼊pd.Series返回⼀个值的效果

+ Combine，把结果收集起来

Pandas的groupby的灵活性：

+ 分组的关键字可以来⾃于index，也可以来⾃于真实的列数据

+ 分组规则可以通过⼀列或者多列

没有具体数据，截图看一下吧，方便日后回忆。

![](http://ww4.sinaimg.cn/large/6d9475f6jw1ez9oqvhpvoj20lz0jggqk.jpg)

分组可以快速实现`MapReduce`的逻辑

+ Map: 指定分组的列标签，不同的值就会被扔到不同的分组处理

+ Reduce: 输入多个值，返回1个值，一般可以通过agg实现，agg能接受1个函数















# 参考：
+ **S1EP3_Pandas.pdf** 不知道什么时候存到电脑里的资料，今天发现了它。感谢作者的资料。






















----
