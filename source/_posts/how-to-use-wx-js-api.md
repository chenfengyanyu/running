---
title: 微信js-api的使用
date: 2016-06-26 22:48:38
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/wx_W020140227562108351485.png
thumbnailImagePosition: left
tags: 
- 微信
comments: true
metaAlignment: center
categories: 技术博文
---
微信火了有一段时间了，很多公司都开通了微信的业务。有人做营销，有人做推广，有人杀熟，有人行骗，有人卖鞋卖臭袜子。。。以上的种种行为暴露出一个问题，那就是微信已经成为社交必不可少的一个环节。
<!-- more -->
可是，这对我们有什么启示呢？答案显而易见，作为前端工程师的你，微信开发也就成了一项软实力。所以，趁年轻，多学学，等待厚积薄发的那一刻～

#### 一、绑定域名
[进入微信公众平台](http://mp.weixin.qq.com/)，公众号设置－功能设置－JS接口安全域名，如下图：
![wx](http://7xvi3w.com1.z0.glb.clouddn.com/wx_440611EA-24A1-4599-94C6-3A4887DBEF86.png)

#### 二、引入js

在需要调用JS接口的页面引入如下JS文件，（支持https）：http://res.wx.qq.com/open/js/jweixin-1.0.0.js

{% alert info %}
备注：支持使用 AMD/CMD 标准模块加载方法加载
{% endalert %}

#### 三、通过config接口注入权限验证配置
```js
wx.config({
    debug: true, // 开启调试模式,调用的所有api的返回值会在客户端alert出来，若要查看传入的参数，可以在pc端打开，参数信息会通过log打出，仅在pc端时才会打印。
    appId: '', // 必填，公众号的唯一标识
    timestamp: , // 必填，生成签名的时间戳
    nonceStr: '', // 必填，生成签名的随机串
    signature: '',// 必填，签名，见附录1
    jsApiList: [] // 必填，需要使用的JS接口列表，所有JS接口列表见附录2
});
```
{% alert info %}
所有需要使用JS-SDK的页面必须先注入配置信息，否则将无法调用（同一个url仅需调用一次，对于变化url的SPA的web app可在每次url变化时进行调用,目前Android微信客户端不支持pushState的H5新特性，所以使用pushState来实现web app的页面会导致签名失败，此问题会在Android6.2中修复）。
{% endalert %}

#### 四、通过ready接口处理成功验证
```js
wx.ready(function(){
    // config信息验证后会执行ready方法，所有接口调用都必须在config接口获得结果之后，config是一个客户端的异步操作，所以如果需要在页面加载时就调用相关接口，则须把相关接口放在ready函数中调用来确保正确执行。对于用户触发时才调用的接口，则可以直接调用，不需要放在ready函数中。
});
```
#### 五、通过error接口处理失败验证
```js
wx.error(function(res){
    // config信息验证失败会执行error函数，如签名过期导致验证失败，具体错误信息可以打开config的debug模式查看，也可以在返回的res参数中查看，对于SPA可以在这里更新签名。
});
```
----------------------------------------
看了如上过程，下面用一个实例来演练一下：

#### 一、判断当前客户端版本是否支持指定js接口
```js
wx.checkJsApi({
    jsApiList: ['chooseImage'], // 需要检测的JS接口列表，所有JS接口列表见附录2,
    success: function(res) {
        // 以键值对的形式返回，可用的api值true，不可用为false
        // 如：{"checkResult":{"chooseImage":true},"errMsg":"checkJsApi:ok"}
    }
});
```
#### 二、代码呈现
```js
wx.ready(function() {
    wx.checkJsApi({
        jsApiList: ['onMenuShareTimeline','onMenuShareAppMessage','hideOptionMenu'], // 需要检测的JS接口列表，所有JS接口列表见附录2,
        success: function(res) {
            // 以键值对的形式返回，可用的api值true，不可用为false
            // 如：{"checkResult":{"chooseImage":true},"errMsg":"checkJsApi:ok"}
        }
    });
    //分享到朋友圈
    wx.onMenuShareTimeline({
        title: that.props.title, // 分享标题
        link: currentPath, // 分享链接
        imgUrl: that.props.imgUrl, // 分享图标
        success: function () {
            // 用户确认分享后执行的回调函数
            // alert('分享成功');
        },
        cancel: function () {
            // 用户取消分享后执行的回调函数
            // alert('取消分享！');
        }
    });

    //分享给朋友
    wx.onMenuShareAppMessage({
        title: that.props.title, // 分享标题
        desc: that.props.desc, // 分享描述
        link: currentPath,// 分享链接
        imgUrl: that.props.imgUrl, // 分享图标
        type: '', // 分享类型,music、video或link，不填默认为link
        dataUrl: '', // 如果type是music或video，则要提供数据链接，默认为空
        success: function () {
            // 用户确认分享后执行的回调函数
            // alert('分享成功');
        },
        cancel: function () {
            // 用户取消分享后执行的回调函数
            // alert('取消分享！');
        }
    });

    //处理失败验证
    wx.error(function(res) {
       // config信息验证失败会执行error函数，如签名过期导致验证失败，具体错误信息可以打开config的debug模式查看，也可以在返回的res参数中查看，对于SPA可以在这里更新签名。
        // alert(res.errMsg);
     });
});
```
{% alert success %}
为了避免不必要的异常问题，一般我们在wx.ready()方法中调用。
{% endalert %}
#### 三、分享相关接口
```js
onMenuShareTimeline
onMenuShareAppMessage
onMenuShareQQ
onMenuShareWeibo
onMenuShareQZone
```
①分享到朋友圈
②分享给朋友
③分享到qq
④分享到腾讯微博
⑤分享到qq空间


参考：
http://mp.weixin.qq.com/wiki/7/aaa137b55fb2e0456bf8dd9148dd613f.html#.E5.88.86.E4.BA.AB.E6.8E.A5.E5.8F.A3










