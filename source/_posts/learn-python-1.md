---
title: Python 笔记 - 简单语法1
date: 2018-05-19 08:37:59
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/python1.png
thumbnailImagePosition: left
tags: 
- python
comments: false
metaAlignment: center
categories: 技术博文
---
随着人工智能，数据分析的势头，Python 被应用到更广泛的场景中。作为世界上增长最快的编程语言，重要性不言而喻。所以，还有什么理由不去学习呢？
<!-- more -->
#### 一、开场白
`Python` 是一门简单易学且功能强大的编程语言。它拥有高效的高级数据结构，并且能够用简单而又高效的方式进行面向对象编程。`Python` 优雅的语法和动态类型，再结合它的解释性，使其在大多数平台的许多领域中成为编写脚本或开发应用程序的理想语言。

{% alert success %}
简单来说，python 的用途很广，可以用来干很多有趣的事情，这就是兴趣所在。
{% endalert %}

#### 二、关于 Python
摘录了几条关于 `python` 的特性，大致如下，大家现有个整体的认识：
- Python 是一门解释型语言，因为无需编译和链接。
- Python 更易于使用，无论在 Windows、Mac OS X 或 Unix 操作系统上它都会帮助你更快地完成任务。
- 完整的编程语言，内置支持高级的数据结构类型
- Python 允许你将程序分割为不同的模块，以便在其他的 Python 程序中重用。
- Python 让程序编写的紧凑和可读。
- Python 是可扩展的。
- ...

#### 三、相关文档
- [官网地址](https://www.python.org)
- [Python 入门指南](http://www.runoob.com/manual/pythontutorial/docs/html/)

{% alert info %}
这里先列出前期需要了解到的 Python 文档，后续我会逐步引入更多。
{% endalert %}

#### 四、快速开始
1.安装
```
brew install python
```
{% alert info %}
默认会安装 python3 ，如果需要安装 2.7 版本，请使用 brew install python@2
{% endalert %}

2.Hello world
命令行输入 `python`，默认会直接打开 `python` 的执行环境。
```
python
print 'jartto: hello world!'
```

#### 五、简单运算符
```
>>> 5*2
>>> round(_,2) #保留两位小数
>>> s = 'First line.\nSecond line.'
>>> print s
```

{% alert info %}
保存之前的值使用下划线，只读属性，不能赋值。
{% endalert %}

如果你前面带有 \ 的字符被当作特殊字符，你可以使用 原始字符串，方法是在第一个引号前面加上一个 r:
```
>>> print 'C:\some\name'  # here \n means newline!
C:\some
ame
>>> print r'C:\some\name'  # note the r before the quote
C:\some\name
```

处理多行，使用“”“
```
>>> print """a
... b
... c
... d"""
a
b
c
d
```

#### 六、字符串操作
1.字符串连接
```
>>> 3*'num'+1
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: cannot concatenate 'str' and 'int' objects
>>> 3 * 'num' + 1
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: cannot concatenate 'str' and 'int' objects
>>> 3 * 'un' + 'ium'
'unununium'
>>> 3 * 'num' + '1'
'numnumnum1'
>>>
```

2.相邻的两个字符串文本自动连接在一起
```
>>> 'Jartto ' 'blog'
'jartto blog'
```
