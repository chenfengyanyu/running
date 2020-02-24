---
title: 极简弹幕方案
date: 2020-02-16 23:58:47
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/barrager/logo.png
thumbnailImagePosition: left
tags: 
- web
- ppt
- barrager
comments: false
metaAlignment: center
categories: 技术博文 
---
重大的活动现场一般离不开 `PPT` 演示，可是如何有效和现场互动呢？这时候弹幕必不可少，静态的 `PPT` 就略显乏力。有没有一种好的方案可以二者兼得呢？
<!-- more -->
{% alert info %}
如何才能使 `PPT` 具有交互性，这是一个值得思考的问题！
{% endalert %}

可能很多童鞋想到了，如果使用「网页 `PPT`」 ，岂不是完美解决了这个问题。本节我们就来提供一种思路，用「`PPT` + 发射器 + `Socket`」 实现「极简弹幕方案」。

**关于「网页 `PPT`」，可以查看我之前的文章「[酷炫的 `HTML5` 网页 `PPT`](http://jartto.wang/2019/04/15/amusing-ppt/)」一探究竟。**


#### 一、效果演示
我们先通过一个简单的视屏演示一下效果：

<video src="https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/barrager/demo.mp4" controls="controls" width="100%" height="">您的浏览器不支持播放该视频！</video>

相关代码：[Demo 地址](#)


#### 二、方案概括
{% alert info %}
看完上面的演示，是不是迫不及待想知道答案，下面我们来逐步拆分。
{% endalert %}
先来看看代码结构
```bash
.
├── README.md
├── mobile
│   ├── README.md
│   ├── node_modules
│   ├── package.json
│   ├── public
│   ├── src
│   └── yarn.lock
├── package.json
├── ppt
│   ├── css
│   ├── extras
│   ├── images
│   ├── index.html
│   ├── js
│   └── temp
├── server
│   ├── app.js
│   ├── data
│   ├── node_modules
│   ├── package-lock.json
│   └── package.json
└── yarn.lock
```
{% alert success %}
我们主要关注以下三个目录：
{% endalert %}


1.`ppt`
使用 `impressjs` 构建的项目，`PPT` 演讲「主屏」，主要演示内容区域，同时接收「服务端」推送弹幕信息。

2.`mobile`
移动端，下文称作「发射器」，主要用作现场用户互动向主屏发送弹幕消息。通过 `Create React App` 生成，技术栈是：`React + Antd`。

3.`server`
服务端，主要接受用户弹幕，同时广报到主屏，使用 `Socket` 实现。


{% alert success %}
启动方式：
{% endalert %}
1.进入 server 目录，启动服务：
```
node app.js
```
此时会启动一个本机 IP 地址的服务。

2.进入 ppt 目录，使用 http-server 启动站点：
```
http-server
```
注意：接口地址需要替换成本机 IP 地址。

3.进入 mobile 目录，启动发射器：
```
yarn start
```
注意：请求接口需要使用本机 IP 地址。

[Demo](https://github.com/chenfengyanyu/barrager-ppt-demo) 比较简单，主要展示主流程，如果细节过程有问题，欢迎一起探讨。

#### 三、主屏细节（核心代码）
主屏是主要演示版面，我们需要像下面这样作出 `PPT`，这里我们做了三个页面：
```html
<div id="impress" class="jartto" data-transition-duration="1000">

    <div id="cover" class="step slide title" data-x="1000" data-y="1000">
        <img src="temp/img/qrcode.png" />
    </div>

    <div id="award" class="step slide" data-x="2000" data-y="3000">
        <h1>请开始你的表演～</h1>
    </div>

    <div id="change" class="step slide" data-x="2000" data-y="3000" data-scale="5">
        <h1>切换 PPT</h1>
    </div>

    <div id="thank" class="step slide"  data-rel-x="0" data-rel-y="3000" data-rotate="90" data-scale="2">
        <img src="images/thanks.png" />
    </div>

</div>
```
{% alert warning %}
每个 div 就是一页 ppt，里面可以随意排版，data-x 控制位置，data-scale 控制缩放，data-rotate 控制旋转。
{% endalert %}

更多 `API` 文档，请参考如下文档：
1.[酷炫的 `HTML5` 网页 `PPT`](http://jartto.wang/2019/04/15/amusing-ppt/)
2.[文档地址](https://github.com/impress/impress.js)


#### 四、实现弹幕
为了更好的理解弹幕，我们来实现一个简版：
1.定义弹幕结构
```html
<div class="jartto_demo">我是弹幕</div>
```
2.定义移动动画
```css
@keyframes barrager{
   from{
     left:100%;
     transform:translateX(0);
     transform:translate3d(0, 0, 0);
   }
   to{
     left:0;
     transform:translate3d(-100%, 0, 0);
   }
}
```
{% alert danger %}
注意，使用 translate3d 可以开启 GPU 硬件加速，会比 translateX 更流畅一些。
{% endalert %}

关于硬件加速，可以关注我之前写的一篇文章：[详谈层合成（composite）](http://jartto.wang/2017/09/29/expand-on-performance-composite/)

3.使用动画
```css
.jartto_demo{
  position:absolute;
  animation: barrager 5s linear 0s;
}
```
OK，我们通过三步实现了一个简单的弹幕动画。那么问题来了，弹幕都是随机位置，随机速度，随机颜色出现在屏幕上的，这个该如何实现呢？

4.随机弹幕出现位置
```js
let window_height = $(window).height() - 150;
bottom = Math.floor(Math.random() * window_height + 40);
code = code.replace("   bottom:{bottom}, //距离底部高度,单位px,默认随机 \n", '');
```
5.随机弹幕颜色
```js
let color = `#${Math.floor(Math.random() * (2 << 23)).toString(16)}`; 
console.log(color); // #6e8360
```

{% alert info %}
好了，大功告成，我们顺手加上 Socket 事件监听。
{% endalert %}

6.事件监听
为了拿到用户发送过来的弹幕，我们需要做一个事件监听（接收服务端数据）：
首先，引入 `socket.io.js` 文件：
```html
<script type="text/javascript" src="http://{jartto.ip}/socket.io/socket.io.js"></script>
```

```js
const socket = io('http://{jartto.ip}');
socket.on('server-push', function (data) {
    console.log('news message >>>>>>>>', data.message);
    run(data.message); 
});
```
当我们监听到 `server-push` 事件的时候，`run` 函数就会初始化弹幕方法，随机生成一条弹幕，在屏幕滑过。

#### 五、发射器细节（核心代码）
发射器就非常简单了，我们使用 `Create React App` 初始化项目，在 `src/app.js` 中写入一个表单(这里以 `React` 为例，`Vue` 也是大同小异）：
```js
<div className="app-box">
    <div className="form-box">
        <Form.Item {...formItemLayout} label="">
        {getFieldDecorator('msg', {
            rules: [
            {
                required: true,
                message: '请输入内容',
            },
            ],
        })(<Input size="large" placeholder="发送消息，嗨起来～" />)}
        </Form.Item>
        <Form.Item {...formTailLayout} >
        <Button className="btns"  shape="round" icon="close" size="large" onClick={this.cancle}>取消</Button>
        <Button   type="primary" shape="round" icon="check" size="large" onClick={this.check}>发送</Button>
        </Form.Item>
    </div>
</div>
```
用户在输入框输入消息，向我们的服务器发送请求，很简单，就不赘述了。效果图可以参考下面：
![Demo 效果图](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/barrager/show.png)

{% alert warning %}
请注意，此处为了演示效果，我将三端同框了。
{% endalert %}

#### 六、服务端细节（核心代码）
服务端比较简单，使用 `Express` 初始化一个 `Node` 项目，向 `app.js` 写入如下内容：
1.启动 `Socket` 服务：
```js
const express = require('express'),
  bodyParser = require('body-parser'),
  socket = require('socket.io'),
  fs = require('fs'); 

const app = express();
const PORT = 4000;
const io = socket(app.listen(PORT, () => console.log(`start on port ${PORT}`)));
```

2.监听 `Socket` 连接，接收用户发送数据，将数据写入本地 `JSON` 文件，并广播到 `server-push` 事件：
```js
io.on('connection', sockets => {
  console.log('连接成功！');
  app.post('/api/send', (req, res, next) => {
    // console.log(req.body);
    let info = JSON.stringify(req.body.msg);

    fs.writeFile('./data/jartto.json', `${info},\n`,
    {flag:'a',encoding:'utf-8',mode:'0666'},function(err){
      if(err) {
        console.log('文件写入失败');
        res.status(500).send('Error');
      } else {
        sockets.broadcast.emit('server-push', { message: req.body.msg });
        res.status(200).send('Done');
      }  
     }) 

  })
  
  sockets.on('disconnect', () => {
    console.log('User Disconnected');
  })
});

```
3.当然，我们也可以存入数据库做持久化，以下演示存入 `MySQL` 核心代码：
```js
io.on('connection', sockets => {
  console.log('连接成功！');
  app.post('/api/send', (req, res, next) => {
    let {ua, msg} = req.body.msg;
    req.getConnection(function(err, cnt) {

      let query = cnt.add('INSERT INTO (ua, msg)', {ua, msg}, function(err, rows) {
        if (err) {
          console.log("Error inserting : %s ",err );
          return next(err);
        }

        sockets.broadcast.emit('server-push', { message: req.body.msg });
        res.status(200).send('Done');
      })
    })

  })

  sockets.on('disconnect', () => {
    console.log('User Disconnected');
  })
});
```
4.启动服务
```
node app.js
```

我们的服务端就启动起来了，访问地址是你的主机 `IP` 和 `4000` 端口。

#### 七、总结
[本文](http://jartto.wang/2020/02/16/web-barrager/)我们从零到一搭建了一个完整的弹幕方案，涉及到三部分：主屏，发射器和服务端，旨在为小伙伴们提供一套极简的设计思路。通过 `Demo` 我们可以简单的串联一个全栈项目，做更多有趣的事情。

文章最后，打个小广告吧。如果你想搭上在线教育的快车，快速成长，不妨加入我们。一起成长，一起学习，一起挑战更多有趣的事情，「跟谁学-高途课堂」欢迎你，请将简历私我～



