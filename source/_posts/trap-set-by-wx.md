---
title: 微信支付大坑
date: 2016-07-29 17:13:03
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/wx_F1264859-71E0-46BB-9384-419885FE56E2.png
thumbnailImagePosition: left
tags: 
- 微信
comments: true
metaAlignment: center
categories: 技术博文
---
搞过微信开发的同学肯定对微信支付不会陌生，想必也碰到了不少麻烦吧？到底有多坑，我们来说道说道。咳～咳，没有批评，存粹只是对知识的敬畏哦🙈
<!-- more -->
本文来细数一下微信支付的大坑，希望可以对后来的你有所帮助。
#### 一、方式
两种方式JS SDK和JS API，这两种方式都是可用
JS SDK方式
http://mp.weixin.qq.com/wiki/7/aaa137b55fb2e0456bf8dd9148dd613f.html
JS API方式
https://pay.weixin.qq.com/wiki/doc/api/jsapi.php?chapter=7_7

#### 二、关于异常fail_invalid_appid
检查商户平台证书，需要手动设置key
检查支付地址和支付测试目录问题
添加测试人微信号到白名单

目录最好只到2级，太深的话，也是会报错的
单独链接，单页应用＃后无法识别，亦会报错

http://blog.sina.com.cn/s/blog_5fc3da040102vjm7.html

#### 三、支付验证签名失败
get_brand_wcpay_request:fail
步骤一：
检测参数大小写和顺序问题
商户key必须拼写在字符串末尾
MD5算法后需转为大写

https://pay.weixin.qq.com/wiki/doc/api/jsapi.php?chapter=4_3

http://jingyan.baidu.com/article/59703552c3c9808fc1074072.html?st=2&os=0&bd_page_type=1&net_type=2

用在线签名验证工具去对比签名
https://pay.weixin.qq.com/wiki/tools/signverify/

步骤二：
{% image fancybox left clear group:travel http://7xvi3w.com1.z0.glb.clouddn.com/wx_97CF93DF-B21E-4247-B9C0-E1E0AF283E6D.png 200px %}
paySign需要上图红圈中的5个参数进行签名，否则就会报错。

#### 四、调用支付JSAPI缺少参数：$key0$
首先确定timeStamp必须是string类型；
其次检查参数名对应情况，必须与api参数列表完全一致；

#### 五、相关补充
http://bbs.csdn.net/topics/390981338
http://www.cnblogs.com/deepleo/p/weixinjssdk.html

#### 六、相关文档：
网页调起支付API
https://pay.weixin.qq.com/wiki/doc/api/jsapi.php?chapter=7_7
安全规范（签名规则等）
https://pay.weixin.qq.com/wiki/doc/api/jsapi.php?chapter=4_3
调用请求参数
https://pay.weixin.qq.com/wiki/tools/signverify/
js sdk
http://mp.weixin.qq.com/wiki/7/aaa137b55fb2e0456bf8dd9148dd613f.html
网页端掉起支付api
https://pay.weixin.qq.com/wiki/doc/api/jsapi.php?chapter=7_7
统一下单：
https://pay.weixin.qq.com/wiki/doc/api/jsapi.php?chapter=9_1
微信公众平台：
https://mp.weixin.qq.com/



