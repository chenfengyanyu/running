---
title: 30s 就可以掌握的 Nginx 片段
date: 2023-03-12 20:56:47
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/nginx/nginx.png
thumbnailImagePosition: left
tags: 
- nginx
comments: false
metaAlignment: center
categories: 技术博文
---
作为一枚程序员，日常研发少不了上线部署工作。一旦走上“基建”的道路，你就会发现 `Nginx` 是你绕不开的一个坎。毫不夸张的说：`Nginx` 能顶半边天！
<!-- more -->
也许你会反驳，我们有专业的运维（ `OP` ）团队，不用操心。然而实际情况却是 `OP` 每天被繁重的工单占据着，你无时无刻不在排队。大公司如此，小公司更甚。因此，储备一些 `Nginx` 知识，一定会让你事半功倍。
{% alert success %}
本文总结了日常开发中高频出现的 15 个 Nginx 配置片段，因为短小，所以你只需 30 秒就可以掌握。
{% endalert %}

#### 一、跨域配置
由于浏览器的安全策略，前端处理跨域请求的概率极高，如下是开启跨域请求常规手段。
```bash
if ($request_method = OPTIONS ) {
    add_header "Access-Control-Allow-Origin"  *;
    add_header "Access-Control-Allow-Methods" "GET, POST, OPTIONS, HEAD";
    add_header "Access-Control-Allow-Headers" "Authorization, Origin, X-Requested-With, Content-Type, Accept";
    add_header 'Access-Control-Max-Age' 600;
    return 200;
}
```

#### 二、开启 GZip 压缩
如果你希望压缩常规的文件类型，可以参考如下代码：
```bash
http 
{
  include       conf/mime.types;
  gzip on;
  gzip_min_length  1000;
  gzip_buffers     4 8k;   
  gzip_http_version 1.1; 
  gzip_types       text/plain application/x-javascript text/css application/xml application/javascript application/json;
}
```
GZip 涉及参数较多，还有 `gzip_comp_level`、`gzip_proxied`等，详情可以参考：[Nginx配置 - Gzip压缩](https://www.jianshu.com/p/e0ff1e275e7f)
#### 三、跨域传递 Cookie
`Chrome 80`以后的版本，`Cookie`默认不可跨域，除非服务器在响应头里再设置 `same-site` 属性（`strict`，`lax`，`none`）。
- `Strict`最为严格，完全禁止第三方`Cookie`，跨站点时，任何情况下都不会发送`Cookie`。换言之，只有当前网页的`URL`与请求目标一致，才会带上`Cookie`。这个规则过于严格，可能造成非常不好的用户体验。比如，当前网页有一个`GitHub`链接，用户点击跳转就不会带有`GitHub`的`Cookie`，跳转过去总是未登陆状态。
- `None`，`Cookie`只能通过`HTTPS`协议发送。必须同时设置`Secure`属性（`Cookie`只能通过`HTTPS`协议发送），否则无效。
```
Set-Cookie: widget_session=abc123; SameSite=None; Secure
```
- `Lax`规则稍稍放宽，大多数情况也是不发送第三方`Cookie`，但是导航到目标网址的`Get`请求除外。

还有一种方式是使用`proxy_pass`反向代理。如果只是`Host`、端口转换，则`Cookie`不会丢失。再次访问时，浏览器会发送当前的`Cookie`。当然，路径变化了，则需要设置`Cookie`的路径转换。
```bash
location /foo {
    proxy_pass http://localhost:4000;
    proxy_cookie_path /foo "/; SameSite=None; HTTPOnly; Secure";
}
```
#### 四、健康检查
`Nginx` 服务端会按照设定的间隔时间主动向后端的 `upstream_server` 发出检查请求来验证后端的各个 `upstream_server` 的状态。
如果得到某个服务器失败的返回超过一定次数，比如 3 次就会标记该服务器为异常，就不会将请求转发至该服务器。一般情况下后端服务器需要为这种健康检查专门提供一个低消耗的接口。
```bash
http {
  # 指定一个 upstream 负载均衡组，名称为 evalue
  upstream evalue {
    # 定义组内的节点服务，如果不加 weight 参数，默认就是 Round Robin ，加上了 weight 参数就是加权轮询
    server 192.168.90.100:9999 weight=100;
    server 192.168.90.101:9999 weight=100;
    
    # interval=3000 检查间隔 3 秒 ， rise=3 连续成功3次认为服务健康 ， fall=5 连续失败 5 次认为服务不健康 ， timeout=3000 健康检查的超时时间为 3 秒 ， type=http  检查类型 http
    check interval=3000 rise=3 fall=5 timeout=3000 type=http;
    
    # check_http_send 设定检查的行为：请求类型 url 请求协议 -> HEAD /api/v1/health HTTP/1.0         
    check_http_send "HEAD /api/v1/health HTTP/1.0\r\n\r\n";
    
    # 设定认为返回正常的响应状态       
    check_http_expect_alive http_2xx http_3xx;
  }
}
```
#### 五、泛域名解析
要在 `Nginx` 中配置域名泛解析，可以使用通配符 `*` 来实现次级域名指向同一 `IP` 地址。如下是一个简单的 `Nginx` 配置示例：
```bash
server {
    listen 80;
    server_name *.jartto.com;
    root /var/www/jartto.com;
}
```
上面的配置将所有以 `.jartto.com` 结尾的域名都指向 `/var/www/jartto.com` 目录下的网站。如果要配置二级域名，可以使用以下的 `Nginx` 配置：
```bash
server {
    listen 80;
    server_name *.sub.jartto.com;
    root /var/www/sub.jartto.com;
}
```
上面的配置将所有以 `.sub.example.com` 结尾的二级域名都指向 `/var/www/sub.example.com` 目录下的网站。需要注意的是，在使用泛解析时，可能会导致一些安全问题。因此，建议仅在必要时使用泛解析，并且要对网站进行充分保护。

#### 六、使用 $request_id 实现链路追踪
`Nginx`在`1.11.0`版本中就提供了内置变量`$request_id`，其原理就是生成 32 位的随机字符串，虽不能比拟 `UUID` 的概率，但 32 位的随机字符串的重复概率也是微不足道了，所以一般可视为 `UUID` 来使用。
```bash
location ^~ /habo/gid {
        add_header Cache-Control no-store;
        default_type application/javascript;
        set $unionId $cookie_GID;
        if ($unionId = "") {
                set $unionId $request_id;
                add_header Set-Cookie "GID=${unionId};path=/habo/;max-age=${GID_MAX_AGE}";
        }
        return 200 "document.cookie='GID=${unionId};path=/;max-age=${GID_MAX_AGE}'";
    }
```
#### 七、限流配置
{% alert info %}
`Nginx`提供了两种限流方式：控制速率和控制并发连接数。
{% endalert %}
其中，控制速率是指限制单位时间内的请求次数，而控制并发连接数是指限制同时处理的请求数量。

下面是一个简单的`Nginx`限流配置示例，使用`leaky bucket`算法进行限流：
```bash
http {
    limit_req_zone $binary_remote_addr zone=one:10m rate=1r/s;

    server {
        location / {
            limit_req zone=one burst=5;
            proxy_pass http://backend;
        }
    }
}
```
在上述配置中，`limit_req_zone`用于定义一个名为`one`的共享内存区域，并将其与客户端`IP`地址相关联。这个共享内存区域最大占用`10MB`空间，并且允许每秒钟通过 1 个请求。然后，在`location`块中使用了`limit_req`指令来启用限流功能。这里设置`burst`为 5，表示当客户端在短时间内发送超过 1 个请求时，可以暂时容忍一定数量的请求超出限制。
需要注意的是，在实际应用中需要根据具体情况调整参数值以达到最佳效果。如下：
```bash
map $http_baggage_flow  $plimit {
 "ptest" $server_name;
 default "";
}


limit_req_zone $plimit zone=prelimit:10m rate=600r/s;
 server {
  listen       443 ssl;
  server_name   m.gaotu100.com;
  limit_req zone=prelimit  nodelay;
  limit_req_status 530;

  location = /530.html {
      default_type application/json;
      return 200 '{"status" : 530}';
  }
  ...
}
```

#### 八、History 二级路由刷新问题
`vue-router+webpack`项目线上部署时单页项目路由，刷新页面出现 `404` 问题，一般需要配置如下：
```bash
location / {
　　root html;
　　try_files $uri $uri/ @router;
　　index index.html index.htm;

}
location @router {
　　rewrite ^.*$ /index.html last;
}
```


#### 九、Cookie 路由识别
最常见的场景就是灰度发布，`Nginx` 来识别来自前端的流量，从而进行转发。
```
map $http_cookie $m_upstream {
    ~*baggage-version=isolute-feat-.*$ al-bj-sre-k8s-test-istio-gateway;
    default test.jartto.com;
}

upstream al-bj-sre-k8s-test-istio-gateway {
    server 47.95.128.11:80;
}
```

#### 十、服务端开启图片转换
这里主要是设置 `WebP` 格式图片，如果你还不了解，请查看：[`WebP` 方案分析与实践](http://jartto.wang/2020/05/02/webp-practice/)。
```
map $http_accept $webp_suffix {
    default   "";
    "~*webp"  ".webp";
}
```

```
location  ~* ^/_nuxt/img/(.+\.png|jpe?g)$ {
    rewrite ^/_nuxt/img/(.+\.png|jpe?g)$ /$1 break;
    root /apps/srv/instance/test-webp.gaotu100.com/.nuxt/dist/client/img/;
    add_header Vary Accept;
    try_files $uri$webp_suffix $uri =404;
    expires 30d;
}
```

#### 十一、负载均衡
负载均衡通常有四种算法：
- 轮询，默认方式，每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务挂了，能自动剔除；
- `weight`，权重分配，指定轮询几率，权重越高，在被访问的概率越大，用于后端服务器性能不均的情况；
- `ip_hash`，每个请求按访问 `IP` 的 `hash` 结果分配，这样每个访客固定访问一个后端服务器，可以解决动态网页 `session` 共享问题。负载均衡每次请求都会重新定位到服务器集群中的某一个，那么已经登录某一个服务器的用户再重新定位到另一个服务器，其登录信息将会丢失，这样显然是不妥的；
- `fair`（第三方），按后端服务器的响应时间分配，响应时间短的优先分配，依赖第三方插件 `nginx-upstream-fair`，使用前请先安装；
```
http {
  upstream jartto-server {
  	# ip_hash;  # ip_hash 方式
    # fair;   # fair 方式
    server 127.0.0.1:8081;  # 负载均衡目的服务地址
    server 127.0.0.1:8080;
    server 127.0.0.1:8082 weight=10;  # weight 方式，不写默认为 1
  }
 
  server {
    location / {
    	proxy_pass http://jartto-server;
      proxy_connect_timeout 10;
    }
  }
}

```
#### 十二、配置 HTTPS
要在 `Nginx` 上配置 `HTTPS`，可以按照以下步骤操作：
1. 为 `Nginx` 安装 `SSL` 模块。 这可以通过使用 `--with-http_ssl_module` 选项编译 `Nginx` 或安装包含 `SSL` 模块的预构建包来完成。
2. 从受信任的证书颁发机构`CA`为您的域获取`SSL`证书。 这可以通过购买证书或从`Let's Encrypt`获得免费证书来完成。
3. 配置`Nginx`以使用`SSL`证书和密钥文件。这涉及将以下行添加到您的`Nginx`配置文件中：
```bash
  ssl_certificate /path/to/jartto.crt;
  ssl_certificate_key /path/to/jartto.key;
```
4. 如果需要，配置`Nginx`将`HTTP`请求重定向到`HTTPS`。 这可以使用服务器块来完成，该服务器块监听端口`80`并将所有请求重定向到端口`443`（默认 `HTTPS` 端口）。
5. 重新启动`Nginx`以应用更改。

下面是启用`HTTPS`并将`HTTP`请求重定向到`HTTPS`的`Nginx`配置文件示例：
```bash
server {
    listen 80;
    server_name jartto.com www.jartto.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl;
    server_name jartto.com www.jartto.com;

    ssl_certificate /path/to/jartto.crt;
    ssl_certificate_key /path/to/jartto.key;
    # Other SSL-related settings go here
    # Other server block settings go here
}
```
如上配置文件同时监听端口`80`和`443`，但仅在端口`443`上通过`HTTPS`提供内容。所有`HTTP`请求都使用第一个服务器块中的返回语句重定向到它们等效的`HTTPS URL`。
#### 十三、图片防盗链
如果你不想图片被外网随便引用，那么可以配置图片防盗链能力，配置如下：
```bash
server {
  listen       80;        
  server_name  *.jartto.com;
  
  # 图片防盗链
  location ~* \.(gif|jpg|jpeg|png|bmp|swf)$ {
    valid_referers none blocked server_names ~\.google\. ~\.baidu\. *.qq.com;  # 只允许本机 IP 外链引用
    if ($invalid_referer){
      return 403;
    }
  }
}

```
#### 十四、配置多 Server
要在`Nginx`中配置多个服务器，可以在`Nginx`配置文件中定义多个服务器块。每个服务器块代表一个单独的虚拟服务器，可以监听不同的端口或`IP`地址，并提供不同的内容。下面是如何在`Nginx`中配置两个虚拟服务器的示例：
```bash
http {
  server {
    listen 80;
    server_name www.jartto.com;
    root /var/www/jartto.com;

    # other server configuration directives
  }

  server {
    listen 80;
    server_name www.another-jartto.com;
    root /var/www/another-jartto.com;

    # other server configuration directives
  }
}
```
上述示例中，我们定义了两个监听端口`80`的虚拟服务器。第一个虚拟服务器配置为从目录`/var/www/jartto.com`为域`www.jartto.com`提供内容。第二个虚拟服务器配置为从目录`/var/www/another-jartto.com`为域`www.another-jartto.com`提供内容。每个服务器块都可以有自己的一组配置指令，例如`SSL`证书、访问日志、错误页面等等。 

{% alert info %}
通过在`Nginx`配置文件中定义多个服务器块，我们就可以在单个`Nginx`实例上托管多个网站或应用程序。
{% endalert %}
#### 十五、动态修改配置模块
`ngx_dynamic_upstream` 是用于使用 `HTTP API` 动态操作上游的模块，例如 `ngx_http_upstream_conf`。如果你想动态修改`Nginx`配置信息，那么不妨试试如下代码：
```
upstream backends {
    zone zone_for_backends 1m;
    server 127.0.0.1:6001;
    server 127.0.0.1:6002;
    server 127.0.0.1:6003;
}


server {
    listen 6000;

    location /dynamic {
      allow 127.0.0.1;
      deny all;
      dynamic_upstream;
    }


    location / {
      proxy_pass http://backends;
    }
}
```
使用方式如下：
```
$ curl "http://127.0.0.1:6000/dynamic?upstream=zone_for_backends&verbose="
server 127.0.0.1:6001 weight=1 max_fails=1 fail_timeout=10;
server 127.0.0.1:6002 weight=1 max_fails=1 fail_timeout=10;
server 127.0.0.1:6003 weight=1 max_fails=1 fail_timeout=10;
```





