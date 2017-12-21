---
title: XHR or Fetch API ?
date: 2017-01-17 15:33:24
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/api_fetch.jpg
thumbnailImagePosition: left
tags: 
- js
- ajax
- fetch
comments: true
metaAlignment: center
categories: 技术博文
---
早已习惯Ajax的你，是否完全忽视了Fetch的入侵？究竟Fetch好在哪里了呢，我们来说道说道。
<!-- more -->
#### 一、先谈XHR
XHR即XMLHttpRequest，通常来说，我们这样来使用XHR：
```js
if (window.XMLHttpRequest) { // Mozilla, Safari, ...
  request = new XMLHttpRequest();
} else if (window.ActiveXObject) { // IE
  try {
    request = new ActiveXObject('Msxml2.XMLHTTP');
  } 
  catch (e) {
    try {
      request = new ActiveXObject('Microsoft.XMLHTTP');
    } 
    catch (e) {}
  }
}
request.open('GET', 'http://jartto.wang/ajax-example', true);
request.send(null);
```
这里只是一个XHR的简单示例，就不赘述了。

{% alert success %}
常用的js框架已经帮我们封装好了，XHR使用起来会更加简单。
{% endalert %}

当然，XHR已经能满足我们的日常开发需求了。但是我们要做的更好，不妨试试新的支持：
```js
window.fetch
```

#### 二、为什么是Fetch
XMLHttpRequest 是一个设计粗糙的 API，不符合关注分离（Separation of Concerns）的原则，配置和调用方式非常混乱，而且基于事件的异步模型写起来也没有现代的 Promise，generator/yield，async/await 友好。

Fetch 的出现就是为了解决 XHR 的问题。同时简洁的语法，基于标准 Promise 的实现，支持 async/await等特性更是如虎添翼，废话不多说，上示例。

#### 三、基本示例
```js
fetch('http://jartto.wang/url', {
	method: 'get'
}).then(function(response) {
	
}).catch(function(err) {
	// Error
});
```
看出来了吧，其实Fetch使用Promise来处理结果和回调，then相当于resolve，而catch充当了then的第二个参数（reject）。我们接着来看参数说明：
```js
url (required), options (optional)
```
依然很简单，下文中会有详细说明。

{% alert info %}
如果你还不懂promise，请看这里[Promise 详解](https://davidwalsh.name/promises)。
{% endalert %}

#### 四、先说Headers
自定义的请求头会让Fetch使用起来更加灵活，我们通过new Headers()来创建：
```js
// Create an empty Headers instance
var headers = new Headers();

// Add a few headers
headers.append('Content-Type', 'text/plain');
headers.append('X-My-Custom-Header', 'CustomValue');

// Check, get, and set header values
headers.has('Content-Type'); // true
headers.get('Content-Type'); // "text/plain"
headers.set('Content-Type', 'application/json');

// Delete a header
headers.delete('X-My-Custom-Header');

// Add initial values
var headers = new Headers({
	'Content-Type': 'text/plain',
	'X-My-Custom-Header': 'CustomValue'
});
```
如果需要修改请求头，我们先得创建一个Request实例，如下：
```js
var request = new Request('http://jartto.wang/test.json', {
	method: 'POST', 
	mode: 'cors', 
	redirect: 'follow',
	headers: new Headers({
		'Content-Type': 'text/plain'
	})
});
fetch(request).then(function() { /* handle response */ });
```
到这里，请求头也设置完了，那么我们来看看Request和Response吧。

#### 五、再说Request和Response
Request也就是一次Fetch调用的请求信息，其实上面已经提到了，这里我们采用简写方式：
```js
fetch('http://jartto.wang/test.json', {
	method: 'POST', 
	mode: 'cors', 
	redirect: 'follow',
	headers: new Headers({
		'Content-Type': 'text/plain'
	})
}).then(function() { /* handle response */ });
```
Request 参数说明：
```js
- method - GET, POST, PUT, DELETE, HEAD                   
- url - 请求的url                          
- headers - 对应的Header对象          
- referrer - 请求的referrer信息           
- mode - 模式，可选类型有cors, no-cors, same-origin             
- credentials - 是否携带cookie，可选类型有omit, same-origin                
- redirect - follow, error, manual            
- integrity - subresource integrity value             
- cache - 缓存模式，可选类型有default, reload, no-cache             
```

相比之下，Response就稍微简单了，Fetch的then方法接收了一个Response实例。当然，你也可以自己去创建Response对象，然后去修改它。
```js
// new Response(BODY, OPTIONS)
var response = new Response('.....', {
	ok: false,
	status: 404,
	url: '/'
});

// The fetch's `then` gets a Response instance back
fetch('http://jartto.wang/')
	.then(function(responseObj) {
		console.log('status: ', responseObj.status);
	});
```

Response 参数说明：
```js
- type - 类型，支持basic, cors
- url
- useFinalURL - 是否是最终的url，Boolean类型
- status - 状态码 (200, 404,等)
- ok - Boolean值，代表成功响应（status值在200-299之间）
- statusText - 状态值（例如：OK）
- headers - 与响应相关联的Headers对象
```

此外，Response还提供了如下的方法：
```js
- clone() - 创建一个新的Response克隆对象
- error() - 返回一个新的，与网络错误相关的Response对象
- redirect() - 重定向，使用新的URL创建新的Response对象
- arrayBuffer() - 返回一个promise,resolves是一个ArrayBuffer
- blob() - 返回一个promise,resolves是一个Blob
- formData() - 返回一个promise, resolves是一个FormData对象
- json() - 返回一个promise,resolves是一个JSON对象
- text() - 返回一个promise,resolves是一个USVString(text)
```

#### 六、更多的场景
说的天花乱坠，还不如来点实际的。下面通过一些常用示例来做说明：
1.JSON格式
```js
fetch('http://jartto.wang/test.json')
.then(function(response) { 
	// Convert to JSON
	return response.json();
})
.then(function(result) {
	console.log(result); 
});
```
这里两个then需要特殊说明一下：
{% alert info %}
response.json()转化成了一个Promise对象，格式是字符串类型，第二个then才是js对象。其实等价于：
{% endalert %}
```js
//return response.json()
JSON.stringify()
//then
JSON.parse(result)
```

2.返回HTML/text
```js
fetch('/next/page')
  .then(function(response) {
    return response.text();
  }).then(function(text) { 
  	// <!DOCTYPE ....
  	console.log(text); 
  });
```

3.发送form表单数据
```js
//form data
var form = document.querySelector('form');
fetch('http://jartto.wang/submit', {
	method: 'post',
	body: new FormData(form)
});
//JSON
fetch('http://jartto.wang/submit-json', {
	method: 'post',
	body: JSON.stringify({
		name: 'jartto',
		blog: 'http://jartto.wang'
	})
});
```

4.图片的处理
图片需要响应流的处理方式，略微不同，如下：
```js
fetch('http://jartto.wang/logo.png')
	.then(function(response) {
	  return response.blob();
	})
	.then(function(imageBlob) {
	  document.querySelector('img').src = URL.createObjectURL(imageBlob);
	});
```

5.上传示例
```js
var input = document.querySelector('input[type="file"]')

var data = new FormData()
data.append('file', input.files[0])
data.append('name', 'jartto')

fetch('http://jartto.wang/logo', {
  method: 'POST',
  body: data
})
```

6.处理HTTP错误状态码
```js
function checkStatus(response) {
  if (response.status >= 200 && response.status < 300) {
    return response
  } else {
    var error = new Error(response.statusText)
    error.response = response
    throw error
  }
}

function parseJSON(response) {
  return response.json()
}

fetch('/users')
.then(checkStatus)
.then(parseJSON)
.then(function(data) {
  console.log('request succeeded with JSON response', data)
}).catch(function(error) {
  console.log('request failed', error)
})
```

7.cookie配置（默认不带cookie，需要配置）
```js
//同域
fetch('http://jartto.wang/users', {
  credentials: 'same-origin'
})
//跨域
fetch('https://baidu.com/users', {
  credentials: 'include'
})
```

#### 七、美中不足
1.不可以取消    
Ajax调用XMLHttpRequest对象上的abort方法，但是Fetch好像没啥办法。

2.浏览器支持  
浏览器支持不是很好，但是我们可以用[window.fetch polyfill](https://github.com/github/fetch)来处理兼容问题。浏览器支持情况大致如下：
```
- Chrome       
- Firefox                
- Safari 6.1+             
- Internet Explorer 10+ 
```   
当然，还有一些细节问题，请看这里[传统 Ajax 已死，Fetch 永生](https://segmentfault.com/a/1190000003810652)

#### 八、总结
可以看到fetch目前阶段还有一些兼容性问题，但是这并不能阻挡我们的热情。毕竟一个新技术的出现总是会逐步取代老的技术。快来占领制高点，放心大胆的去尝鲜吧！

#### 九、参考文章
1.[Introduction to the Fetch API](https://www.sitepoint.com/introduction-to-the-fetch-api/)
2.[Fetch API](https://davidwalsh.name/fetch)
3.[Ain't that fetch!](http://webreflection.blogspot.jp/2015/03/aint-that-fetch.html)
4.[That's so fetch!](https://jakearchibald.com/2015/thats-so-fetch/)
5.[Mozilla Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)
6.[window.fetch polyfill](https://github.com/github/fetch)
7.[新一代Ajax API --fetch](http://www.cnblogs.com/hsprout/p/5504053.html)
8.[Ajax,Fetch](https://segmentfault.com/a/1190000003810652)



