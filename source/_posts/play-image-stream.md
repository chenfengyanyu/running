---
title: 玩转图片流
date: 2018-01-19 23:22:22
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/blob0.png
thumbnailImagePosition: left
tags: 
- image
- blob
- base64
comments: false
metaAlignment: center
categories: 技术博文
---
做了一款[图片识别](https://github.com/chenfengyanyu/electron-ocr)软件，应用到了图片流，预览，Blob，Base64，以及类型转换等技术。这些技术在图片相关开发中必不可少，这一次，让我们玩转「图片流」吧！
<!-- more -->
#### 一、常见场景
我们先来看一下图片的使用场景：
- 通过 input 上传；
- 文件拖拽上传；
- 图片预览；
- 图片数据存储；
- 第三方接口需要；
- ...

{% alert success %}
当然，实际的应用远远比这些复杂，但是我们如果掌握了核心思想，那么无非就是「图片流」的获取和转换，下面让我们逐步深入吧！
{% endalert %}

#### 二、说说图片流
上面提到了「图片流」，其实就是用二进制流来表示图片，也就是我们常说的「二进制图片」。
{% alert info %}
计算机的存储在物理上是都二进制的，所以文本文件与二进制文件的区别并不是物理上的，而是逻辑上的。
{% endalert %}
可能有人要问了，为什么要用二进制来表示图片呢？我们来看看二进制的优势：
1. 二进制文件比较节约空间，在储存字符型数据时与普通存储并没有差别。但是在储存数字，特别是实型数字时，二进制更节省空间；
2. 内存中参加计算的数据都是用二进制无格式储存起来的，因此，使用二进制储存到文件就更快捷。如果储存为文本文件，则需要一个转换的过程。在数据量很大的时候，两者就会有明显的速度差别了；　
3. 涉及到一些比较精确的数据，使用二进制储存不会造成有效位的丢失；

看到这里，相信大家已经对图片流有了一定的认识。既然提到了存储，补充说明一下图片在后端存储方式：
- 其一：可以将图片以独立文件的形式存储在服务器的指定文件夹中，再将路径存入数据库字段中；
- 其二：将图片转换成二进制流，直接存储到数据库的 `Image` 类型字段中；

#### 三、FileReader 是什么？
`FileReader` 对象允许 `Web` 应用程序异步读取存储在用户计算机上的文件（或原始数据缓冲区）的内容，使用 `File` 或 `Blob` 对象指定要读取的文件或数据。

其中 `File` 对象可以是来自用户在一个 `<input>` 元素上选择文件后返回的 `FileList` 对象,也可以来自拖放操作生成的  `DataTransfer` 对象,还可以是来自在一个 `HTMLCanvasElement` 上执行 `mozGetAsFile()` 方法后返回结果。
{% alert warning %}
官话说了一大堆，其实你只需要知道「它是用来读取文件的」就够了。
{% endalert %}
参数说明如下：
- `FileReader.error`（只读）：一个 `DOMException`，表示在读取文件时发生的错误 ；
- `FileReader.readyState`（只读）：表示 `FileReader` 状态的数字。取值如下：
  - `EMPTY`	0	还没有加载任何数据.
  - `LOADING`	1	数据正在被加载.
  - `DONE`	2	已完成全部的读取请求.
- `FileReader.result`（只读）：文件的内容。该属性仅在读取操作完成后才有效，数据的格式取决于使用哪个方法来启动读取操作。

还有几个事件处理函数：
- `FileReader.onabort`：处理 `abort` 事件。该事件在读取操作被中断时触发。
- `FileReader.onerror`：处理 `error` 事件。该事件在读取操作发生错误时触发。
- `FileReader.onload`：处理 `load` 事件。该事件在读取操作完成时触发。
- `FileReader.onloadstart`：处理 `loadstart` 事件。该事件在读取操作开始时触发。
- `FileReader.onloadend`：处理 `loadend` 事件。该事件在读取操作结束时（要么成功，要么失败）触发。
- `FileReader.onprogress`：处理 `progress` 事件。该事件在读取 `Blob` 时触发。

可以用的方法如下：
- `FileReader.abort()`：中止读取操作。在返回时，`readyState` 属性为 `DONE`。
- `FileReader.readAsArrayBuffer()`：开始读取指定的 `Blob` 中的内容。一旦完成，`result` 属性将包含一个`ArrayBuffer` 来表示文件数据；
- `FileReader.readAsBinaryString()`：开始读取指定的 `Blob` 中的内容。一旦完成，`result` 属性中将包含所读取文件的原始二进制数据。
- `FileReader.readAsDataURL()`：开始读取指定的 `Blob` 中的内容。一旦完成，`result` 属性中将包含一个 `data: URL` 格式的字符串以表示所读取文件的内容。
- `FileReader.readAsText()`：开始读取指定的 `Blob` 中的内容。一旦完成，`result` 属性中将包含一个字符串以表示所读取的文件内容。

{% alert info %}
上面的文档描述的很清楚了，但是估计你也没逐条细看，没关系，我们还有示例🙈
{% endalert %}

具体的应用场景可能如下：
```js
onDrop(files) {
  files.forEach(file => {
    const reader = new FileReader();
    reader.onload = () => {
      console.log('Jartto files logs: ', file);
      const fileAsBinaryString = reader.result;
      // Todo...
    };
    reader.onabort = () => console.log('file reading was aborted');
    reader.onerror = () => console.log('file reading has failed');

    reader.readAsBinaryString(file);
  });
}
```
{% alert danger %}
用法很简单，你唯一需要注意的是一定要在 `onload` 之后再做操作！
{% endalert %}

#### 四、base64 格式
我们有时候会在网页中看到这样的代码：
```json
data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAb4AAABYCAYAAACK55LCAAAgAElEQVR4Xuy9aXNc2ZEleN4e+wIgsO8ACXAnk8mUUqoqldQ9Yz3W/an/wsxvnA/TM2NWizKVmczkTgIg9h0IxL6+fez4DbCo7JQqpZLZfGBARjEJBOK95/de9+PHj3toO//7f40x/BpaYGiBoQWGFhha4BOxgDYMfJ/ISg8fc2iBoQWGFhhaQCwwDHzDjTC0wNACQwsMLfBJWWAY
...
```
这就是 `base64` 格式的数据，代表着一张图片的数据，专业术语叫：`Data URI scheme`。
{% alert info %}
`Data URI scheme` 是在RFC2397中定义的，目的是将一些小的数据，直接嵌入到网页中，从而不用再从外部文件载入。
{% endalert %}

目前，`Data URI scheme` 支持的类型有：
```json
data:,文本数据
data:text/plain,文本数据
data:text/html,HTML 代码
data:text/html;base64,base64 编码的 HTML 代码
data:text/css,CSS代码
data:text/css;base64,base64 编码的 CSS 代码
data:text/javascript,Javascript 代码
data:text/javascript;base64,base64 编码的 Javascript代码
data:image/gif;base64,base64 编码的 gif 图片数据
data:image/png;base64,base64 编码的 png 图片数据
data:image/jpeg;base64,base64 编码的 jpeg 图片数据
data:image/x-icon;base64,base64 编码的 icon 图片数据
```
`Base64` 编码之所以称为 `Base64`，是因为其使用 64 个字符来对任意数据进行编码。`Base64` 编码本质上是一种将二进制数据转成文本数据的方案。对于非二进制数据，是先将其转换成二进制形式，然后每连续 6 比特（ 2 的 6 次方= 64 ）计算其十进制值，根据该值在上面的索引表中找到对应的字符，最终得到一个文本字符串。

更深入的了解可以参考：[Base64 编码原理与应用](http://blog.xiayf.cn/2016/01/24/base64-encoding/)

#### 五、Blob 格式
{% alert success %}
Blob (binary large object)，二进制大对象，是一个可以存储二进制文件的容器。
{% endalert %}

`Blob` 是一个大文件，典型的 `Blob` 是一张图片或一个声音文件，由于它们的尺寸，必须使用特殊的方式来处理（例如：上传、下载或者存放到一个数据库）。

这是我转换的一个 `Blob` 对象：
```
Blob {
  name: "图片示例：jartto.png",
  preview: "blob:file:///f3823a2a-2908-44cb-81e2-c19d98abc5d1",
  size: 47396,
  type: "image/png",
}
```

{% alert success %}
请注意，这里的 `preview` 属性是可以拿出来做图片预览的。
{% endalert %}

#### 六、base64 转 Blob
当我们拿到一段用 `base64` 表示的图片代码，你觉得不够优雅，或者其他各种原因，你可能需要转化成 `Blob` 格式，代码如下：
```js
base64ToBlob(b64data, contentType, sliceSize) {
  sliceSize || (sliceSize = 512);
  return new Promise((resolve, reject) => {
    // 使用 atob() 方法将数据解码
    let byteCharacters = atob(b64data);
    let byteArrays = [];

      for (let offset = 0; offset < byteCharacters.length; offset += sliceSize) {
        let slice = byteCharacters.slice(offset, offset + sliceSize);

        let byteNumbers = [];
        for (let i = 0; i < slice.length; i++) {
            byteNumbers.push(slice.charCodeAt(i));
        }
        // 8 位无符号整数值的类型化数组。内容将初始化为 0。
        // 如果无法分配请求数目的字节，则将引发异常。
        byteArrays.push(new Uint8Array(byteNumbers));
      }
      resolve(new Blob(byteArrays, {
        type: contentType
      }))
  })
}
```
调用方式：
```js
// dataUri 是你的 base64 图片流
let blob = await this.base64ToBlob(dataUri.split(',')[1], 'image/png');
```
经过处理，我们拿到了一个 `Blob` 对象，但可能是这样：
```js
Blob {
  size: 47396,
  type: "image/png",
}
```
就这个，那我想预览的计划不就泡汤了？不要着急，我们来补充一下：
```js
Object.assign(blob,{
  // jartto: 这里一定要处理一下
  preview: URL.createObjectURL(blob),
  name: `图片示例：${index}.png`
});
```


{% alert info %}
你可以使用 `window.btoa()` 方法来编码一个可能在传输过程中出现问题的数据，并且在接受数据之后，使用 `atob()` 方法再将数据解码。
{% endalert %}

关于 `atob` 和 `Uint8Array` 这里就不细说了，大家可以看看文档：
- [window.atob](https://developer.mozilla.org/zh-CN/docs/Web/API/WindowBase64/atob)
- [Uint8Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)

#### 七、Blob 转 base64
这又是另一种场景了，很可能出现在你使用第三方插件，如图片拖拽上传插件后，自动返回给你了 `Blob` 对象，但不幸的是，你发现你又用了一个第三方的服务接口只接收 `base64` 格式的数据，是否有点欲哭无泪。

不要怕，我们也有办法，如下：
```js
blobToBase64(blob) {
  return new Promise((resolve, reject) => {
    const fileReader = new FileReader();
    fileReader.onload = (e) => {
      resolve(e.target.result);
    };
    fileReader.readAsDataURL(blob);
    fileReader.onerror = () => {
      reject(new Error('文件流异常'));
    };
  });
}
```
调用方式：
```js
const allBase64 = await this.blobToBase64(blobObj);
```
{% alert info %}
补充一句，上面所说的场景是我真实遇到过的，生活真是处处充满惊喜。
{% endalert %}

#### 八、URL 转 base64
好了，更有意思的事情来了，我提高了要求，只给你一个图片的 URL（本地或者在线地址），是否能将图片编码成流？答案是肯定的，这里我们得请出 `canvas` 了：
{% alert success %}
思路很简单，根据图片对象的数据去画 canvas，再利用它来等价表示图片流。
{% endalert %}
```js
getDataUri(url) {
  return new Promise((resolve, reject) => {
    /* eslint-disable */
    let image = new Image();
    image.onload = function() {
      let canvas = document.createElement('canvas');
      canvas.width = this.naturalWidth;
      canvas.height = this.naturalHeight;
      canvas.getContext('2d').drawImage(this, 0, 0);
      // Data URI
      resolve(canvas.toDataURL('image/png'));
    };

    image.src = url;
    // console.log(image.src);

    image.onerror = () => {
      reject(new Error('图片流异常'));
    };
  });
}
```
你可以像这样调用：
```js
let dataUri = await this.getDataUri(`image/test/jartto.png`);
```

#### 九、常见问题与补充说明
1.`Failed to execute 'readAsDataURL' on 'FileReader': parameter 1 is not of type 'Blob'.`
出现如上问题，请检查你的 Blob 对象格式是否正确，是否缺少必要项。

2.很多第三方应用都需要去除 `base64` 头信息：
```js
base64.replace(/^data:image\/(jpeg|png|gif);base64,/,'')
```

3.`FileReader` 读取文件后，记得放在 `onload` 函数内，切记。

4.在使用 `canvas.getContext('2d').drawImage(this, 0, 0);` 过程中，一定要注意这里的 `this` 指向，否则可能发生异常。

5.`base64` 转 `Blob`，请记得：
```js
Object.assign(blob,{
  // jartto: 这里一定要处理一下
  preview: URL.createObjectURL(blob),
  name: `图片示例：${index}.png`
});
```

#### 十、总结
一个图片流转来转去的，是否有点晕头转向？相信我，如果你认真的看完了文章，那么以后你对图片数据的处理就会顺畅很多。

一直喜欢这样来写文章：在项目之余，把问题纪录下来，然后逐条搞懂。知识就像是树枝一样，你不断的学习、反思，它不断的蔓延、分化，久而久之将会形成一张网。

所以，努力学习吧，让自己长成参天大树，生机勃勃，遮天蔽日。

#### 十一、参考资源
[Blob](https://baike.baidu.com/item/blob/543419?fr=aladdin)
[MDN FileReader](https://developer.mozilla.org/zh-CN/docs/Web/API/FileReader)
[二进制图片](https://baike.baidu.com/item/二进制图片/22031326?fr=aladdin)
[Convert Image to Data URI](https://davidwalsh.name/convert-image-data-uri-javascript)
[html base64 img 图片显示](http://blog.csdn.net/lgh1117/article/details/7740136)
