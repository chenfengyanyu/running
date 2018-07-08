---
title: Python 笔记2 - 常用操作
date: 2018-06-12 08:43:39
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/python1.png
thumbnailImagePosition: left
tags: 
- python
comments: false
metaAlignment: center
categories: 技术博文
---
继续[上一节](http://jartto.wang/2018/05/19/learn-python-1/)，我们来补充一些常用语法。很简单，主要是了解一些 Python 语言的特性，为后续使用做好铺垫。OK，快来开始吧！
<!-- more -->
#### 一、关于 if else
`if else` 和其他语言使用一样，但是需要注意简写形式：
```python
if number < 100:
    print('小于 100')
elif number < 200:
    print('大于 100，小于 200')
else:
    print('大于 200')
```

{% alert warning %}
这里需要注意：elif
{% endalert %}

真值检测就更简单了：
```python
if x:
  print('Jartto\'s blog')
```

#### 二、判断是否包含
- 第一种方法：in
```python
string = 'hi,jartto!'
if 'jartto' in string: # true 
　　print 'Exist'
else:
　　print 'Not exist'
```
- 第二种方法：find
```python
string = 'hi,jartto!'
if string.find('jartto') === 3:
　　print 'Exist'
else:
　　print 'Not exist'
```
  或者如下：
  ```python
  str = '- jartto'
  str.find('-') # 0
  str.find('[') # -1
  ```

- 第三种方法：index，此方法与 find 作用类似，也是找到字符起始的序号
```python
if string.index('jartto') > -1:
　　print 'Exist'
```
  {% alert danger %}
  这里需要注意：index 大于 －1 就说明包含。如果没找到，程序会抛出异常！
  {% endalert %}

#### 三、Python 如何取括号中的字符串？
如果你恰好要处理一下字符串操作，譬如：我们将 `Markdown` 文件中的链接全部取出来，这时候就需要使用正则了。
```python
import re
str1 = '(1, 0.123) (2, 0.234)'
re.findall(r'[(](.*?)[)]', str1)

# 输出：['1, 0.123', '2, 0.234']
result.append(re.findall(r'[[](.*?)[]]',str)[0])
```

{% alert info %}
使用正则的话，需要先引入内置模块 re，这在后续章节中会深入介绍。
{% endalert %}

当然，这仅仅为了说明一些处理思路，正则操作会比你想象的更加复杂和强大。

#### 四、字符串特殊操作
通常我们用的最多的可能是一些类似 `trim` 之类的方法，去掉首尾空格。`Python` 中也有类似的操作，不过更加强大。这里我们来介绍几个方法。
- 去掉两端字符串： strip(), rstrip(),lstrip()
```python
text = '  ----- http://jartto.wang +++++    '
# 删除两边空字符
print(text.strip()) # '----- http://jartto.wang +++++'
# 删除右边空字符
print(text.rstrip()) # '  ----- http://jartto.wang +++++'
# 删除左边空字符
print(text.lstrip()) # '----- http://jartto.wang +++++    '

```
  更高级的用法来，我们甚至可以指定去掉的字符，如下：
  ```python
  text = '  ----- http://jartto.wang +++++    '
  # 删除两边 -+ 和空字符
  print(text.strip().strip('-+')) # ' http://jartto.wang '
  ```

- 删除单个固定位置字符
```python
text = 'name:jartto'
# 字符串拼接方式去除冒号
news = text[:4] + text[5:]
print(news) #namejartto
```

- 字符替换：replace(), re.sub()
```python
text = '\njartto\n'
text.replace('\n','') # jartto
```
  {% alert info %}
  很简单，replace 和 js 中的 replace 用法完全一样。
  {% endalert %}

  那如果是同时替换多个字符呢，我们总不能写个遍历吧。这时候就需要使用 `re.sub()` 方法了。
  ```python
  import re
  text = '\njartto\tblog'
  print(re.sub('[\n\t]','',text))
  ```
  {% alert success %}
  re 模块后续会深入介绍，这里能看懂基本使用就可以了啦。
  {% endalert %}

- 去掉 unicode 字符中音调
```python
import sys
import unicodedata
s = "Zhào Qián Sūn Lǐ Zhōu Wú Zhèng Wáng"
remap = {
    # ord返回ascii值
    ord('\t'): '',
    ord('\f'): '',
    ord('\r'): None
    }
# 去除\t, \f, \r
a = s.translate(remap)
'''
　　通过使用dict.fromkeys() 方法构造一个字典，每个Unicode 和音符作为键，对于的值全部为None
　　然后使用unicodedata.normalize() 将原始输入标准化为分解形式字符
　　sys.maxunicode : 给出最大Unicode代码点的值的整数，即1114111（十六进制的0x10FFFF）。
　　unicodedata.combining:将分配给字符chr的规范组合类作为整数返回。 如果未定义组合类，则返回0。
'''
cmb_chrs = dict.fromkeys(c for c in range(sys.maxunicode) if unicodedata.combining(chr(c))) #此部分建议拆分开来理解
b = unicodedata.normalize('NFD', a)
'''
　　　调用translate 函数删除所有重音符
'''
print(b.translate(cmb_chrs))
```

  更多细节，请参考：[Python - 去除字符串中不想要的字符](https://www.cnblogs.com/2bjiujiu/p/7257744.html)。


#### 五、数组过滤
1.如何找出 `a` 数组 的子集 `b` 数组，与 `a` 数组的不同项？
```python
a = [1, 22, 33, 44]
b = [1, 22, 33]
c = [i for i in a if i not in b] # 44
```
2.更骚气的操作
```python
a = set([1, 2, 3])
b = set([2])
print(list(a - b))
```

{% alert success %}
可以看到，Python 对数据的处理细致入微，无不显示强大之处。
{% endalert %}

#### 六、字符串拼接
那常用的字符串拼接，`Python` 中是如何处理的呢？
1.加号连接
```python
>>> a = 'jartto'
>>> b = a + 'blog'
>>> print(b)
```

2.相邻的两个字符串文本自动连接在一起
```python
>>> 'Jartto ' 'blog'
'jartto blog'
```

3.使用 `format` 进行字符串连接：
```python
import sys
test = '{}/jartto.txt'.format(sys.path[0])
```

#### 八、小结
可能大家或多或少都看出来了，我并没有按照 `Python` 官网的文档去介绍，而是介绍了一些常用操作。因为语法介绍到这里可能就结束了，后续我们将偏向于实际应用。这些应用可能覆盖很多范围，但最终目的都是促进我们对 `Python` 的深入学习与理解。

下一篇：[Python 笔记3 - open 文件操作](http://jartto.wang/2018/06/24/learn-python-3/)