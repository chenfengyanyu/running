---
title: 关于shell脚本
date: 2016-06-16 18:28:56
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/shell_41ED6A11-DFE0-4BE7-AB20-B543E18F6DB8.png
thumbnailImagePosition: left
tags: 
- shell
comments: true
metaAlignment: center
categories: 技术博文
---
为了搞一个代码自动化部署工具，我花时间简单的学习了一下shell脚本的基本语法。顺着思路缕，很快我就达到了目的，shell果然很是强大啊～
<!-- more -->
李开复曾说过：
> “你现在所做的事情可能看来并没有多大意义，但是不久的将来他们都会起到积极作用。”

工欲善其事，必先利其器，让我们来愉快的学习shell吧！

#### 一、创建文件
```sh
#!/bin/bash
```
“#!” 是一个约定的标记，它告诉系统这个脚本需要什么解释器来执行，即使用哪一种shell。
```sh
echo 'hello world!'
```
echo命令用于向窗口输出文本。

#### 二、先睹为快，如何运行？
方式一
```
chmod +x ./test.sh  #使脚本具有执行权限
./test.sh  #执行脚本
```
{% alert info %}
注意，一定要写成./test.sh，而不是test.sh。运行其它二进制的程序也一样，直接写test.sh，linux系统会去PATH里寻找有没有叫test.sh的，而只有/bin, /sbin, /usr/bin，/usr/sbin等在PATH里，你的当前目录通常不在PATH里，所以写成test.sh是会找不到命令的，要用./test.sh告诉系统说，就在当前目录找。
{% endalert %}

方式二
```sh
/bin/sh test.sh
```

#### 三、定义变量
定义变量时，变量名不加美元符号（$），如：variableName="value"
注意，变量名和等号之间不能有空格，这可能和你熟悉的所有编程语言都不一样。同时，变量名的命名须遵循如下规则：

- 首个字符必须为字母（a-z，A-Z）。
- 中间不能有空格，可以使用下划线（_）。
- 不能使用标点符号。
- 不能使用bash里的关键字（可用help命令查看保留关键字）。

#### 四、输出变量
```sh
#!/bin/bash
PERSON ＝ "jartto"
echo "What is your name?"
read PERSON
echo "Hello, $PERSON"
```

输出变量${PERSON}
```sh
PERSON ＝ "jartto"
echo $PERSON
echo ${PERSON} #推荐
```

#### 五、重新定义变量
已定义的变量，可以被重新定义，如：
```sh
MyBlog="http://jartto.wang/"
echo ${MyBlog}
MyBlog="http://baidu.com/"
echo ${MyBlog}
```
{% alert warning %}
这里需要注意，第二次赋值的时候我们不能写 $MyBlog="baidu.com"，只有使用变量的时候才加（$）。
{% endalert %}

#### 六、只读变量
使用 readonly 命令可以将变量定义为只读变量，只读变量的值不能被改变。

下面的例子尝试更改只读变量，结果报错：
```sh
#!/bin/bash
MyBlog="http://jartto.wang/"
readonly MyBlog
MyBlog="http://baidu.com/"
```
运行脚本，结果如下：
```
/bin/sh: NAME: This variable is read only.
```

#### 七、删除变量
使用 unset 命令可以删除变量。语法：
```sh
unset variable_name
```
{% alert danger %}
变量被删除后不能再次使用；unset 命令不能删除只读变量。
{% endalert %}

#### 八、变量类型
运行shell时，会同时存在三种变量：
1) 局部变量
局部变量在脚本或命令中定义，仅在当前shell实例中有效，其他shell启动的程序不能访问局部变量。

2) 环境变量
所有的程序，包括shell启动的程序，都能访问环境变量，有些程序需要环境变量来保证其正常运行。必要的时候shell脚本也可以定义环境变量。

3) shell变量
shell变量是由shell程序设置的特殊变量。shell变量中有一部分是环境变量，有一部分是局部变量，这些变量保证了shell的正常运行

#### 九、查看文件
```sh
#查看文件
cat REANDME.md
#查看文件，每显示一屏后暂停，回车继续
more package.json
#显示前10行
head README.md
#显示后10行
tail README.md
#计算文件的行数、字数或字符数
wc README.md
#页查看工具，相对于pg提供了更多高级功能，如可以用上下键滚行，PageUp和PageDown翻页等
less app.js
```

参考：
http://c.biancheng.net/cpp/view/6994.html
















