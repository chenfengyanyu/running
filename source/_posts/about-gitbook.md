---
title: GitBook 和它有趣的插件
date: 2020-02-02 15:20:03
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/logo/gitbook.png
thumbnailImagePosition: left
tags: 
- gitbook
- plugin
comments: false
metaAlignment: center
categories: 技术博文 
---
相信很多童鞋在日常开发中都会有「说明文档」、「学习笔记」和「个人博客」的需求，那么最经济简洁的方式莫过于 `GitBook` 方案了，`10` 分钟学习，永久使用。
<!-- more -->
本文将简单介绍 `GitBook` 的使用方式以及最佳插件搭配方案，快来运行一个与众不同而且免费托管的个人站点吧！

#### 一、安装与使用
1.**安装 `GitBook` 插件**
```bash
npm install gitbook-cli -g
```

2.**查看安装版本**
```bash
gitbook -V
```
控制台会输出如下信息：
```bash
# Jartto
CLI version: 2.3.2
GitBook version: 3.2.3
```

3.**初始化**
```bash
# 建立项目：
mkdir jartto-gitbook-demo
# 进入目录：
cd jartto-gitbook-demo
# 初始化：
gitbook init
```
此时，`jartto-gitbook-demo` 目录下会自动生成如下文件：
```bash
.
├── README.md
└── SUMMARY.md

0 directories, 2 files
```

4.**启动**
```bash
gitbook serve
```

5.**访问站点**：http://localhost:4000
![网站预览](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/gitbook/demo.png)

{% alert success %}
恭喜你，到这一步我们已经完成了基本版本。
{% endalert %}

更详细的操作，请查看如下文档：
- [官网地址](https://www.gitbook.com/)
- [Github 地址](https://github.com/GitbookIO/gitbook)
- [帮助文档](https://docs.gitbook.com/resources/gitbook-legacy/v2-differences#plugins)

#### 二、重点说明
1.**目录结构**
当我们运行 `gitbook serve` 后，`jartto-gitbook-demo` 目录下会生成一个 `_book` 文件夹：
```bash
.
├── README.md
├── SUMMARY.md
└── _book
    ├── gitbook
    ├── index.html
    └── search_index.json

2 directories, 4 files
```

2.**关于 `README.md`**
```md
# Introduction
```
说明文档，大家应该都不陌生，就不赘述了。

3.**关于 `SUMMARY.md`**
```md
# Summary
* [Introduction](README.md)
```
`SUMMARY.md` 其实就是书的章节目录，我们不妨稍作修改：
```md
# Jartto-GitBook-Demo

* [一、概要](README.md)
    * [1.示例](README.md)
    * [2.说明](README.md)
    * [3.文档](README.md)
* [二、高级](README.md)
    * [1.配置](README.md)
    * [2.插件](README.md)
```
效果如下：
![演示目录](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/gitbook/demo0.png)


#### 三、补充文档
当然，`GitBook` 的远比我们想象的强大，我们还可以通过 `gitbook help` 来查看：
```bash
build [book] [output]       build a book
        --log                   Minimum log level to display (Default is info; Values are debug, info, warn, error, disabled)
        --format                Format to build to (Default is website; Values are website, json, ebook)
        --[no-]timing           Print timing debug information (Default is false)

serve [book] [output]       serve the book as a website for testing
    --port                  Port for server to listen on (Default is 4000)
    --lrport                Port for livereload server to listen on (Default is 35729)
    --[no-]watch            Enable file watcher and live reloading (Default is true)
    --[no-]live             Enable live reloading (Default is true)
    --[no-]open             Enable opening book in browser (Default is false)
    --browser               Specify browser for opening book (Default is )
    --log                   Minimum log level to display (Default is info; Values are debug, info, warn, error, disabled)
    --format                Format to build to (Default is website; Values are website, json, ebook)

install [book]              install all plugins dependencies
    --log                   Minimum log level to display (Default is info; Values are debug, info, warn, error, disabled)

parse [book]                parse and print debug information about a book
    --log                   Minimum log level to display (Default is info; Values are debug, info, warn, error, disabled)

init [book]                 setup and create files for chapters
    --log                   Minimum log level to display (Default is info; Values are debug, info, warn, error, disabled)

pdf [book] [output]         build a book into an ebook file
    --log                   Minimum log level to display (Default is info; Values are debug, info, warn, error, disabled)

epub [book] [output]        build a book into an ebook file
    --log                   Minimum log level to display (Default is info; Values are debug, info, warn, error, disabled)

mobi [book] [output]        build a book into an ebook file
    --log                   Minimum log level to display (Default is info; Values are debug, info, warn, error, disabled)
```

#### 四、有趣插件
了解上面的操作，使用 `GitBook` 已经没有任何障碍了。

{% alert info %}
如果你还想做一些个性化的操作，不妨继续深入。
{% endalert %}

要安装插架，需要我们有配置文件 `book.json`，我们可以在根目录下创建：
```bash
touch book.json
```
写入基本配置：
```json
{
    "title": "Jartto-GitBook-Demo",
    "description": "Jartto-GitBook-Demo",
    "author": "sphard",
    "language": "zh-hans",
    "root": ".",

    "plugins": [
        "donate",
        "github-buttons@2.1.0",
        "edit-link"
    ],

    "pluginsConfig": {
        "donate": {
            "wechat": "http://jartto.wang/images/wechatpay.jpg",
            "alipay": "http://jartto.wang/images/alipay.jpg",
            "title": "",
            "button": "打赏",
            "alipayText": "支付宝打赏",
            "wechatText": "微信打赏"
        },
        "github-buttons": {
            "repo": "jartto/gitbook",
            "types": [
                "star"
            ],
            "size": "small"
        },
        "edit-link": {
            "base": "https://github.com/jartto/gitbook/master",
            "label": "Edit This Page"
        }
    }
}
```

插件安装通用命令：
```
npm install gitbook-plugin-[插件名]
```
例如：我们要安装 `flexible-alerts` 信息框插件：
```
npm install gitbook-plugin-flexible-alerts
```
效果如下：
![信息框插件](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/gitbook/plugin.png)


还有很多可用插件，具体如下：
1. 信息框(`flexible-alerts`)
2. 阅读统计（`pageview-count`）
3. 侧边栏宽度可调节（`splitter`）
4. 页脚版权（`page-copyright`）
5. 打赏功能（`donate`）
6. 分享当前页面（`sharing-plus`）
7. 修改标题栏图标（`custom-favicon`）
8. 复选框（`todo`）
9. 显示图片名称（`image-captions`）
10. 目录折叠（`toggle-chapters`）
11. 分章节展示（`multipart`）
12. 插入 `Logo`（`insert-logo`）
13. `Google` 分析（`ga`）
14. 返回顶部（`back-to-top-button`）
15. 代码添加行号和复制按钮（`code`）
16. 高级搜索，支持中文（`search-pro`）
17. 添加 `Github` 图标（`github`）
...


需要注意的是：
`GitBook` 默认带有 `5` 个插件：
- `highlight`
- `search`
- `sharing`
- `fontsettings`
- `livereload`

如果要去除自带的插件，可以在插件名称前面加 `-`，例如：
```
"plugins":[
    "-search"
]
```

小技巧：`NPM` 中搜索关键字 [`GitBook-Plugin`](https://www.npmjs.com/search?q=gitbook-plugin)，发现更多插件。

#### 五、效果展示
1.`GitBook` 扩展：
![扩展 GitBook](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/gitbook/gitbook1.png)

2.示例一：
![示例一](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/gitbook/demo1.png)

3.示例二：
![示例二](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/gitbook/demo2.png)

4.示例三：
![示例三](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/gitbook/demo3.png)

#### 六、总结
[上文](http://jartto.wang/2020/02/02/about-gitbook/)介绍了 `GitBook` 的基本使用和一些实用插件，构建在线文档变得轻而易举。加上 `Github` 免费的托管平台，我们就可以干更多有趣的事情了。快输出你的 `HTML`、`PDF`、`eBook` 技术文档吧～


