---
title: HTML5 之地理定位（Geolocation）
date: 2018-11-16 08:55:31
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/geolocation/logo.png
thumbnailImagePosition: left
tags: 
- chrome
- extensions
comments: false
metaAlignment: center
categories: 技术博文 
---
很久前研究过 HTML5 Geolocation，最近整理 [F2E-Awesome](https://github.com/f2e-awesome/knowledge) 的时候，发现缺少相关的文章，所以按照自己的使用经验整理了此篇文章。
<!-- more -->
#### 一、应用场景：
附近商家定位；计算行走路程；`GPS` 导航；

请求一个位置信息，如果用户同意，浏览器就会返回位置信息，该位置信息是通过支持 `HTML5` 地理定位功能的底层设备提供给浏览器。位置信息由纬度／经度坐标和一些其他的元数据组成。有了这些位置信息就可以构建引人注意目的位置感知类应用程序。

#### 二、简单示例
```js
window.addEventListener('load',loadDemo,true);

function loadDemo(){
    if(navigator.geolocation){
        navigator.geolocation.watchPosition(
            updateLocation,
            handleLocationError,
            {maximumAge:20000}
        );
    }
}

function updateLocation(position){
    var latitude = position.coords.latitude;
    var longitude = position.coords.longitude;
    var accuracy = position.coords.accuracy;
    var timestamp = position.timestamp;
}
```

#### 三、兼容性
![geolocation](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/geolocation/canuse.png)

#### 四、相关 API
[MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Geolocation/Using_geolocation)

先打印：

{% alert info %}
两种类型的定位请求API：单次定位请求和重复性的位置更新请求；
{% endalert %}


三个方法：
1.`getCurrentPosition`
获取用户当前定位位置，这会异步地请求获取用户位置，并查询定位硬件来获取最新信息。
```js
if(navigator.geolocation){
    navigator.geolocation.watchPosition(
    updateLocation,
    handleLocationError,
    {maximumAge:20000}
    );
}
```

需要注意的是：有 `GPS` 的设备可能需要一分钟或更久来获取 `GPS` 定位，在这种情况下 `getCurrentPosition()` 会返回低精度数据（基于 `IP` 的定位或 `Wi-Fi` 定位）。

2.`watchPosition`
您可以设定一个回调函数来响应定位数据发生的变更（设备发生了移动，或获取到了更高精度的地理位置信息），它与 `getCurrentPosition()` 接受相同的参数，但回调函数会被调用多次。错误回调函数与 `getCurrentPosition()` 中一样是可选的，也会被多次调用。
```js
var watchID = navigator.geolocation.watchPosition(function(position) {
  do_something(position.coords.latitude, position.coords.longitude);
});
```

3.`clearWatch`
`watchPosition()` 函数会返回一个 `ID`，唯一地标记该位置监视器。您可以将这个 `ID` 传给 `clearWatch()` 函数来停止监视用户位置。
```js
navigator.geolocation.clearWatch(watchID);
```

#### 五、警告
```
[Deprecation] getCurrentPosition() and watchPosition() no longer work on insecure origins. To use this feature, you should consider switching your application to a secure origin, such as HTTPS. See https://goo.gl/rStTGz for more details.
```

在 `IOS 10` 中，苹果对 `webkit` 定位权限进行了修改，所有定位请求的页面必须是 `https` 协议的。如果是非 `https` 网页，在 `http` 协议下通过 `html5` 原生定位接口会返回错误，也就是无法正常定位到用户的具体位置，而已经支持 `https` 的网站则不会受影响。



{% alert warning %}
经测试，本地 `file` 文件或者是启动服务器，都会直接返回拒绝服务。
{% endalert %}

#### 六、定位原理
{% alert info %}
那么浏览器是如何实现定位的呢，原理又是如何？
{% endalert %}

1.`IP` 定位
每一台连上网路的电脑都具有一个独一无二的 `IP` 位址，经由 `IP` 位址可以反查出电脑位置，但是结果并不精确，而且很容易到受到 `ISP` 的 `IP` 分配机制影响，而造成误差。

2.`GPS` 定位
`GPS` 是我们最常听到的定位技术，它是利用卫星来定位，可以很精确的侦测到使用者位置。但是要玩 `GPS`，行动装置一定要有 `GPS` 模组，而且尽管 `GPS` 非常精准，还是会有技术上的局限，比如在侦测位置时，`GPS` 的感应器要位于视线范围内（line of sight），因此在有很多高楼大厦、或者是障碍物的地方，`GPS` 反而没有办法运作自如。

3.`Wi-Fi` 三角测量定位
这个定位技术可以说是为都会区而设的，因为热闹的大都市通常会有不少 `Wi-Fi` 热点，定位供应商可以根据这些热点的相对位置、距离等建立一个资料库。`Wi-Fi` 三角测量适用于 `GPS` 无法渗透的地方，不过缺点是需要经常更新资料库。

4.基地台三角测量定位
借助基地台之间的相对位置，可以定位出用户在哪里，只要你的手机收得到讯号，就能定位。 一般来说，这四种技术的准确性由高而低分别是：`GPS` 定位、`Wi-Fi` 三角测量、基地台三角测量、`IP` 定位。但还是要视环境而定，比如你到没有 `Wi-Fi` 热点的地方、或是手机信号不好，定位效果当然也会受影响。

[为什么浏览器知道你在哪里](http://www.iefans.net/liulanqi-zhidao-weizhi/)
