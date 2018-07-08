---
title: Python 笔记3 - Open 文件操作
date: 2018-06-24 20:27:34
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/python1.png
thumbnailImagePosition: left
tags: 
- python
- open
comments: false
metaAlignment: center
categories: 技术博文
---
[简单语法](http://jartto.wang/2018/06/12/learn-python-2/)支撑不起 Python，文件操作却是重中之重。这节我们来探索一下 Python 中内置函数 Open()，让你从此爱上文件操作。
<!-- more -->
#### 一、简介
`Python open()` 函数用于打开一个文件，创建一个 `file` 对象，相关的方法才可以调用它进行读写。

#### 二、Open 文件操作
先来一个示例直观感受一下吧，我们打开一个文件，并读取文件内容：
```python
import sys

result = []
with open(r'{}/README.md'.format(sys.path[0]), 'r') as fp:
  for line in fp.readlines():
    result.append(line)
```

{% alert info %}
代码很简单，先创建了一个空数组，然后按行读取文件内容，并存入数组。
{% endalert %}

存完后，又觉得无聊，我要把读到的内容输出到指定的文件中：
```python
import sys

with open('{}/jartto.txt'.format(sys.path[0]),'w') as f:
  f.writelines(result);
```
{% alert info %}
打开 jartto.txt 文件，将上一步读取到的 result 结果，写入其中。
{% endalert %}

从头到尾我们貌似做了无用功，但是你却对文件读写操作有了整体的认识。我们继续，是时候解开 `Python` 中 `Open()` 函数的面纱了！

#### 三、函数语法
要真正理解 `Open` 方法，我们需要做一些功课：
```
open(name[, mode[, buffering]])
```
参数说明：
- name: 一个包含了你要访问的文件名称的字符串值。
- mode: mode 决定了打开文件的模式：只读，写入，追加等。所有可取值见如下的完全列表。这个参数是非强制的，默认文件访问模式为只读(r)。
- buffering: 如果 buffering 的值被设为 0，就不会有寄存。如果 buffering 的值取 1，访问文件时会寄存行。如果将 buffering 的值设为大于 1 的整数，表明了这就是的寄存区的缓冲大小。如果取负值，寄存区的缓冲大小则为系统默认。

{% alert success %}
关于 `mode` 这里多说一句：最常用的模式可能是 r 和 w
{% endalert %}

参数说明：
- r: 以只读方式打开文件。文件的指针将会放在文件的开头。这是默认模式。
- rb: 以二进制格式打开一个文件用于只读。文件指针将会放在文件的开头。这是默认模式。
- r+: 打开一个文件用于读写。文件指针将会放在文件的开头。
- rb+: 以二进制格式打开一个文件用于读写。文件指针将会放在文件的开头。
- w: 打开一个文件只用于写入。如果该文件已存在则打开文件，并从开头开始编辑，即原有内容会被删除。如果该文件不存在，创建新文件。
- wb: 以二进制格式打开一个文件只用于写入。如果该文件已存在则打开文件，并从开头开始编辑，即原有内容会被删除。如果该文件不存在，创建新文件。
- w+: 打开一个文件用于读写。如果该文件已存在则打开文件，并从开头开始编辑，即原有内容会被删除。如果该文件不存在，创建新文件。
- wb+: 以二进制格式打开一个文件用于读写。如果该文件已存在则打开文件，并从开头开始编辑，即原有内容会被删除。如果该文件不存在，创建新文件。
- a: 打开一个文件用于追加。如果该文件已存在，文件指针将会放在文件的结尾。也就是说，新的内容将会被写入到已有内容之后。如果该文件不存在，创建新文件进行写入。
- ab: 以二进制格式打开一个文件用于追加。如果该文件已存在，文件指针将会放在文件的结尾。也就是说，新的内容将会被写入到已有内容之后。如果该文件不存在，创建新文件进行写入。
- a+: 打开一个文件用于读写。如果该文件已存在，文件指针将会放在文件的结尾。文件打开时会是追加模式。如果该文件不存在，创建新文件用于读写。
- ab+: 以二进制格式打开一个文件用于追加。如果该文件已存在，文件指针将会放在文件的结尾。如果该文件不存在，创建新文件用于读写。

{% alert warning %}
name 和 mode 都不难理解，上文示例已经应用到了，那 buffering 是什么鬼？
{% endalert %}

#### 四、关于 Buffering
官方解释如下：
The optional buffering argument specifies the file’s desired buffer size: 0 means unbuffered, 1 means line buffered, any other positive value means use a buffer of (approximately) that size. A negative buffering means to use the system default, which is usually line buffered for tty devices and fully buffered for other files. If omitted, the system default is used.

- 若 `Buffering` 为 `0` 或 `False` ，没有缓冲区；
```python
import sys

f = open(r'{}/jartto.txt'.format(sys.path[0]), 'w', False)
f.write('Jartto\'s blog') 
```
  去 `jartto.txt` 文件查看，字符串已被写入文件。

- 若为 `1` 或 `True` 有缓冲区；
```python
f = open(r'{}/jartto.txt'.format(sys.path[0]), 'w', True) 
f.write('Jartto\'s blog')  
```
  {% alert info %}
  若缓冲区满了则自动写入文件 jartto.txt 中，否则需要 f.flush() 或 f.close(),才能写入 jartto.txt 文件；
  {% endalert %}


- 若为其他正数则表示缓冲区大小； 
```python
f = open(r'{}/jartto.txt'.format(sys.path[0]), 'w', 20) 
```

  有缓冲区，缓冲区大小 20 字节。当写入字符串少于 20 字节时，先写入缓冲区，需要 `flush()` 或 `close()`,才能写入 `jartto.txt` 文件； 
  当字符串不少于 20 字节时，先写入缓冲区，若缓冲区满了，则自动写入 `jartto.txt` 文件，依次类推，最后缓冲区中的内容需 `f.flush` 或 `f.close()` ,才能写入 `jartto.txt` 文件；

- 若为负数，则使用默认缓冲区大小； 
```python
f = open(r'{}/jartto.txt'.format(sys.path[0]), 'w', -1) 
f.write('Jartto\'s blog') 
```
  先写入缓冲区，若缓冲区满了则自动写入文件 `jartto.txt` 中，否则需要 `f.flush()` 或 `f.close()` 才能写入文件。

{% alert danger %}
这里有个问题，为什么要写入缓冲区，Buffering 是不是可有可无？
{% endalert %}

在网上查到一个关于缓冲区作用的描述：
FileInputStream/FileOutputStream 每次调用 read()/write() 都会触发一个 IO 操作。
BufferedInputStream/BufferedOutputSteam 调用 read()/write() 并不会每次都触发一个 IO 操作，只是写到内部的buffer里面，而只有内部的 buffer 满了或者调用 flush() 才会触发 IO 操作。

{% alert info %}
IO 操作越少，性能越好。
{% endalert %}

操作系统不仅会将硬盘上的文件读到内存作为缓存使用，也会在内存开辟另外一段空间给内存的数据做缓存处理。意义何在？

为了管理，也为了效率。
- 内存与内存之间的数据交换，比内存与硬盘之间的数据交换速率高了两个数量级。
- 当内存上的某个进程需要数据的时候，如果内存缓存有匹配的数据，则直接在缓存区取，如果没有，再去硬盘上读。（写也同理）
- 代码需要进行频繁的IO操作时，意义就体现出来了。

扩展阅读：
- [关于 python open 函数缓冲区](https://blog.csdn.net/u011878795/article/details/52194644)
- [在程序里读取磁盘文件时，为什么需要使用缓冲区](https://segmentfault.com/q/1010000004947600)

#### 五、File 对象方法
`Open()` 函数从来都不是单用的，它与文件操作密不可分，所以请不要离开，再了解一下 `File` 对象方法？
- file.read([size]) size未指定则返回整个文件,如果文件大小>2倍内存则有问题.f.read()读到文件尾时返回""(空字串)
- file.readline() 返回一行
- file.readlines([size]) 返回包含size行的列表,size 未指定则返回全部行
- for line in f: print line #通过迭代器访问
- f.write("hello\n") #如果要写入字符串以外的数据,先将他转换为字符串.
- f.tell() 返回一个整数,表示当前文件指针的位置(就是到文件头的比特数).
- f.seek(偏移量,[起始位置]) 用来移动文件指针.
  - 偏移量:单位:比特,可正可负
  - 起始位置:0-文件头,默认值;1-当前位置;2-文件尾
- f.close() 关闭文件

#### 六、file() 与 open()
两者都能够打开文件，对文件进行操作，也具有相似的用法和参数。但是，这两种文件打开方式却有着本质的区别：
- `file` 为文件类，用 `file()` 来打开文件，相当于这是在构造文件类
- 用 `open()` 打开文件，是用 `python` 的内建函数来操作

又到了留下线索的时候了，下一篇：[Python 笔记4 - OS 与 Sys 模块](#)