---
title: 强大的富文本编辑器 CKEditor
date: 2018-05-13 22:56:40
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/ckeditor.png
thumbnailImagePosition: left
tags: 
- 富文本
- ckeditor
comments: false
metaAlignment: center
categories: 技术博文
---
提到富文本编辑器，可能前端童鞋并不陌生。今天我要推荐的富文本编辑器就是 CKEditor ，趁着最近项目的使用热劲，整理一些实用笔记，以备不时之需。
<!-- more -->
#### 一、快速构建
按照官网指南，我们可以迅速的[下载](https://ckeditor.com/ckeditor-4/download/)到适合项目的文件包，大致分为四种：
- 基础包
- 标准包
- 完整包
- 定制包

#### 二、简单使用
当你下载好项目文件后，使用非常简单：
- 包含类库文件
- 创建 textarea
- 使用 CKEDITOR.replace() 替换存在的 textarea

参考代码如下：
```js
<html>
    <head>
        <meta charset="utf-8">
        <title>A Simple Page with CKEditor</title>
        <!-- Make sure the path to CKEditor is correct. -->
        <script src="../ckeditor.js"/>
    </meta></head>
    <body>
        <form>
            <textarea name="editor1" id="editor1" rows="10" cols="80">
                This is my textarea to be replaced with CKEditor.
            </textarea>
            <script>
                // Replace the <textarea id="editor1"> with a CKEditor
                // instance, using default configuration.
                CKEDITOR.replace( 'editor1' );
            </script>
        </form>
    </body>
</html>
```

#### 三、关于插件
- [CKEditor Plugin](https://docs.ckeditor.com/ckeditor4/latest/guide/plugin_sdk_sample_1.html)
- [CKEditor Widget](https://docs.ckeditor.com/ckeditor4/latest/guide/widget_sdk_tutorial_1.html)

#### 四、CKEditor 获取 DOM 数据
```js
// 获取数据：
let operateState = editor.getData();

// 设置数据
editor.setData(operateState);
```

#### 五、CKEditor 去除粘贴样式
```js
// All content will be pasted as plain text.

config.forcePasteAsPlainText = true;

// Only Microsoft Word content formatting will be preserved.

config.forcePasteAsPlainText = 'allow-word’;
```

#### 六、CKEditor 监听 Undo 和 Redo 事件
```js
editor.on('beforeCommandExec', function(e){
  if(e.data.name === 'undo') {
    // handle before undo
    console.log('jartto:undo');
  }
  if(e.data.name === 'redo') {
    // handle before redo
    console.log('jartto:redo');
  }
});
```

#### 七、CKEditor 如何处理撤回操作
场景：用户操作后，需要撤销之前的操作，做一次回退，但不能保留程序代码的过程。
```js
var data = editor.getSnapshot();
editor.loadSnapshot( data );
```

#### 八、设置光标位置
```js
editor.focus();
var selection = editor.getSelection();
var range = selection.getRanges()[0];
var pCon = range.startContainer.getAscendant({p:2},true); //getAscendant('p',true);

var newRange = new CKEDITOR.dom.range(range.document);
newRange.moveToPosition(pCon, CKEDITOR.POSITION_BEFORE_START);
newRange.select();
```

相关文档地址：[DOM Range](https://docs.ckeditor.com/ckeditor4/latest/api/CKEDITOR_dom_range.html#method-moveToPosition)。

#### 九、事件监听
```js
// on change listener for cke ( source mode not supported )
editor.on( 'change', function() {
    // Debugger.log('ckeditor change event');
    //do stuff
});

// kludge onchange listener for cke source mode
editor.on( 'mode', function() {
    // Debugger.log('ckeditor mode change: '+ this.mode);
    if ( this.mode == 'source' ) {
        var editable = editor.editable();
        editable.attachListener( editable, 'input', function() {
            // Debugger.log('ckeditor source input change');
            editor.fire('change');
        } );
    }
} );
```

#### 十、递归获取元素节点
```js
function getTextNodes(element) {
  var children = element.getChildren(),child;

  for (var i = children.count(); i--;) {
    child = children.getItem(i);
    if (child.type == CKEDITOR.NODE_ELEMENT)
      getTextNodes(child);
    else if (child.type == CKEDITOR.NODE_TEXT)
      textNodes.push(child);
  }
}

getTextNodes(jartto_root_test);
```

#### 十一、如何处理选中的文本 Text
```js
// jartto's demo
var jartto = oEditor.getSelection().getRanges()[0].extractContents();
var container = CKEDITOR.dom.element.createFromHtml("<b/>", oEditor.document);

jartto.appendTo(container);
oEditor.insertElement(container);
```

#### 十二、增加快捷键操作
```js
// jartto's demo
( function() {
    CKEDITOR.plugins.add( 'keystrokes', {
        init: function( editor ) {
            editor.addCommand( 'h1', {
                exec: function( editor ) {
                    var format = { element: 'h1' };
                    var style = new CKEDITOR.style(format);
                    style.apply(editor.document);
                }
            } );
            editor.setKeystroke( CKEDITOR.ALT + 49 , 'h1' ); // ALT + 1
        }
    });
} )();
```

#### 十三、学习建议
- [guiders](https://docs.ckeditor.com/ckeditor4/latest/guide/dev_installation.html)
- [API](https://docs.ckeditor.com/ckeditor4/latest/api/index.html)
- [SDK](https://sdk.ckeditor.com/)

#### 十四、CKEditor4 和 CKEditor5
- 高扩展性和可用性
- 新的数据模型
- ES6支持

更多细节请参考：[The CKEditor 5 and CKEditor 4 comparison](https://support.ckeditor.com/hc/en-us/sections/115001489149-The-CKEditor-4-and-CKEditor-5-Comparison)。

#### 十五、资源汇总
- [官网](https://ckeditor.com/ckeditor-4/#inline)
- [CKEditor 插件库](https://ckeditor.com/cke4/addons/plugins/all)
- [文档地址](https://docs.ckeditor.com/ckeditor4/latest/api/CKEDITOR_event.html)
- [论坛](https://ckeditor.com/old/Forums/CKEditor)
- [开发指南](https://blog.csdn.net/sr3373739392/article/details/54929826)