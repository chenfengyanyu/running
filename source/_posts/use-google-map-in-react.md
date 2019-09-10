---
title: 在 React 中接入 Google Map
date: 2017-05-18 22:43:57
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/googleMap0.png
thumbnailImagePosition: left
tags: 
- google
- map
comments: false
metaAlignment: center
categories: 技术博文 
---
业务需求，正好对接了 google map 。初次使用，碰到了很多细节问题，于是总结整理，也算是学习过程自己踩过的坑吧。
<!-- more -->
#### 一、React 中如何引入
1.使用 [google-map-react](https://github.com/istarkov/google-map-react) ；
```bash
npm install --save google-map-react
```
使用示例：
```jsx
import React, { Component } from 'react';
import GoogleMapReact from 'google-map-react';

const AnyReactComponent = ({ text }) => <div>{text}</div>;

class SimpleMap extends Component {
  static defaultProps = {
    center: {lat: 59.95, lng: 30.33},
    zoom: 11
  };

  render() {
    return (
      <GoogleMapReact
        defaultCenter={this.props.center}
        defaultZoom={this.props.zoom}
      >
        <AnyReactComponent
          lat={59.955413}
          lng={30.337844}
          text={'Kreyser Avrora'}
        />
      </GoogleMapReact>
    );
  }
}
```
[demo 演示地址](http://istarkov.github.io/google-map-react/map/simple_hover/)

2.使用 [ react-google-maps ](https://github.com/tomchentw/react-google-maps) ；
```bash
npm install --save react-google-maps # or
yarn add react-google-maps
```
简单代码示例：
```jsx
import {
  withGoogleMap,
  GoogleMap,
  Marker,
} from "react-google-maps";

const MapWithAMarker = withGoogleMap(props =>
  <GoogleMap
    defaultZoom={8}
    defaultCenter={{ lat: -34.397, lng: 150.644 }}
  >
    <Marker
      position={{ lat: -34.397, lng: 150.644 }}
    />
  </GoogleMap>
);

<MapWithAMarker
  containerElement={<div style={{ height: `400px` }} />}
  mapElement={<div style={{ height: `100%` }} />}
/>
```
[demo 文档地址](https://tomchentw.github.io/react-google-maps/) ，[demo 演示地址](https://tomchentw.github.io/#/demos/react-google-maps)

友情提示，记得提前引入 `Google Map API`：
```js
<script src="https://maps.googleapis.com/maps/api/js?key=YOUR_API_KEY&callback=initMap"
```

#### 二、API 调用次数限制：
`Google Map` 虽然是免费的，但是也是有调用次数限制，这里列举几条计数的规则：
- 重新加载；
- 调用地图；
- 切换卫星模式；
- 切换全景模式；

#### 三、经纬度查询：
处理地图业务，可能会用到一个经纬度检测工具：[ GPS SPG ](http://www.gpsspg.com/maps.htm)

#### 四、地图个性化
如果你不想局限于默认的地图，可以在这里找到答案：[个性化地图样式](https://github.com/tomchentw/react-google-maps/issues/53)：

首先，我们需要下载一个 `JSON` 类型的[样式文件](https://github.com/wuct/react-google-maps/blob/21d70d0afd03aa802cdc69028c8ead2f35b3c1ce/examples/gh-pages/scripts/components/basics/StyledMap.js#L40-L43)

其次，引入文件：
```js
const demoFancyMapStyles = require("./demoFancyMapStyles.json");
```
最后，在 `Map` 初始化的时候应用定义样式：
```js
<GoogleMap
    defaultZoom={5}
    defaultCenter={props.center}
    defaultOptions={{ styles: demoFancyMapStyles }}
  >
</GoogleMap>
```
OK，大功告成，可以刷新页面查看新的地图样式了。此外，这里推荐两款样式相关的工具：
- [地图样式编辑器](https://snazzymaps.com/editor/customize/2)
- [样式库](https://snazzymaps.com/explore?text=&sort=&tag=&color=gray)


#### 五、关于 `DrawingManager`
1.`DrawingManager undefined` 处理方式：
遇到这个问题，需要在加载 `Map API` 的时候初始化 `libraries=drawing`，如下：
```
<script src="https://maps.googleapis.com/maps/api/js?key=AIzaSyB8Qspe99idJxy7QlEi_VIH2Bcl9y5Mlqk&libraries=drawing"></script>
```

2.`DrawingManagerOption` 说明文档：
- [ Drawinglayer ](https://developers.google.com/maps/documentation/javascript/drawinglayer?hl=zh-cn)
- [DrawingManagerOptions](https://developers.google.com/maps/documentation/javascript/reference?hl=zh-cn#DrawingManagerOptions)

3.`DrawManager` 画图按键监听：
当我们使用 `DrawManager` 的时候，可能会有按键监听的需求，那么只需要这么写：
```js
google.maps.event.addDomListener(document, 'keyup', function (e) {    
  var code = (e.keyCode ? e.keyCode : e.which);
  if (code === 27) {
    drawingManager.setDrawingMode(null);
    polygon.setMap(null);
  }
});
```
更多内容，请查看：[参考示例](http://jsfiddle.net/upsidown/a1j58c2b/)

4.对于 `DrawManager` 来说，清除地图时需要调用 `setDrawingMode(null)`：
```js
this._drawingManager.setDrawingMode(null);
clearFlag.setMap && clearFlag.setMap(null);
```

此处参考：
[how-to-remove-previous-shape-when-drawing-new-shape-is-started-on-google-map](http://stackoverflow.com/questions/30342519/how-to-remove-previous-shape-when-drawing-new-shape-is-started-on-google-map)


5.`Draw Manage` 如果要拖动图形，需要把 `Clickable` 设置为 `True`
```js
clickable: true,
draggable: true,
```

6.扩展 `Google Draw Manager Toolbar`
我们可以发现，`Draw Manager ` 只支持给定的配置，如果我们有扩展的需求呢？此处，我们需要清除 `Map` 上绘制的图形，所以只能自行扩展，思路如下：
{% alert info %}
添加一个与 `Draw Manager ` 相似的按钮，控制显示位置即可。
{% endalert %}
```css
.clearbtn{
  border: 1px solid #e2e1e1;
  border-left: 1px solid #f6f4f4;
  display: inline-block;
  padding: 2.5px 5px;
  position: absolute;
  bottom: 4px;
  left: 50%;
  margin-left: 48px;
  cursor: pointer;
  background: #fff;
  &:hover{
    background: #eceaea;
  }
}
```

7.如何给 `Draw Manager` 添加修改监听事件？
{% alert success %}
绘制图形最难的就是对其添加事件监听，我们需要注意两个事件 `set_at` 和 `insert_at`
{% endalert %}

```js
// Add an event listener that selects the newly-drawn shape when the user
    // mouses down on it.
var newShape = e.overlay;
newShape.type = e.type;
google.maps.event.addListener(newShape, 'click', function() {
    google.maps.event.addListener(newShape.getPath(), 'set_at', function() {
        console.log("test");
    });

    google.maps.event.addListener(newShape.getPath(), 'insert_at', function() {
        console.log("test");
    });
  setSelection(newShape);
});
```

此处参考：
- [event-after-modifying-polygon-in-google-maps-api-v3](http://stackoverflow.com/questions/12515748/event-after-modifying-polygon-in-google-maps-api-v3)
- [多边形：Polygon Class](https://developers.google.com/maps/documentation/javascript/reference?hl=zh-cn#Polygon)

#### 六、地图事件监听
上述例子只是列举了个别监听事件，详细请查看文档：[事件监听 API ](https://developers.google.com/maps/documentation/javascript/reference?hl=zh-cn#MapsEventListener)

示例代码：
```js
handleOverlayComplete(event) {
let newShape = event.overlay;
clearFlag = event.overlay;
newShape.type = event.type;
shapes.push(newShape);

if (this._drawingManager.getDrawingMode()) {
  this._drawingManager.setDrawingMode(null);
}

if (newShape.type === 'polygon') {
  google.maps.event.addListener(newShape.getPath(), 'set_at', () => {
    console.log(newShape.getPaths(), 'polygon_set_at');
  });

  google.maps.event.addListener(newShape.getPath(), 'insert_at', () => {
    console.log(newShape.getPaths(), 'polygon_insert_at');
  });
  } else if (newShape.type === 'rectangle') {
    google.maps.event.addListener(newShape, 'bounds_changed', () => {
    console.log(newShape.getBounds(), 'rectangle_changed');
    });
  } else if (newShape.type === 'circle') {
    google.maps.event.addListener(newShape, 'radius_changed', () => {
    console.log(newShape.getRadius(), 'circle_radius_changed');
    });
    google.maps.event.addListener(newShape, 'center_changed', () => {
    console.log(newShape.getCenter().lat(), newShape.getCenter().lng(), 'circle_center_changed');
    });
  }
}
```

#### 七、引入多个 `Map Lib`
多个 `Map Lib` 需要在引入 `API` 的时候，需要用 `&` 连接起来，如： `libraries=geometry,places&sensor=false`，示例如下：
```html
<script type="text/javascript"
  src="http://maps.googleapis.com/maps/api/js?libraries=geometry,places&sensor=false">
```
此处容易出现的问题，如：
1.this api project is not authorized to use this API [如何解决](http://stackoverflow.com/questions/32994634/this-api-project-is-not-authorized-to-use-this-api-please-ensure-that-this-api)：
```
From the picture you posted, it say it's disabled...

Go to Developer Console -> APIs & auth -> APIs

Search for Geocoding and click on Google Maps Geocoding API -> Enable API. Do the same thing for Geolocating
```

[API 文档](https://developers.google.com/maps/documentation/javascript/libraries)

#### 八、如何更改 `Marker` 图标：
```jsx
const image = {
  url: 'https://developers.google.com/maps/documentation/javascript/examples/full/images/beachflag.png',
  // This marker is 20 pixels wide by 32 pixels high.
  size: new google.maps.Size(20, 32),
  // The origin for this image is (0, 0).
  origin: new google.maps.Point(0, 0),
  // The anchor for this image is the base of the flagpole at (0, 32).
  anchor: new google.maps.Point(0, 32)
};

<Marker position={marker.position} key={index} icon={image} />
```

1.为标记[添加动画](https://developers.google.com/maps/documentation/javascript/markers?hl=zh-cn#animate)：
```js
marker = new google.maps.Marker({
  map: map,
  draggable: true,
  animation: google.maps.Animation.DROP,
  position: {lat: 59.327, lng: 18.067}
});
```

2.标记如何全部显示在可见区域：
- `Marker` 适配示例
```js
var markers = [];//some array
var bounds = new google.maps.LatLngBounds();
for (var i = 0; i < markers.length; i++) {
 bounds.extend(markers[i].getPosition());
}

map.fitBounds(bounds);
```
- `Circle` 适配示例
```js
var bounds = circle.getBounds();
bounds.union(rectangle.getBounds();
for (var aa=0; aa < arrayLatitude.length; aa++) {
  var points = new google.maps.LatLng(arrayLatitude[aa], arrayLongitude[aa]);
  bounds.extend(points);
}

map.fitBounds(bounds);
```
更多详情，请参考如下：
[ Marker ](http://stackoverflow.com/questions/19304574/center-set-zoom-of-map-to-cover-all-visible-markers)
[ Circle ](http://stackoverflow.com/questions/9768439/how-to-google-map-circles-fit-the-map)
[ Shape ](http://stackoverflow.com/questions/19887218/how-can-i-fitbounds-for-all-shapes-on-the-map)
[ LatLngBounds ](https://developers.google.com/maps/documentation/javascript/reference#LatLngBounds)