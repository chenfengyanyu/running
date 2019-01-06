---
title: 初探 Electron - 实践篇2
date: 2018-01-21 14:28:33
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/electron0.png
thumbnailImagePosition: left
tags: 
- electron
comments: false
metaAlignment: center
categories: 技术博文
---
上文 [初探 Electron - 实践篇1](http://jartto.wang/2018/01/14/first-exploration-electron-3/)中，我们完成了项目的主架构，虽然可以运行，但并不完美。这节我们继续补充，一起完成剩下的工作。
<!-- more -->
#### 八、使用 Material-UI
项目虽然有了主轮廓，但是外观并不令人满意。我们来引入一个 `UI` 框架 [`Material-UI`](http://www.material-ui.com/#/)，为项目添加一些亮丽的色彩。
```bash
npm install material-ui --save
```
{% alert success %}
至于为啥选 `Material-UI` ，大概是其他 `UI` 框架使用的太频繁了，换换口味😂
{% endalert %}

当然，从理论上讲，你可以引入任何你喜欢的 `UI` 框架，大家各取所需。

#### 九、增加 less 文件
仅仅引入 `UI` 框架是不够的，我们需要引入自己的样式文件，这里用 `less` 示例。首先，在 `app` 目录下，建立 `css` 文件夹：
```bash
cd app && mkdir css
```
这里将存放我们的 `less` 文件，我们分别添加 `main.less` 和 `_reset.less` 文件：
`main.less`，为项目的主要样式文件，代码如下：
```less
@import "./_reset.less";
body, .box::before{
  background: url(../image/bg.jpg) 0 / cover fixed;
  overflow: hidden;
}
a {
  text-decoration: none;
}
.box {
  text-align: center;
  color: #fff;
  position: absolute;
  width: 500px;
  height: 400px;
  background: rgba(255, 255, 255, 0.3);
  top: 50%;
  left: 50%;
  margin: -200px 0 0 -250px;
  border-radius: 8px;
  background: hsla(0,0%,100%,.3);
  overflow: hidden;
  box-shadow: 2px 2px 5px rgba(0, 0, 0, 0.3);
}
.box::before { 
  content: '';
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  filter: blur(10px);
  margin: -20px;
  z-index: -1;
}
.box {
  .desc {
    font-size: 12px;
    position: absolute;
    bottom: 90px;
    opacity: 0.6;
    cursor: pointer;
    letter-spacing: 1px;
  }
}
```
`_reset.less` 为重置样式表文件，具体代码如下：
```less
/* http://meyerweb.com/eric/tools/css/reset/ 
   v2.0 | 20110126
   License: none (public domain)
*/

html, body, div, span, applet, object, iframe,
h1, h2, h3, h4, h5, h6, p, blockquote, pre,
a, abbr, acronym, address, big, cite, code,
del, dfn, em, img, ins, kbd, q, s, samp,
small, strike, strong, sub, sup, tt, var,
b, u, i, center,
dl, dt, dd, ol, ul, li,
fieldset, form, label, legend,
table, caption, tbody, tfoot, thead, tr, th, td,
article, aside, canvas, details, embed, 
figure, figcaption, footer, header, hgroup, 
menu, nav, output, ruby, section, summary,
time, mark, audio, video {
	margin: 0;
	padding: 0;
	border: 0;
	font-size: 100%;
	font: inherit;
	vertical-align: baseline;
}
/* HTML5 display-role reset for older browsers */
article, aside, details, figcaption, figure, 
footer, header, hgroup, menu, nav, section {
	display: block;
}
body {
	line-height: 1;
}
ol, ul {
	list-style: none;
}
blockquote, q {
	quotes: none;
}
blockquote:before, blockquote:after,
q:before, q:after {
	content: '';
	content: none;
}
table {
	border-collapse: collapse;
	border-spacing: 0;
}
```

#### 十、美化首页
如上，我们已经准备好了样式文件，修改 `app/index.html` 文件，引入 `main.less`
```html
<link rel="stylesheet" type="text/less"  href="css/main.less" />
```
`less` 并不会自己编译，但是显然我们并没有做额外的工作，那究竟是为什么呢？
{% alert danger %}
心细的同学可能意识到了，一切的编译，`electron-compile` 已经帮我们做好了。
{% endalert %}

要美化首页，就需要去更新 `app/render.js` 文件，我们做如下更新：
```jsx
import React from 'react';
import ReactDOM from 'react-dom';
import getMuiTheme from 'material-ui/styles/getMuiTheme';
import MuiThemeProvider from 'material-ui/styles/MuiThemeProvider';
import SideMenu from './components/Menu';
import Upload from './components/Upload';

const muiTheme = getMuiTheme({
  fontFamily: 'Roboto, sans-serif',
  palette: {
    // primaryColor: white,
  }
});

class MainWindow extends React.Component {

  constructor(props) {
    super(props);

    this.state = {
      
    };
  }

  render() {
    return (
      <MuiThemeProvider muiTheme={muiTheme}>
        <div>
          <SideMenu />
          <Upload />
        </div>
      </MuiThemeProvider>
    );
  }
}

const mainWndComponent = ReactDOM.render(
  <MainWindow/>, document.getElementById('content'));
```

{% alert info %}
如上：我们使用了 `MuiThemeProvider` 外层容器，同时添加了 `SideMenu` 和 `Upload` 两个组件。
{% endalert %}

#### 十一、编写组件
首页 `UI` 已经就绪，我们来添加 `SideMenu` 和 `Upload` 这两个组件。`app` 目录下创建 `components` 文件夹：
```
cd app && mkdir components
```
接着来添加 `Upload.js` 也就是上面我们说的 `Upload` 组件：
{% alert danger %}
请先安装拖拽依赖包 `npm install react-dropzone --save`
{% endalert %}

```jsx
import React from 'react';
import ActionBackup from 'material-ui/svg-icons/action/backup';
import Dropzone from 'react-dropzone';

const dropStyles = {
  width: '300px',
  height: '300px',
  position: 'relative',
  top: '50%',
  left: '50%',
  margin: '-150px 0 0 -150px',
  display: 'flex',
  alignItems: 'center',
  justifyContent: 'center',
};

const iconStyles = {
  width: '100px',
  height: '100px',
  fill: '#ececec',
  cursor: 'pointer',
};

class Upload extends React.Component {
  /* eslint-disable */
  constructor(props) {
    super(props);
    // this.state = { files: [] };
  }

  onDrop(files) {
    files.forEach(file => {
      const reader = new FileReader();
      reader.onload = () => {
        console.log('Received files: ', file);
        const fileAsBinaryString = reader.result;
        this.props.history.push({pathname:'/preview', myfile: file, binary: fileAsBinaryString});
      };
      reader.onabort = () => console.log('file reading was aborted');
      reader.onerror = () => console.log('file reading has failed');

      reader.readAsBinaryString(file);
    });
  }

  render() {
    return (
      <div className="box">
        <Dropzone onDrop={this.onDrop.bind(this)} style={dropStyles}>
          <ActionBackup style={iconStyles}/>
          <div className="desc">拖拽文件或点击上传</div>
        </Dropzone>
      </div>
    );
  }
}

export default Upload;
```
代码看起来长，但是逻辑很简单，主要是依赖了一个 `react` 组件 `react-dropzone` 来实现拖拽的交互。触发方法 `onDrop` 然后读取文件流，详细信息可以参考[玩转图片流](http://jartto.wang/2018/01/19/play-image-stream/)，这里就不细说了。

下来，来看第二个组件 `SideMenu`，创建 `app/components/Menu.js` 文件：
```jsx
import React from 'react';
import Drawer from 'material-ui/Drawer';
import { List, ListItem } from 'material-ui/List';
import ActionInfo from 'material-ui/svg-icons/hardware/keyboard-arrow-right';
import Avatar from 'material-ui/Avatar';
import ActionBackup from 'material-ui/svg-icons/action/backup';
import { blue400 } from 'material-ui/styles/colors';
import NavigationMenu from 'material-ui/svg-icons/navigation/menu';
import FloatingActionButton from 'material-ui/FloatingActionButton';


const svgStyles = {
  fill: grey800,
};

export default class DrawerUndockedExample extends React.Component {
  constructor(props) {
    super(props);
    this.state = { open: false };
  }

  handleToggle() {
    this.setState({ open: !this.state.open });
  }

  handleClose() {
    this.setState({ open: false });
  }

  render() {
    return (
      <div className="_menu">
        <FloatingActionButton className="action" onClick={this.handleToggle.bind(this)} backgroundColor={lightWhite}  mini={true}>
          <NavigationMenu style={svgStyles} />
        </FloatingActionButton>
        <Drawer
          docked={false}
          width={230}
          open={this.state.open}
          onRequestChange={open => this.setState({ open })}
        >
          <List>
            <ListItem
              leftAvatar={<Avatar icon={<ActionBackup />} backgroundColor={blue400} />}
              rightIcon={<ActionInfo />}
              primaryText="上传图片"
            />
          </List>
        </Drawer>
      </div>
    );
  }
}
```
{% alert info %}
这里，我们增加了一按钮，用来触发菜单的操作，`UI` 样式请参考 [`Material-UI`](http://www.material-ui.com/#/)
{% endalert %}

#### 十二、页面路由
随着页面的不断完善，我们发现可能需要拆分出更多的页面，是时候引入 `react-router` 了。首先安装依赖包：
```js
npm install react-router-dom --save
```
我们来更新一下 `render.js` 文件，首先引入 `react-router`：
```js
import {
  HashRouter as Router,
  Route,
  Switch,
  Redirect,
} from 'react-router-dom';
```
修改渲染部分：
```jsx
render() {
  return (
    <Router>
      <MuiThemeProvider muiTheme={muiTheme}>
        <div>
          <SideMenu />
          <RefreshIndicator
            size={40}
            left={750}
            top={10}
            loadingColor="#FFB74D"
            status="loading"
            style={style.refresh}
          />
          <Switch>
            <Route path="/" exact component={Upload} />
            <Route path="/list" component={ListPage} />
            <Redirect to="/" />
          </Switch>
        </div>
      </MuiThemeProvider>
    </Router>
  );
}
```
页面部分使用，很简单，加上 `link` 就行了：
```jsx
<Link to="/" onClick={this.handleClose.bind(this)}>
  <ListItem
    leftAvatar={<Avatar icon={<ActionBackup />} backgroundColor={blue400} />}
    rightIcon={<ActionInfo />}
    primaryText="上传图片"
  />
</Link>
```
{% alert danger %}
注意，此处代码较多，所以我挑了一些核心代码展示，更多细节请参考项目：[Electron-OCR](https://github.com/chenfengyanyu/electron-ocr)
{% endalert %}


#### 十三、编码规范
说编码规范可能有些言过其实了，其实目的就是想统一一下编程风格，方便团队管理。当然，个人开发也用得上，起码不会犯一些语法错误，而影响项目进度。

我们全局安装 `eslint`：
```bash
npm install -g eslint
```
安装完之后，执行 `eslint-init` ，就会生成 `.eslintrc.js` 文件，修改配置如下：
```js
module.exports = {
  "extends": "airbnb",
  "parser": "babel-eslint",
  "ecmaFeatures": {
    "classes": true,
    "jsx": true,
  },
  "plugins": [
    "react",
    "jsx-a11y",
    "import"
  ],
  "rules": {
    "react/jsx-filename-extension": [1, { "extensions": [".js", ".jsx"] }],
  },
};
```
如果发现有如下异常，请安装 `babel-eslint` 文件：
```
ESLint: Cannot find module 'babel-eslint'. Please see the 'ESLint' output channel for details.
```
除此之外，我们要熟悉 `eslint` 的使用规则，记住下面的规则即可。
```
"off" or 0 - turn the rule off
"warn" or 1 - turn the rule on as a warning (doesn’t affect exit code)
"error" or 2 - turn the rule on as an error (exit code will be 1)
```
补充一点：局部禁用规则 `/* eslint-disable */`

#### 十四、对接 API
对接 `API` 的时候，少不了发送请求，这里我们依赖了 `superagent`，需要先安装：
```bash
npm install superagent --save
```
下面我们举一个简单的例子：
```js
import request from 'superagent';
// 一个简单的例子
export async function getAccessToken() {
  const param = qs.stringify({
    grant_type: 'client_credentials',
    client_id: Keys.ak,
    client_secret: Keys.sk,
  });
  return new Promise((resolve, reject) => {
    request
      .get(`http://jartto.wang/token?${param}`)
      .then((res) => {
        // res.body, res.headers, res.status
        resolve(res.body.access_token);
      })
      .catch((err) => {
        // err.message, err.response
        reject(err.message);
      });
  });
}
```
代码很简单，就不细说了。

#### 十五、使用 async/await
在上面的 `API` 请求中，我们用到了 `async`，这里我说一下怎样配置。首先，安装依赖：
```bash
cnpm install --save-dev babel-plugin-transform-runtime
cnpm install --save babel-runtime
```
接着配置 `compilerc`：
```json
{
  "plugins": [
    ["transform-runtime", {
      "polyfill": false,
      "regenerator": true
    }]
  ]
}
```
{% alert danger %}
如果出现异常：`await is a reserved word`，请注意配套 `async` 使用。
{% endalert %}

#### 十六、总结
本篇主要围绕具体实现来展开说明，提到了 `UI` 库、添加了路由、编写了组件、完善了规范、对接了 `API` 等等。功能也算是完成了七七八八，剩下的业务逻辑就不在这里啰嗦了。`Electron` 还有很多的技术细节，肯定也不是三言两语就能说完的，所以建议大家都去实际的应用一下，只有这样才能融汇贯通。

[Electron-OCR](https://github.com/chenfengyanyu/electron-ocr) 这个项目我还在继续，想到什么就加上去实现。欢迎大家加入进来，一起来做一个完美的识图工具。

