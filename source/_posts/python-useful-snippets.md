---
title: Python 代码片段收藏
date: 2019-09-07 11:10:57
categories: Python
tags:
    - Snippet
keywords:
    - 代码片段
---

# list 列表相关

## list 中最小值、最大值

```python
import operator

values = [1, 2, 3, 4, 5]

min_index, min_value = min(enumerate(values), key=operator.itemgetter(1))
max_index, max_value = max(enumerate(values), key=operator.itemgetter(1))

print('min_index:', min_index, 'min_value:', min_value)
print('max_index:', max_index, 'max_value:', max_value)

# Out
min_index: 0 min_value: 1
max_index: 4 max_value: 5
```

## list 中连续元素之间的差

```python
from itertools import islice
ls = [1,2,3,5,8]
diff = [j-i for i,j in zip(ls, islice(ls, 1, None))]
print(diff)

# Out
[1, 1, 2, 3]
```

## 删除列表中的重复元素

下面这种方法不能维持顺序：
```python
x = [1, 8, 4, 5, 5, 5, 8, 1, 8]
list(set(x))

# Out
[8, 1, 4, 5]
```

下面的方法，可以维持顺序：
```python
from collections import OrderedDict
x = [1, 8, 4, 5, 5, 5, 8, 1, 8]
list(OrderedDict.fromkeys(x))

# Out
[1,8,4,5]
```

## 并行遍历2个列表

```python
a = [1, 2, 3]
b = [4, 5, 6]

for (a_val, b_val) in zip(a, b):
    print(a_val, b_val)

# Out
1 4
2 5
3 6
```

## 合并列表值

输入的两个数组，输出一个是数组&值相加或者相乘：

```python
# input
first = [1,2,3,4,5]
second = [6,7,8,9,10]

#output
three = [7,9,11,13,15]

# The zip function is useful here, used with a list comprehension.
# add
[x + y for x, y in zip(first, second)]

# other
[x*y for x, y in zip(first, second)]
[max(x,y) for x, y in zip(first, second)]
```

参考：
- [Python中的常用代码
](http://blog.ourren.com/2015/07/19/pythonzhong-de-chang-yong-dai-ma/)

# 字典处理

## 字典做交、差、并

```python
a={'name':'michael','age':"27",'sex':'male'}
b={'name':'hqh','age':'27'}
{k:a[k] for k in a.keys()-b.keys()}
out: {'sex': 'male'}
dict(a.items()-b.items())
out: {'name': 'michael', 'sex': 'male'}
```
需要注意的是，当字典的值有字典时，`a.items()-b.items()` 这种方式会报错 `TypeError: unhashable type: 'dict'`；

参考：
- [cookbook-查找两字典的相同点](https://python3-cookbook.readthedocs.io/zh_CN/latest/c01/p09_find_commonalities_in_dicts.html)
- [简书-Python 字典操作进阶](https://www.jianshu.com/p/65f4bebc1061)

## 字典的Key与Value对调

```python
m = {'A': 1, 'B': 2, 'C': 3}
invert_map_key_value = lambda m: dict(zip(m.values(), m.keys()))
invert_map_key_value(m)

# output: {1: 'A', 2: 'B', 3: 'C'}
```

参考：
- [2gua-几个Python代码片段](https://zhuanlan.zhihu.com/p/33069486)

## 合并字典值

```python
>>> from collections import Counter
>>> A = Counter({'a':1, 'b':2, 'c':3})
>>> B = Counter({'b':3, 'c':4, 'd':5})
>>> A + B
Counter({'c': 7, 'b': 5, 'd': 5, 'a': 1})
```

## 字典的增加

用 `update` 方法往已有字典中增加键值对：

```python
deploy_info=dict()
for idx, row in raw_data.iterrows():
    temp=dict()
    version = row['version']
    app_comp_name = row['app_comp_name']
    pkg_name = "{}_{}.tar.gz".format(app_comp_name, version)
    time.sleep(2)
    data = get_verify_value(api_url,pkg_name)
    temp = {
        deploy_history_id:{
            'app_comp_name':app_comp_name,
            'version':version,
            'pkg_name':pkg_name,
            'data':data
        }
    }
    deploy_info.update(temp)
```

# 字符串相关

## 索引

```python
tag='hx/mitaka_compute/12.0.0'
[m.start() for m in re.finditer('/',tag)]
```

参考：
- [在Python中查找所有出现的子字符串](https://codeday.me/bug/20170404/7720.html)

## 将百分号的百分比字符串转为数字

```python
p="75%"
float(p.strip('%'))/100
```

参考：
- [Convert to numeric Python](https://www.kaggle.com/grfiv4/convert-to-numeric-python)

## 剔除分隔符

通常做法：
```python
''.join('A|B|C|D|E|F|G'.split('|'))

# output: 'ABCDEFG'
```

用 `itertools.islice`，因为可以节选字符串：
```python
import itertools

''.join(itertools.islice('A|B|C|D|E|F|G', 6, None, 2))
# output: 'DEFG'

''.join(itertools.islice('A|B|C|D|E|F|G', 0, None, 2))
# output: ''ABCDEFG'
```

## 美观打印

```python
import pprint as pp
animals = [{'animal': 'dog', 'legs': 4, 'breeds': ['Border Collie', 'Pit Bull', 'Huskie']}, {'animal': 'cat', 'legs': 4, 'breeds': ['Siamese', 'Persian', 'Sphynx']}]
pp.pprint(animals, width=1)

# Out
[{'animal': 'dog',
  'breeds': ['Border '
             'Collie',
             'Pit '
             'Bull',
             'Huskie'],
  'legs': 4},
 {'animal': 'cat',
  'breeds': ['Siamese',
             'Persian',
             'Sphynx'],
  'legs': 4}]
```
width参数指定一行上最大的字符数。设置width为1确保字典打印在单独的行

# 文件读写

## 基本文件读 txt

```python
# Note: rb opens file in binary mode to avoid issues with Windows systems
# where 'rn' is used instead of 'n' as newline character(s).


# A) Reading in Byte chunks
reader_a = open("file.txt", "rb")
chunks = []
data = reader_a.read(64)  # reads first 64 bytes
while data != "":
    chunks.append(data)
    data = reader_a.read(64)
if data:
    chunks.append(data)
print(len(chunks))
reader_a.close()


# B) Reading whole file at once into a list of lines
with open("file.txt", "rb") as reader_b:   # recommended syntax, auto closes
    data = reader_b.readlines() # data is assigned a list of lines
print(len(data))


# C) Reading whole file at once into a string
with open("file.txt", "rb") as reader_c:
    data = reader_c.read() # data is assigned a list of lines
print(len(data))


# D) Reading line by line into a list
data = []
with open("file.txt", "rb") as reader_d:
    for line in reader_d:
        data.append(line)
print(len(data))
```

## json 读写json文件
- json.loads()是将str转化成dict格式，json.dumps()是将dict转化成str格式。
- json.load()和json.dump()也是类似的功能，只是与文件操作结合起来了。

```python
# 解码
import json
with open('build_info.json','r') as f:
    array = json.load(f)
print(array)
```

在编码JSON的时候，还有一些选项很有用。 如果你想获得漂亮的格式化字符串后输出，可以使用 `json.dumps()` 的`indent`参数。 它会使得输出和pprint() 函数效果类似：
```python
>>> print(json.dumps(data))
{"price": 542.23, "name": "ACME", "shares": 100}
>>> print(json.dumps(data, indent=4))
{
    "price": 542.23,
    "name": "ACME",
    "shares": 100
}
>>>
```

保存为 json 文件：
```python
# 编码
import json
a = {"name":"michael"}
with open("demo.json","w") as f:
	json.dump(a, f, indent=4)
```

- [CookBook-6.2 读写JSON数据](https://python3-cookbook.readthedocs.io/zh_CN/latest/c06/p02_read-write_json_data.html?highlight=json)
- [简书-Python: json模块实例详解](https://www.jianshu.com/p/e29611244810)
- [官宣-json — JSON encoder and decoder](https://docs.python.org/3.6/library/json.html)
- [Reading JSON file with Python 3](https://stackoverflow.com/questions/38644480/reading-json-file-with-python-3)
- [What is the difference between json.load() and json.loads() functions in Python?](https://stackoverflow.com/questions/39719689/what-is-the-difference-between-json-load-and-json-loads-functions-in-python)

# 时间日期

## 基本时间（time）和日期（date）

```python
import time

# print time HOURS:MINUTES:SECONDS
# e.g., '10:50:58'
print(time.strftime("%H:%M:%S"))

# print current date DAY:MONTH:YEAR
# e.g., '05/01/2019'
print(time.strftime("%d/%m/%Y"))

# Out
15:18:03
05/01/2019
```

## 字符串和日期的相互转换

strptime 是将字符串转换为 datetime，其实这个方法的全称是 “string parse time”，叫做字符串解析成时间，重点在解析（parse）:
```python
from datetime import datetime

date_obj = datetime.strptime('2018-10-15 20:59:29', '%Y-%m-%d %H:%M:%S')
print(type(date_obj),date_obj)

# Out
<class 'datetime.datetime'> 2018-10-15 20:59:29
```

strftime 是将 datetime 转换为字符串，全称是 “string format time”，翻译过来就是将字符串的形式来格式化时间，重点在格式化（format），使之以一种可读的字符串形式返回：
```python
from datetime import datetime
date_obj = datetime.now()
date_string = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
print(type(date_string),date_string)

# Out
<class 'str'> 2019-01-05 18:41:04
```
参考：
- [3分钟学会一个小技巧
](https://mp.weixin.qq.com/s?__biz=MjM5MzgyODQxMQ==&mid=2650369197&idx=1&sn=0057a786cb28f9d264e8bdd5ed0a0d15&chksm=be9cd7f989eb5eefa012a502def9c071ac790e70162ec525c8dc43b087c961648ee1a9bb6264&mpshare=1&scene=1&srcid=1115C4UhHFYie2bJLm4K8erU#rd)

# 编码相关

## Python Requests 编码问题
- [requests github issue](https://github.com/requests/requests/issues/1604)

# 下载

## Python下载文件

- [urlretrieve](https://blog.csdn.net/fengzhizi76506/article/details/59229846)

## Python根据url下载目录或者文件

```python
    def download_package(self, package_url):
        print("start download_build_result")
        if not package_url.endswith("/"):
            package_url += '/'
        cmd = "wget -c -r -nd -np -P %s %s" % ("output", package_url)
        print(cmd)
        os.system(cmd)
        print(os.getcwd())
        print("finish download_build_result")
```

# 数据处理

## Python Pandas处理Excel数据

### 逐行处理数据 iterrows

```python
for idx, row in data.iterrows():
    project_name=row['projectName']
    tag_name=row['tagName']
```

### Pandas追加模式写入csv文件

```python
data = pd.DataFrame([[1,2,3]])
csv_headers=['A','B','C']
data.to_csv('./Marvel3_yingpping.csv', header=csv_headers, index=False, mode='a+', encoding='utf-8')
data = pd.DataFrame([[4,5,6]])
data.to_csv('./Marvel3_yingpping.csv', header=False, index=False, mode='a+', encoding='utf-8')
data = pd.DataFrame([[7,8,9]])
data.to_csv('./Marvel3_yingpping.csv', header=False, index=False, mode='a+', encoding='utf-8')
```

## Python-CSV-Excel
- [Python Pandas iterrows method](https://stackoverflow.com/questions/28310409/python-pandas-iterrows-method)

```python
for idx, row in data.iterrows():
    project_name=row['projectName']
    tag_name=row['tagName']
```

## to_csv表格中文乱码

ipython中直接打印df，中文没有乱码，但是`to_csv`方法存储时，中文有乱码。

```python
df.to_csv('file.csv',encoding='utf-8-sig')
```

参考：
- [Pandas df.to_csv(“file.csv” encode=“utf-8”) still gives trash characters for minus sign](https://stackoverflow.com/questions/25788037/pandas-df-to-csvfile-csv-encode-utf-8-still-gives-trash-characters-for-min)
- [关于pandas中,to_csv函数输出的utf8数据用Excel打开是乱码](https://blog.csdn.net/weixin_39461443/article/details/75303072)

## itero

看题目：
- [python数据处理，字典生成的一个问题](https://segmentfault.com/q/1010000006624056?_ea=1081758)答案中有位前辈用这个用的炉火纯青啊！

# Shell/Linux 操作相关

## Python运行shell命令的函数：

```python
def run(cmd_str, fatal=True):
    # this is not a good implement
    log.command(log.term.cmd(cmd_str))
    ret = os.system(cmd_str)
    if ret is not 0:
        if fatal:
            log.error('[ERROR] run cmd: %s failed', cmd_str)
            os._exit(1)
        else:
            log.info('[INFO] %s is not fatal' % cmd_str)
```

## 调用外部的命令

```python
#
import subprocess

subprocess.call(['mkdir', 'empty_folder'])

# 运行一条命令并输出得到的结果
output = subprocess.check_output(['ls', '-l'])

# 上面的调用是阻塞的
# 如果运行shell中内置的命令，如cd或者dir，需要指定标记shell=True
output = subprocess.call(['cd', '/'], shell=True)

# 对于更高级的用例，可以使用 Popen constructor。
```

Python 3.5引进了一个新的run函数，它的行为与call和check_output很相似。如果你使用的是3.5版本或更高版本，看一看run的文档，里面有一些有用的例子。否则，如果你使用的是Python 3.5以前的版本或者你想保持向后兼容性，上面的call和check_output代码片段是你最安全和最简单的选择

参考：
- [](https://python.freelycode.com/contribution/detail/53)

## 计算文件的校验值

可以计算文件的 `md5`、`sha256` 等值
```python
# https://pymotw.com/3/hashlib/index.html#module-hashlib
def get_verify_value(file_path, verify_type):
    """
    计算指定文件的校验值
    :param file_path: 文件路径
    :param verify_type: 校验值类型，md5 sha256 等等
    :return:
    """
    h = hashlib.new(verify_type)
    if not file_path:
        return None
    with open(file_path, 'rb') as f:
        for block in iter(lambda: f.read(4096), b""):
            h.update(block)
    return h.hexdigest()
```

# 性能相关

## 脚本的运行时间

```python
import time

start_time = time.clock()

for i in range(10000000):
    pass

elapsed_time = time.clock() - start_time
print("Time elapsed: {} seconds".format(elapsed_time))

# Out
Time elapsed: 0.30121700000000007 seconds
```

```python
import timeit
elapsed_time = timeit.timeit('for i in range(10000000): pass', number=1)
print("Time elapsed: {} seconds".format(elapsed_time))

# Out
Time elapsed: 0.2051873060000844 seconds
```

## 计算运行时间

```python
class Timer(object):
    def __enter__(self):
        self.error = None
        self.start = time.time()
        return self
    def __exit__(self, type, value, tb):
        self.finish = time.time()
        if type:
            self.error = (type, value, tb)
    def duration(self):
        return self.finish - self.start
with Timer() as timer:
    func()
timer.duration()

# Out
0.29994797706604004
```

参考：
- [CSDN- python代码片段](https://blog.csdn.net/u010571844/article/details/50731541)

# 目录、路径相关

## 基本目录文件操作

```python
import os
import shutil
import glob

# working directory
c_dir = os.getcwd()                 # show current working directory
os.listdir(c_dir)                   # shows all files in the working directory
os.chdir('~/Data')                  # change working directory


# get all files in a directory
glob.glob('/Users/sebastian/Desktop/*')

# e.g.,  ['/Users/sebastian/Desktop/untitled folder', '/Users/sebastian/Desktop/Untitled.txt']

# walk
tree = os.walk(c_dir)
# moves through sub directories and creates a 'generator' object of tuples
# ('dir', [file1, file2, ...] [subdirectory1, subdirectory2, ...]),
#    (...), ...

#check files: returns either True or False
os.exists('../rel_path')
os.exists('/home/abs_path')
os.isfile('./file.txt')
os.isdir('./subdir')


# file permission (True or False
os.access('./some_file', os.F_OK) # File exists? Python 2.7
os.access('./some_file', os.R_OK) # Ok to read? Python 2.7
os.access('./some_file', os.W_OK) # Ok to write? Python 2.7
os.access('./some_file', os.X_OK) # Ok to execute? Python 2.7
os.access('./some_file', os.X_OK | os.W_OK) # Ok to execute or write? Python 2.7

# join (creates operating system dependent paths)
os.path.join('a', 'b', 'c')
# 'a/b/c' on Unix/Linux
# 'a\b\c' on Windows
os.path.normpath('a/b/c') # converts file separators


# os.path: direcory and file names
os.path.samefile('./some_file', '/home/some_file')  # True if those are the same
os.path.dirname('./some_file')  # returns '.' (everythin but last component)
os.path.basename('./some_file') # returns 'some_file' (only last component
os.path.split('./some_file') # returns (dirname, basename) or ('.', 'some_file)
os.path.splitext('./some_file.txt') # returns ('./some_file', '.txt')
os.path.splitdrive('./some_file.txt') # returns ('', './some_file.txt')
os.path.isabs('./some_file.txt') # returns False (not an absolute path)
os.path.abspath('./some_file.txt')


# create and delete files and directories
os.mkdir('./test')  # create a new direcotory
os.rmdir('./test')  # removes an empty direcotory
os.removedirs('./test') # removes nested empty directories
os.remove('file.txt')   # removes an individual file
shutil.rmtree('./test') # removes directory (empty or not empty)

os.rename('./dir_before', './renamed') # renames directory if destination doesn't exist
shutil.move('./dir_before', './renamed') # renames directory always

shutil.copytree('./orig', './copy') # copies a directory recursively
shutil.copyfile('file', 'copy')     # copies a file


# Getting files of particular type from directory
files = [f for f in os.listdir(s_pdb_dir) if f.endswith(".txt")]

# Copy and move
shutil.copyfile("/path/to/file", "/path/to/new/file")
shutil.copy("/path/to/file", "/path/to/directory")
shutil.move("/path/to/file","/path/to/directory")

# Check if file or directory exists
os.path.exists("file or directory")
os.path.isfile("file")
os.path.isdir("directory")

# Working directory and absolute path to files
os.getcwd()
os.path.abspath("file")
```
 参考：
 - [斗大的熊猫-收集的有用的 Python 代码片段](http://blog.topspeedsnail.com/archives/883)

 ## Python 删除文件夹

```python
def onerror(func, path, exc_info):
    """
    Error handler for ``shutil.rmtree``.

    If the error is due to an access error (read only file)
    it attempts to add write permission and then retries.

    If the error is for another reason it re-raises the error.

    Usage : ``shutil.rmtree(path, onerror=onerror)``
    """
    import stat
    if not os.access(path, os.W_OK):
        # Is the error an access error ?
        os.chmod(path, stat.S_IWUSR)
        func(path)
    else:
        raise
```
参考：
- [shutil.rmtree fails on Windows with 'Access is denied'](https://stackoverflow.com/questions/2656322/shutil-rmtree-fails-on-windows-with-access-is-denied)

 ## Python 切换目录

执行完，返回之前目录
```python
import contextlib
@contextlib.contextmanager
def cdir(path):
    prev_cwd = os.getcwd()
    os.chdir(path)
    try:
        yield
    finally:
        os.chdir(prev_cwd)
```

用法：
```python
with cdir(path):
    func()
```

## 搜索指定目录下的文件

将指定目录及其子目录下的文件搜索出来：

```python
def find_file(start_path, name):
    """
    search the files of name from the dir start_path，存放的是搜索文件的路径
    :param start_path: the search scope of dir
    :param name: the name of search file
    :return: set of files path
    """
    files_path = set()
    for rel_path, dirs, files in os.walk(start_path):
        # if name in files:
        for f in files:
            if name in f:
                full_path = os.path.join(start_path, rel_path, f)
                path = os.path.normpath(os.path.abspath(full_path))
                files_path.add(path)
    return files_path
```

## 只列出文件夹下的文件夹

```
[ name for name in os.listdir(thedir) if os.path.isdir(os.path.join(thedir, name)) ]

filter(os.path.isdir, os.listdir(os.getcwd()))
```

- [How to list only top level directories in Python?
](https://stackoverflow.com/questions/141291/how-to-list-only-top-level-directories-in-python)


## Python Path相关问题

```python
os.path.split(r"C:\foo\bar\file_name.txt")
```

- [格式化文件路径](http://stackoverflow.org.cn/front/ask/view?ask_id=811828)

 # 数据库

 ## MySQL 数据库

```python
db = MySQLdb.connect("localhost","your_username","your_password","your_dbname")
cursor = db.cursor()
sql = "select Column1,Column2 from Table1"
cursor.execute(sql)
results = cursor.fetchall()

for row in results:
    print row[0]+row[1]

db.close()
```

参考：
- [斗大的熊猫-最常用的Python代码片段
](http://blog.topspeedsnail.com/archives/3918)

## MongoDB

```python
uri="mongodb://admin:admin@xxx.xxx.xxx.xxx:27017,xxx.xxx.xxx.xxx:27018,xxx.xxx.xxx.xxx:27019/test"
client=pymongo.MongoClient(uri,replicaSet='noah-cluster',readPreference='primaryPreferred')
db=client.get_default_database()
decouple_history=db.rpm_decouple_release_history_info
pprint(decouple_history.find_one({'service_name':'test'}))
```