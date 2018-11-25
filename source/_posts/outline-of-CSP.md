---
title: CSP 概要
date: 2018-11-12 10:32:45
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/csp.png
thumbnailImagePosition: left
tags: 
- 内容安全策略
- CSP
- web
- 安全
comments: false
metaAlignment: center
categories: 技术博文 
---
在开发 Chrome 插件的时候，遇到了 CSP 问题。源于对 CSP 的一知半解，所以折腾了不少时间，索性系统的学习，归纳总结一下。
<!-- more -->
#### 一、起因
我们先来看一个异常：
```
Refused to execute inline script because it violates the following Content Security Policy directive: "script-src 'self' blob: filesystem: chrome-extension-resource:". Either the 'unsafe-inline' keyword, a hash ('sha256-GgRxrVOKNdB4LrRsVPDSbzvfdV4UqglmviH9GoBJ5jk='), or a nonce ('nonce-...') is required to enable inline execution.
```
{% alert info %}
看到了这样的字眼 `Content Security Policy`，所以问题关键就变成了如何处理 `CSP`。
{% endalert %}

`Chrome` 扩展和应用都使用了 `CSP（Content Security Policy）` 声明可以引用哪些资源，但是 `Chrome` 扩展和应用会在我们创建时提供一个默认的值，对于 `Chrome` 扩展来说是 `script-src 'self'; object-src 'self'`。

上面的 `CSP` 规则表示只能引用自身（同域下）的 `JavaScript` 文件和自身的 `object` 元素（如 `Flash` 等），其他资源未做限定。

当然，这是 `Chrome` 插件的硬性限制，可是为什么会有这样的限制，大 `Google` 为何要多此一举？

#### 二、什么是 CSP
带着好奇我们来看一下什么是 `CSP`，`CSP（Content Security Policy）`，内容安全策略，`CSP` 通常是在 `Header` 或者 `HTML` 的 `meta` 标签中定义的，它声明了一系列可以被当前网页合法引用的资源，如果不在 `CSP` 声明的合法范围内，浏览器会拒绝引用这些资源。

{% alert success %}
`CSP` 的主要目的是防止跨站脚本攻击（XSS）。
{% endalert %}

通俗来说：`CSP` 的实质就是白名单策略，开发者明确告诉客户端，哪些外部资源可以加载和执行。开发者只需要简单的配置，其他的交给浏览器来执行或者处理。

#### 三、启用 CSP
{% alert info %}
既然是浏览器的安全策略，那我们如何来启用呢？
{% endalert %}

1.通过 `HTTP` 头信息的 `Content-Security-Policy` 的字段：
```
Content-Security-Policy: script-src 'self'; object-src 'none';
style-src cdn.example.org third-party.org; child-src https:
```

2.也可以通过网页的 `<meta>` 标签设定规则：
```html
<meta http-equiv="Content-Security-Policy" content="script-src 'self'; object-src 'none'; style-src cdn.example.org third-party.org; child-src https:”>
```

不管是从 `HTTP` 来设置，或者是从 `Meta` 来设置，我们看到了共同的属性值：
```
script-src 'self'; object-src 'none'; style-src cdn.example.org third-party.org; child-src https:
```
- 脚本：只信任当前域名
- `<object>` 标签：不信任任何 `URL`，即不加载任何资源
- 样式表：只信任 `cdn.example.org` 和 `third-party.org`
- 框架（`frame`）：必须使用 `HTTPS` 协议加载
- 其他资源：没有限制

#### 四、详细参数
是不是看到这里有些了然了，`CSP` 参数可远不止这些，我们再来多了解一些：

- script-src：外部脚本
- style-src：样式表
- img-src：图像
- media-src：媒体文件（音频和视频）
- font-src：字体文件
- object-src：插件（比如 `Flash`）
- child-src：框架
- frame-ancestors：嵌入的外部资源（比如`<frame>`、`<iframe>`、`<embed>` 和 `<applet>`）
- connect-src：`HTTP` 连接（通过 `XHR`、`WebSockets`、`EventSource` 等）
- worker-src：`worker` 脚本
- manifest-src：`manifest` 文件

#### 五、补充
1.`default-src` 用来设置上面各个选项的默认值。
```
Content-Security-Policy: default-src 'self'
```

{% alert info %}
上面代码限制所有的外部资源，都只能从当前域名加载。
{% endalert %}

如果同时设置某个单项限制（比如 `font-src`  ）和 `default-src`，前者会覆盖后者，即字体文件会采用 `font-src` 的值，其他资源依然采用 `default-src` 的值。

2.`URL` 限制
有时，网页会跟其他 `URL` 发生联系，这时也可以加以限制。
* frame-ancestors：限制嵌入框架的网页
* base-uri：限制 `<base#href>`
* form-action：限制 `<form#action>`

其他一些安全相关的功能，也放在了 `CSP` 里面。
* block-all-mixed-content：HTTPS 网页不得加载 HTTP 资源（浏览器已经默认开启）
* upgrade-insecure-requests：自动将网页上所有加载外部资源的 HTTP 链接换成 HTTPS 协议
* plugin-types：限制可以使用的插件格式
* sandbox：浏览器行为的限制，比如不能有弹出窗口等。

#### 六、高级
有时，我们不仅希望防止 `XSS`，还希望记录此类行为。`report-uri` 就用来告诉浏览器，应该把注入行为报告给哪个网址。
```
Content-Security-Policy: default-src 'self'; ...; report-uri /my_amazing_csp_report_parser;
```

浏览器会使用 `POST` 方法，发送一个 `JSON` 对象，下面是一个例子。
```json
{
  "csp-report": {
    "document-uri": "http://example.org/page.html",
    "referrer": "http://evil.example.com/",
    "blocked-uri": "http://evil.example.com/evil.js",
    "violated-directive": "script-src 'self' https://apis.google.com",
    "original-policy": "script-src 'self' https://apis.google.com; report-uri http://example.org/my_amazing_csp_report_parser"
  }
}
```

除了常规值，`script-src` 还可以设置一些特殊值。注意，下面这些值都必须放在单引号里面。
* `unsafe-inline`：允许执行页面内嵌的 `<script>` 标签和事件监听函数
* `unsafe-eval`：允许将字符串当作代码执行，比如使用 `eval`、`setTimeout`、`setInterval` 和 `Function` 等函数。
* `nonce` ：每次 `HTTP` 回应给出一个授权 `token`，页面内嵌脚本必须有这个 `token`，才会执行
* `hash` ：列出允许执行的脚本代码的 `Hash` 值，页面内嵌脚本的哈希值只有吻合的情况下，才能执行。

`nonce` 值的例子如下，服务器发送网页的时候，告诉浏览器一个随机生成的 `token`。
```
Content-Security-Policy: script-src 'nonce-EDNnf03nceIOfn39fn3e9h3sdfa’
```
页面内嵌脚本，必须有这个 `token` 才能执行。
```html
<script nonce=EDNnf03nceIOfn39fn3e9h3sdfa>
  // some code
</script>
```
`hash` 值的例子如下，服务器给出一个允许执行的代码的 `hash` 值。
```
Content-Security-Policy: script-src 'sha256-qznLcsROx4GACP2dm0UCKCzCG-HiZ1guq6ZZDob_Tng=‘
```
下面的代码就会允许执行，因为hash值相符。
```html
<script>alert('Hello, world.');</script>
```

{% alert warning %}
注意，计算hash值的时候，`<script>` 标签不算在内。
{% endalert %}

#### 七、回顾
{% alert info %}
看完了 `CSP` 的相关内容，我们来解决一下文章开头抛出的那个问题。
{% endalert %}

在了解了 `CSP` 策略后，我们有了两个解决问题的思路：
1.关闭 `CSP`；
2.修改脚本，让其满足 `CSP` 策略；

默认情况下，内容安全策略将不会加载内联脚本，只能加载本地脚本。你可以通过以下方式放宽默认政策：
1.内联脚本：通过官方文档，我们可以在策略中指定源代码的 `base64` 编码散列，可以将内联脚本列入白名单。 
{% alert success %}
最佳实践：将这个逻辑提取到一个单独的脚本，而不是使用内联脚本。
{% endalert %}

2.白名单策略：我们可以设置 `http://jartto.wang/2018/11/12/outline-of-CSP/` 白名单脚本资源，如下：
```
manifest.json "content_security_policy":"script-src 'self' http://jartto.wang/; object-src 'self'"
```

{% alert warning %}
需要注意的是：根据内联脚本的说明，`unsafe-inline` 不再有效。
{% endalert %}

直到 `Chrome 45`，没有放松对内嵌 `JavaScript` 执行限制的机制。尤其是，制定包含不安全内联的脚本策略将不起作用。

#### 八、参考
[Content Security Policy 入门教程](http://www.ruanyifeng.com/blog/2016/09/csp.html)
[Content Security Policy Level 2](https://www.w3.org/TR/2015/CR-CSP2-20150721/#script-src-hash-usage)