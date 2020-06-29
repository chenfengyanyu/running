---
title: WebP 方案分析与实践
date: 2020-05-02 18:22:43
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/webp/logo.png
thumbnailImagePosition: left
tags: 
- webp
- web
- optimization
comments: false
metaAlignment: center
categories: 技术博文
---
对于网站来说，图片始终扮演着重要角色。图片大小直接影响网站速度、流量、运营成本以及用户体验。因此，减少图片大小成为网站优化最重要的一个环节。
<!-- more -->
如果你对优化还不甚了解，推荐阅读如下文章：
1.[网站优化实战](http://jartto.wang/2019/02/16/web-optimization/)
2.[网站优化工具](http://jartto.wang/2019/09/08/web-optimization-tools/)
3.[CSS 渲染原理以及优化策略](http://jartto.wang/2019/10/23/css-theory-and-optimization/)
4.[网站性能指标 - FMP](http://jartto.wang/2020/03/15/about-web-fmp/)
5.[聚焦 Web 性能指标 TTI](http://jartto.wang/2020/03/29/web-tti/)
6.[Web「性能测试」知多少？](http://jartto.wang/2020/04/05/about-wrk/)

#### 一、背景
当我们在做网站性能优化的时候，减少图片大小，意味着减少了网络传输，提升了网站加载速度，而这部分也是性价比最高的。

我们可以通过压缩图片来减少体积，但压缩比例一直是前端开发和设计师争执的焦点。压缩比例大的话，可以有效减少图片体积，对页面加载有利，但是却损失了像素，是设计师无法容忍的。

在这种矛盾的场景下，我们既要最大程度的压缩图片又要保持足够的清晰，`WebP` 便应运而生。
{% alert danger %}
值得注意的是：WebP 并不是新技术，而是受限于兼容性而未全面普及。
{% endalert %}

#### 二、目标与意义
1.时间成本
我们先来看一组数据对比图，如果你做过 `Gif` 动图，你肯定知道下面这样的处理意义有多大：
![WebP 时间对比](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/webp/sample.png)

{% alert success %}
在肉眼无法识别差异的前提下，图片大小减少了 88%。
{% endalert %}

2.带宽成本
- `YouTube` 的视频略缩图采用 `WebP` 格式后，网页加载速度提升了 `10%`；
- 谷歌的 `Chrome` 网上应用商店采用 `WebP` 格式图片后，每天可以节省几 `TB` 的带宽，页面平均加载时间大约减少 `1/3`；
- `Google+` 移动应用采用 `WebP` 图片格式后，每天节省了 `50TB` 数据存储空间。


目前 `Google`、`Facebook`、阿里、京东的等国内外互联网公司广泛应用了 `WebP`，超过 `70%` 的浏览器已经支持 `WebP`。

#### 三、什么是 WebP ？
{% alert info %}
WebP 格式，谷歌开发的一种旨在加快图片加载速度的图片格式。
{% endalert %}

优势在于它具有**更优的图像数据压缩算法**，在拥有肉眼无法识别差异的图像质量前提下，带来**更小的图片体积**，同时具备了无损和有损的压缩模式、`Alpha` 透明以及动画的特性，在 `JPEG` 和 `PNG` 上的转化效果都非常优秀、稳定和统一。

在尝试一些技术前，我们必须要充分了解他的**兼容性**，如下图：
![WebP 兼容性](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/webp/caniuse.png)

可以看出来，绝大多数浏览器已经有了较好的支持。当然，除了 `Safari` 和 `IE`！我们来看看浏览器市场占有率吧：
![浏览器市场占有率](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/webp/chrome.png)

{% alert warning %}
Safari 和 Foxmail 也在进行支持 WebP 的测试。
{% endalert %}

#### 四、应用场景
`WebP` 看起来不错，那么它究竟适合什么样的场景呢？不着急，我们先来看下面的图例：
![WebP 使用场景](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/webp/sence.png)

从上面我们可以看出，适合 `WebP` 场景的站点不外乎如下几种场景：
1.**网站图片比重大**
如果你的网站 `80%` 甚至更多依赖图片资源，那么请使用 `WebP`，资源成本可以较少 `50%` 以上。

2.**细节要求不高**
图片起到占位目的，不需要毛孔级别，那么请大胆使用！

3.**流量运营推广**
运营推广都是抢占先机，我们不但要和竞品比拼用户，更要快速响应，提高转化率。

4.**视频首图**
视频内容形式的网站，资源存储必是一大难题。如何做到稳准狠，速度必不可少，大量的视频占位图也便成为了重中之重。

#### 五、实践方案
相信到这里，你已经对 `WebP` 有了足够的了解，快来看看实际项目中我们是如何使用的吧～

**方式一：`HTML5 Picture`**
`Picture` 元素允许我们在不同的设备上显示不同的图片，一般用于响应式。
```html
<picture>
  <source type="image/webp" srcset="images/jartto.webp">
  <img src="images/jartto.jpg" alt=“jartto’s demo">
</picture>
```
`Picture` 算是最简单易行的方案了，但是需要注意以下两点：
1.兼容性，`IE` 不支持，可以查看[Picutre Element](https://caniuse.com/#search=picture)；
2.老项目迁移成本较大，需要改动每一个 `IMG` 资源，请尽可能封装成组件；

**方式二：`Webpack + Nginx`**
此方案在 `Webpack` 打包过程生成了 `.webp` 格式的图片，通过 `Nginx` 检测浏览器 `Accept` 是否包含 `image/webp` 而进行动态转发。
{% alert info %}
完整格式：accept: image/webp,image/apng,image/*,*/*;q=0.8
{% endalert %}

1.项目引入 `Webpack` 插件 `imagemin-webp-webpack-plugin`
```
const ImageminWebpWebpackPlugin = require('imagemin-webp-webpack-plugin’);

plugins: [
            new ImageminWebpWebpackPlugin({
                config: [
                    {
                        test: /\.(jpe?g|png)$/,
                        options: {
                            quality: 60,
                        }
                    }
                ],
                overrideExtension: false,
                detailedLogs: true,
                strict: false
            })
        ],
```
2.`Nginx` 配置
检测 `Accept` 头中是否含有 `WebP` 字段：
```bash
map $http_accept $webp_suffix {
    default   "";
    "~*webp"  ".webp";
}
```
如果浏览器支持 `WebP` 格式，那么我们就将 `.png` 或者 `.jpg` 格式图片转发到 `.webp` 格式下。
```bash
location  ~* ^/_nuxt/img/(.+\.png|jpe?g)$ {
    rewrite ^/_nuxt/img/(.+\.png|jpe?g)$ /$1 break;
    root /apps/srv/instance/test-webp.jartto.wang/.nuxt/dist/client/img/;
    add_header Vary Accept;
    try_files $uri$webp_suffix $uri =404;
    expires 30d;
}
```
设置完 `Nginx` 转发规则后，记得 `Reload Nginx`。刷新浏览器，这时候浏览器中的图片 `Type` 类型已经变成了 `WebP` 格式。

**方式三：服务端 `Nginx PageSpeed` 模块**
`Google` 开发的 `PageSpeed` 模块有一个功能，会自动将图像转换成 `WebP` 格式或者是浏览器所支持的其它格式。
{% alert info %}
安装 Nginx 模块请查看文档：[Build ngx_pagespeed From Source](https://www.modpagespeed.com/doc/build_ngx_pagespeed_from_source#dependencies)
{% endalert %}

安装成功之后，需要在 `Nginx Config` 中添加如下内容：
```nginx
pagespeed on;
pagespeed FileCachePath "/var/cache/ngx_pagespeed/";
# pagespeed RewriteLevel OptimizeForBandwidth;
pagespeed XHeaderValue "Powered By Jartto";
pagespeed EnableFilters convert_gif_to_png;
pagespeed EnableFilters convert_png_to_jpeg;
pagespeed EnableFilters convert_jpeg_to_webp;
pagespeed ImageRecompressionQuality 10;
# pagespeed EnableFilters convert_jpeg_to_progressive;
# pagespeed EnableFilters inline_images;

location ~ "\.pagespeed\.([a-z]\.)?[a-z]{2}\.[^.]{10}\.[^.]+" {
add_header "" "";
}
location ~ "^/pagespeed_static/" { }
location ~ "^/ngx_pagespeed_beacon$" { }
```
看起来此方案是最佳选择，既没有前端代码侵入，也不需要各种嗅探，服务端一个模块就搞定了。需要注意的是，当我们享受便利的同时，**一定要明确具体的原理，多维度思考**。

既然在服务端完成 `WebP` 格式的转化，那么一定要注意此操作对服务器的性能损耗。我们不妨试一下，通过 `Wrk` 对服务器施压，看一下服务器的并发性能。

具体的压测过程就不细说了，感兴趣的童鞋可以查看：[Web 性能测试](http://jartto.wang/2020/04/05/about-wrk/)。我们直接上结论：

![Nginx CPU](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/webp/nginx.png)

**相比转码，相同 `QPS` 下 `Nginx` 对 `CPU` 的使用上升了 `70%`，页面提升时间在毫秒量级，性价比不高**。

**方式四：`Nginx + Lua（OpenResty）`**
先来科普一下：`OpenResty` 是一个强大的 `Web` 应用服务器，`Web` 开发人员可以使用 `Lua` 脚本语言调动 `Nginx` 支持的各种 `C` 以及 `Lua` 模块，更主要的是在性能方面，`OpenResty` 可以快速构造出足以胜任 `10K` 以上并发连接响应的超高性能 `Web` 应用系统。

`Lua` 是一种轻量小巧的脚本语言，用标准 `C` 语言编写并以源代码形式开放，其设计目的是为了嵌入应用程序中，从而为应用程序提供灵活的扩展和定制功能。

**1.编写 `Lua` 脚本**
```lua
function file_exists(name)
   local f=io.open(name,"r")
   if f~=nil then io.close(f) return true else return false end
end
local newFile = ngx.var.request_filename;
local originalFile = newFile:sub(1, #newFile - 5);
if not file_exists(originalFile) then
  ngx.exit(404);
  return;
end
os.execute("cwebp -q 75 " .. originalFile  .. " -o " .. newFile);
if file_exists(newFile) then
    ngx.exec(ngx.var.uri)
else
    ngx.exit(404)
end
```
不考虑学习成本的话，`Nginx + Lua` 将会是最好的选择。

**2.配置 `Nginx` 文件**
```bash
# http 中加入，lua 脚本的搜索路径
lua_package_path "/usr/local/openresty/nginx/conf/jartto/?.lua;";


# server 中配置 location
location /images {
  expires 365d;
  # 如果不存在，则通过 @webp 进行内部重定向
  try_files $uri $uri/ @webp; 
}
location @webp{
  # 图片访问地址
  if ($uri ~ "/([a-zA-Z0-9-_]+)\.(png|jpg|gif)\.webp") { 
    # 查找执行的 lua 脚本
    content_by_lua_file "/usr/local/nginx/conf/jartto/webp.lua";
  }
}
```

**补充：浏览器嗅探（阿里云 `OSS` 方式）**
1.本地嗅探：
```js
// check_webp_feature:
// 'feature' can be one of 'lossy', 'lossless', 'alpha' or 'animation'.
// 'callback(feature, result)' will be passed back the detection result (in an asynchronous way!)
function check_webp_feature(feature, callback) {
    const kTestImages = {
        lossy: "UklGRiIAAABXRUJQVlA4IBYAAAAwAQCdASoBAAEADsD+JaQAA3AAAAAA",
        lossless: "UklGRhoAAABXRUJQVlA4TA0AAAAvAAAAEAcQERGIiP4HAA==",
        alpha: "UklGRkoAAABXRUJQVlA4WAoAAAAQAAAAAAAAAAAAQUxQSAwAAAARBxAR/Q9ERP8DAABWUDggGAAAABQBAJ0BKgEAAQAAAP4AAA3AAP7mtQAAAA==",
        animation: "UklGRlIAAABXRUJQVlA4WAoAAAASAAAAAAAAAAAAQU5JTQYAAAD/////AABBTk1GJgAAAAAAAAAAAAAAAAAAAGQAAABWUDhMDQAAAC8AAAAQBxAREYiI/gcA"
    };

    let img = new Image();
    img.onload = function () {
        const result = (img.width > 0) && (img.height > 0);
        callback(feature, result);
    };
    img.onerror = function () {
        callback(feature, false);
    };
    img.src = `data:image/webp;base64${kTestImages[feature]}`;
}
```
2.调用方式
```js
// Jartto's Demo
check_webp_feature('lossy', function (feature, isSupported) {
    if (isSupported) {
        // webp is supported, you can cache the result here if you want
    }
});
```
{% alert danger %}
请注意，图像加载是非阻塞且异步的。 这意味着依赖于 WebP 支持的任何代码最好都应放在回调函数中。
{% endalert %}

#### 六、部署模型
服务端部署模型需要简单了解一下，这样我们就可以根据不同的实际情况从上述[四种方案](http://jartto.wang/2020/05/02/webp-practice/)中进行选择了。
![Server](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/webp/server.png)

注意，如果你需要对 `Nginx` 进行配置，请不要操作 `Load Balancer` 层，尽量在应用服务器层操作。

#### 七、技术揭秘
听起来很酷，那么 `WebP` 究竟使用了什么很魔法，我们来揭秘一下：              

**1.分块 `MacroBlocking`**
将图片划分成多个宏块 `Macro Blocks`，典型的宏块由一个 `16×16` 的亮度像素 `luma pixel` 块和两个 `8×8` 的色度像素 `chroma pixel` 块组成。
{% alert success %}
分块越小，预测越准，需要记录的信息也越多。
{% endalert %}
一般来说，细节越丰富的地方，分块越细，即使用 `4×4` 分块预测。细节相对不丰富的地方使用 `16×16` 分块。

**2.帧内预测**
`WebP` 有损压缩使用了帧内预测编码，这一技术也被用于 `VP8` 视频编码中的关键帧压缩。

`VP8` 有四种常见的帧内预测模型：
- `H_PRED(horizontal prediction)`：像素块中每一行使用其左边一列 `Col L` 的数据填充；
- `V_PRED(vertical prediction)`：像素块中每一列使用其上边一行 `Row A` 的数据填充；
- `DC_PRED(DC prediction)`：像素块中每个单元使用 `Row A` 和 `Col L` 的所有像素的平均值填充；
- `TM_PRED(TrueMotion prediction)`：混合式，接近真实数据；

使用哪种分块预测模式是动态决定的。
{% alert info %}
编码器会将所有可能的预测模式都计算出来，然后选出错误程度最小的模式。
{% endalert %}

**3.算法编码**
`WebP` 使用 `Arithmetic entropy encoding`，该算法相比 `JPEG` 上使用的 `Huffman encoding`，在压缩表现上更出色。

深入研究，请移步：                   
1.[Compression Techniques](https://developers.google.com/speed/webp/docs/compression?hl=zh_cn)
2.[WebP 有损压缩的编码过程](https://www.jianshu.com/p/7fb720ec57cd)

#### 八、4种常见压缩格式
1. `Lossy`
有损压缩基于 `VP8` 关键帧编码。 `VP8` 是 一种视频压缩格式，是 `VP6` 和 `VP7` 格式的后继格式。

2. `Lossless`
无损压缩格式由 `WebP` 团队开发。

3. Alpha
`8` 位 `Alpha` 通道对于图形图像很有用。 `Alpha` 通道可与有损 `RGB` 一起使用，该功能目前无法在任何其他格式下使用。

4. Animation
它支持真彩色动画图像。

{% alert warning %}
24 位色被称为真彩色，它可以达到人眼分辨的极限，发色数是 1677 万多色，也就是 2 的 24 次方。
{% endalert %}

#### 九、WebP 方案 ROI
一般在做技术选型的过程中，我们都要评估引入技术的收益，这也就是我们常说的 `ROI`。那么如果你决定要使用 `WebP`，以下的数据可能会对你产生帮助：
1.优化后，网站资源大小从 `2.6MB` 减少到 `1.5MB`；              
2.图片大小减少到原来的 `1/8`；                  
3.同一时间服务器请求数增加 `335%`，请求时长减少 `75%`；                   
4.`Lighthouse Performance` 评分 `97`，`FCP`，`FMP`大概在 `0.5-0.7s`（和其他优化有关）；           

{% alert danger %}
网站数据可能不尽相同，以上数据仅供参考。
{% endalert %}

#### 十、总结
[上文](http://jartto.wang/2020/05/02/webp-practice/) 我们从 `WebP` 背景展开，介绍了它的兼容性、应用场景，以及 4 种实践方案，同时也提到了浏览器如何嗅探 `WebP` 格式。后半部分主要从原理出发，了解 `WebP` 格式的算法细节以及压缩格式，由浅入深，逐层剖析。

文章最后，打个小广告吧。如果你想搭上在线教育的快车，快速成长，不妨加入我们。一起成长，一起学习，一起挑战更多有趣的事情，「跟谁学-高途课堂」欢迎你，请将简历私我～



