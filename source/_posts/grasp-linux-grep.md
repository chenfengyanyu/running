---
title: 掌握linux命令grep
date: 2016-10-12 23:31:28
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/blog_linux.png
thumbnailImagePosition: left
tags: 
- linux
- grep
comments: true
metaAlignment: center
categories: 技术博文
---
一直忽略了 linux 中 grep 的用途，在无意间使用了几次之后，深深的被 grep 所吸引。索性查阅文档，掌握其用法。
<!-- more -->
借用《写给大家看的设计书》中的一句话：一旦能够说出什么东西的名字，就会很容易注意到它。你就会掌握它，拥有它，使它在你的控制中。

#### 一、grep概述
grep（global search regular expression(RE) and print out the line，全面搜索正则表达式并把行打印出来）应该算是linux系统中一个非常强大的文本检索工具，它能使用正则表达式搜索文本，并把匹配的行打印出来。
{% alert info %}
这为我们在海量文档中搜索有效消息提供了便利而又高效的方式，所以快来掌握它的基本用法吧！
{% endalert %}

#### 二、抛砖引玉
场景一：打印系统进程，并找到有效信息。
```sh
ps -ef | grep 'jartto' -i
```
场景二：git 中查找某个文件的369行更改纪录。
```sh
git blame main.js | grep 369
```
场景三：对日志进行过滤，方便git版本回退。
```sh
git log --oneline | grep 'add'
```
输出：
```sh
88f81ae jartto:add image
bfd4d65 jartto:add api
25ac3d4 jartto:add index.jade
```
场景四：使用正则匹配更复杂的检测条件。
```sh
echo this is a test line. | grep -o -E "[a-z]+\."
```
输出：
```sh
test.
```
场景五：查看磁盘空间并过滤较大文件
```sh
df -h
```
输出：
```sh
Filesystem      Size   Used  Avail Capacity  iused   ifree %iused  Mounted on
/dev/disk1     112Gi  105Gi  6.3Gi    95% 27676760 1649542   94%   /
devfs          183Ki  183Ki    0Bi   100%      634       0  100%   /dev
map -hosts       0Bi    0Bi    0Bi   100%        0       0  100%   /net
map auto_home    0Bi    0Bi    0Bi   100%        0       0  100%   /home
```
假如数据很多，我们要找到占用空间较大的文件：
```sh
df -h | grep 'G' -n
```
输出结果：
```sh
2:/dev/disk1     112Gi  105Gi  6.3Gi    95% 27676905 1649397   94%   /
```
可以看到，第二行的/dev/disk1已经达到112G了，我们可以对其监控然后做相应的处理。

当然，还有很多场景，先喝下这碗开胃汤，下面我来详细说明。

#### 三、参数介绍
我们先打出grep的用法示例，如下：
```sh
usage: grep [-abcDEFGHhIiJLlmnOoqRSsUVvwxZ] [-A num] [-B num] [-C[num]]
       	[-e pattern] [-f file] [--binary-files=value] [--color=when]
       	[--context[=num]] [--directories=action] [--label] [--line-buffered]
       	[--null] [pattern] [file ...]
 ```
嗯，看起来有些复杂，完全不知所云，翻译一下：
```sh
grep [options]
-c：只输出匹配行的计数。
-i：不区分大小写(只适用于单字符)。
-h：查询多文件时不显示文件名。
-l：查询多文件时只输出包含匹配字符的文件名。
-n：显示匹配行及行号。
-s：不显示不存在或无匹配文本的错误信息。
-v：显示不包含匹配文本的所有行。
pattern正则表达式主要参数：
\： 忽略正则表达式中特殊字符的原有含义。
^：匹配正则表达式的开始行。
$: 匹配正则表达式的结束行。
\<：从匹配正则表达 式的行开始。
\>：到匹配正则表达式的行结束。
[ ]：单个字符，如[A]即A符合要求 。
[ - ]：范围，如[A-Z]，即A、B、C一直到Z都符合要求 。
.：所有的单个字符。
* ：有字符，长度可以为0。
```

#### 四、用法示例
这里我将列举一些日常工作中较为常见的示例，查看更为详细的命令请移步[linux grep命令](http://www.cnblogs.com/end/archive/2012/02/21/2360965.html)。

显示所有以d开头的文件中包含 test的行：
```sh
grep 'test' d*
```
显示在aa，bb，cc文件中匹配test的行：
```sh
grep 'test' aa bb cc
```
显示所有包含每个字符串至少有5个连续小写字符的字符串的行：
```sh
grep ‘[a-z]\{5\}’ aa
```
在’/usr/src/Linux/’目录下搜索带字符串'log'的文件：
```sh
grep log /usr/src/Linux/*
```
查看test文件，查询出包含a，b字母的结果：
```sh
more test.txt | grep '[a-b]'
```
{% alert info %}
说明：将文件读成流，然后grep后正则匹配，输出结果。
{% endalert %}
查找js中包含log字符串的文件：
```sh
grep 'log' *.js
```
显示所有匹配'jartto'的行和行号：
```sh
ps -ef | grep -n 'jartto'
```
输出结果：
```sh
272:  501 10861 92770   0 12:16下午 ttys005    0:00.00 grep --color=auto --exclude-dir=.bzr --exclude-dir=CVS --exclude-dir=.git --exclude-dir=.hg --exclude-dir=.svn -n jartto
```
如果加上参数i，则忽略匹配字符串的大小写，如下：
```sh
ps -ef | grep -in 'jartto'
```
输出结果：
```sh
262:  501 93853 84378   0  11216  ttys000    0:00.12 /Users/iTerm.app/Contents/MacOS/iTerm2 --server login -fp Jartto
269:    0  2482  2481   0 二09下午 ttys002    0:00.80 login -fp Jartto
272:  501 10898 92770   0 12:22下午 ttys005    0:00.00 grep --color=auto --exclude-dir=.bzr --exclude-dir=CVS --exclude-dir=.git --exclude-dir=.hg --exclude-dir=.svn -in jartto
```
当然，还可以反向输出，详见参数v，如下：
```sh
ps -ef | grep -inv 'jartto'
```
{% alert info %}
说明：-v过滤掉了忽略大小写匹配到的数据。
{% endalert %}
不匹配行首为test的行：
```sh
grep '^[^test]' test.log
```
下面还有一些有意思的命令行参数：
```sh
grep -i pattern files ：不区分大小写地搜索。默认情况区分大小写，
grep -l pattern files ：只列出匹配的文件名，
grep -L pattern files ：列出不匹配的文件名，
grep -w pattern files ：只匹配整个单词，而不是字符串的一部分(如匹配’magic’，而不是’magical’)，
grep -C number pattern files ：匹配的上下文分别显示[number]行，
grep pattern1 | pattern2 files ：显示匹配 pattern1 或 pattern2 的行，
grep pattern1 files | grep pattern2 ：显示既匹配 pattern1 又匹配 pattern2 的行。
grep -n pattern files  即可显示行号信息
grep -c pattern files  即可查找总行数
```
这里还有些用于搜索的特殊符号：
```sh
# \< 和 \> 分别标注单词的开始与结尾
# ‘^’：指匹配的字符串在行首
# ‘$’：指匹配的字符串在行 尾
grep man * 会匹配 ‘Batman’、’manic’、’man’等，
grep ‘\<man’ * 匹配’manic’和’man’，但不是’Batman’，
grep ‘\<man\>’ 只匹配’man’，而不是’Batman’或’manic’等其他的字符串。
```

#### 五、总结
零零散散的举了好多例子，当然，grep的用法可不仅仅局限于此。大家如果感兴趣的话，可以看看下面的参考文档，这里就不细说了。

#### 六、参考文档
1.[linux grep命令](http://www.cnblogs.com/end/archive/2012/02/21/2360965.html)
2.[grep 命令](http://man.linuxde.net/grep)