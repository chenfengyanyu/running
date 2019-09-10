---
title: Nginx 使用与异常处理
date: 2017-04-15 23:00:04
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/nginx.png
thumbnailImagePosition: left
tags: 
- nginx
comments: false
metaAlignment: center
categories: 技术博文 
---
以前总是偷懒使用 Http-Server 来启动一个本地服务，后来花时间学习了一下 Nginx，感觉挺好用。总结整理一下，就当打点存档了。
<!-- more -->
#### 一、简单介绍
Nginx — Ngine X，是一款自由的、开源的、高性能 HTTP 服务器和反向代理服务器；也是一个 IMAP 、POP3 、SMTP 代理服务器；也就是说 Nginx 本身就可以托管网站（类似于 Tomcat 一样），进行 Http 服务处理，也可以作为反向代理服务器使用。

Nginx 解决了服务器的 C10K（就是在一秒之内连接客户端的数目为10k即1万）问题。它的设计不像传统的服务器那样使用线程处理请求，而是一个更加高级的机制—事件驱动机制，是一种异步事件驱动结构。

{% alert success %}
Nginx 有一个主线程（ master process）和几个工作线程（worker process）。主线程的目的是**加载**和**验证配置文件**、**维护工作线程**。
{% endalert %}

工作线程处理实际的请求，Nginx 采用基于事件的模型和依赖操作系统的机制在工作线程之间高效地分发请求。工作线程的数量可配置，也可自动调整为服务器 CPU 的数量。

#### 二、搞懂正向代理和反向代码
- 正向代理：
首先，代理服务器一般指局域网内部的机器通过代理服务器发送请求到互联网上的服务器，代理服务器一般作用在客户端。例如：GoAgent 翻墙软件。我们的客户端在进行翻墙操作的时候，我们使用的正是正向代理，通过正向代理的方式，在我们的客户端运行一个软件，将我们的 HTTP 请求转发到其他不同的服务器端，实现请求的分发。

- 反向代理：
反向代理服务器作用在服务器端，它在服务器端接收客户端的请求，然后将请求分发给具体的服务器进行处理，然后再将服务器的相应结果反馈给客户端。Nginx 就是一个反向代理服务器软件。

#### 三、Nginx 安装与使用
这里我已 Mac 安装举例：
```bash
brew search nginx

brew install nginx
```

Nginx 及其模块的工作方式由配置文件确定。 默认情况下，配置文件名为 `nginx.conf`，放在 `/usr/local/nginx/conf` 、`/etc/nginx` 或者 `/usr/local/etc/nginx` 文件夹中。

安装完，打开浏览器，访问 `localhost:8080` ，页面出现欢迎语 `Welcome to nginx!`，则说明安装成功。

#### 四、Nginx 查看配置
```bash
nginx -t
```
当你执行 `nginx -t` 的时候，`Nginx` 会去测试你得配置文件得语法，并告诉你配置文件是否写得正确，同时也告诉了你配置文件得路径，如下：

```bash
nginx: the configuration file /usr/local/etc/nginx/nginx.conf syntax is ok
nginx: configuration file /usr/local/etc/nginx/nginx.conf test is successful
```

通过 `vim` 查看与编辑配置文件：
```
vim /usr/local/etc/nginx/nginx.confıg
```

启动 `Nginx`:
```bash
nginx
```

重启 `Nginx`：
```bash
nginx -s reload
```

退出 `Nginx`:
```bash
nginx -s quit
```

#### 五、Nginx 配置文件
通过 `vim /usr/local/etc/nginx/nginx.confıg` 打开 `Nginx` 配置文件：
```bash
#定义 Nginx 运行的用户和用户组
user www www;

# Nginx 进程数，建议设置为等于 CPU 总核心数
worker_processes 8;

#全局错误日志定义类型，[ debug | info | notice | warn | error | crit ]
error_log ar/loginx/error.log info;

#进程文件
pid ar/runinx.pid;

#一个 Nginx 进程打开的最多文件描述符数目，理论值应该是最多打开文件数（系统的值 ulimit -n ）与 Nginx 进程数相除，但是 Nginx 分配请求并不均匀，所以建议与 ulimit -n 的值保持一致。
worker_rlimit_nofile 65535;

#工作模式与连接数上限
events
{
  #参考事件模型，use [ kqueue | rtsig | epoll | /dev/poll | select | poll ]; epoll 模型是 Linux 2.6以上版本内核中的高性能网络 I/O 模型，如果跑在 FreeBSD 上面，就用 kqueue 模型。
  use epoll;
  #单个进程最大连接数（最大连接数=连接数*进程数）
  worker_connections 65535;
}

#设定http服务器
http
{
  include mime.types; #文件扩展名与文件类型映射表
  default_type application/octet-stream; #默认文件类型
  charset utf-8; #默认编码
  server_names_hash_bucket_size 128; #服务器名字的hash表大小
  client_header_buffer_size 32k; #上传文件大小限制
  large_client_header_buffers 4 64k; #设定请求缓
  client_max_body_size 8m; #设定请求缓
  sendfile on; #开启高效文件传输模式，sendfile指令指定nginx是否调用sendfile函数来输出文件，对于普通应用设为 on，如果用来进行下载等应用磁盘IO重负载应用，可设置为off，以平衡磁盘与网络I/O处理速度，降低系统的负载。注意：如果图片显示不正常把这个改 成off。

  autoindex on; #开启目录列表访问，合适下载服务器，默认关闭。
  tcp_nopush on; #防止网络阻塞
  tcp_nodelay on; #防止网络阻塞
  keepalive_timeout 120; #长连接超时时间，单位是秒

  #FastCGI相关参数是为了改善网站的性能：减少资源占用，提高访问速度。下面参数看字面意思都能理解。
  fastcgi_connect_timeout 300;
  fastcgi_send_timeout 300;
  fastcgi_read_timeout 300;
  fastcgi_buffer_size 64k;
  fastcgi_buffers 4 64k;
  fastcgi_busy_buffers_size 128k;
  fastcgi_temp_file_write_size 128k;

  #gzip模块设置
  gzip on; #开启gzip压缩输出
  gzip_min_length 1k; #最小压缩文件大小
  gzip_buffers 4 16k; #压缩缓冲区
  gzip_http_version 1.0; #压缩版本（默认1.1，前端如果是squid2.5请使用1.0）
  gzip_comp_level 2; #压缩等级
  gzip_types text/plain application/x-javascript text/css application/xml;
  #压缩类型，默认就已经包含 textml，所以下面就不用再写了，写上去也不会有问题，但是会有一个 warn。
  gzip_vary on;

  limit_zone crawler $binary_remote_addr 10m; #开启限制IP连接数的时候需要使用

  upstream blog.linuxidc.com {
    #upstream的负载均衡，weight是权重，可以根据机器配置定义权重。weigth参数表示权值，权值越高被分配到的几率越大。
    server 192.168.80.121:80 weight=3;
    server 192.168.80.122:80 weight=2;
    server 192.168.80.123:80 weight=3;
  }
  #虚拟主机的配置
  server
  {
    #监听端口
    listen 80;

    #域名可以有多个，用空格隔开
    server_name www.linuxidc.com linuxidc.com;
    index index.html index.htm index.php;
    root /data/www/linuxidc;
    location ~ .*.(php|php5)?$
    {
      fastcgi_pass 127.0.0.1:9000;
      fastcgi_index index.php;
      include fastcgi.conf;
    }
    location ~ .*.(gif|jpg|jpeg|png|bmp|swf)$
    {
      expires 10d;
    }

    #JS和CSS缓存时间设置
    location ~ .*.(js|css)?$
    {
      expires 1h;
    }

    #日志格式设定
    log_format access '$remote_addr - $remote_user [$time_local] "$request" '
    '$status $body_bytes_sent "$http_referer" '
    '"$http_user_agent" $http_x_forwarded_for';

    #定义本虚拟主机的访问日志
    access_log ar/loginx/linuxidcaccess.log access;
    
    #对 "/" 启用反向代理
    location / {
      proxy_pass http://127.0.0.1:88;
      proxy_redirect off;
      proxy_set_header X-Real-IP $remote_addr;
      #后端的Web服务器可以通过X-Forwarded-For获取用户真实IP
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      #以下是一些反向代理的配置，可选。
      proxy_set_header Host $host;
      client_max_body_size 10m; #允许客户端请求的最大单文件字节数
      client_body_buffer_size 128k; #缓冲区代理缓冲用户端请求的最大字节数，
      proxy_connect_timeout 90; #nginx跟后端服务器连接超时时间(代理连接超时)
      proxy_send_timeout 90; #后端服务器数据回传时间(代理发送超时)
      proxy_read_timeout 90; #连接成功后，后端服务器响应时间(代理接收超时)
      proxy_buffer_size 4k; #设置代理服务器（nginx）保存用户头信息的缓冲区大小
      proxy_buffers 4 32k; #proxy_buffers缓冲区，网页平均在32k以下的设置
      proxy_busy_buffers_size 64k; #高负荷下缓冲大小（proxy_buffers*2）
      proxy_temp_file_write_size 64k; #设定缓存文件夹大小，大于这个值，将从upstream服务器传
    }

    #设定查看Nginx状态的地址
    location /NginxStatus {
      stub_status on;
      access_log on;
      auth_basic "NginxStatus";
      auth_basic_user_file confpasswd;
      # htpasswd 文件的内容可以用 apache 提供的 htpasswd 工具来产生。
    }

    #本地动静分离反向代理配置
    #所有jsp的页面均交由tomcat或resin处理
    location ~ .(jsp|jspx|do)?$ {
      proxy_set_header Host $host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_pass http://127.0.0.1:8080;
    }

    #所有静态文件由 nginx 直接读取不经过 tomcat 或 resin
    location ~ .*.(htm|html|gif|jpg|jpeg|png|bmp|swf|ioc|rar|zip|txt|flv|mid|doc|ppt|pdf|xls|mp3|wma)$
    { expires 15d; }
    location ~ .*.(js|css)?$
    { expires 1h; }
  }
}

```

深入了解，请参考：[Config 配置详解](http://linux.it.net.cn/e/server/nginx/2015/0621/15898.html)

#### 六、Nginx 配置 HTTPS
```bash
server {
  listen 80;
  listen 443 ssl;

  server_name passport.sensoro.com;

  ssl_certificate     /ssl/https.crt;
  ssl_certificate_key /ssl/https.key;

  proxy_set_header Host $http_host;
  proxy_set_header X-Real-IP $remote_addr;
  proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  proxy_set_header X-Forwarded-Proto $scheme;
  proxy_http_version 1.1;
  proxy_set_header Upgrade $http_upgrade;
  proxy_set_header Connection "upgrade";

  more_set_headers 'X-Powered-By: Jartto';

  location / {
      access_log /var/log/nginx/user-service.log  main;
      proxy_pass http://192.168.1.4:3000/;
      client_max_body_size 10M;
      proxy_redirect off;
  }
}
```

#### 七、Nginx 中使用 rewrite
1.Nginx Rewrite 基本标记( flags )
last - 基本上都用这个 Flag。
※相当于Apache里的[L]标记，表示完成 rewrite，不再匹配后面的规则
break - 中止 Rewirte ，不再继续匹配
redirect - 返回临时重定向的 HTTP 状态302
permanent - 返回永久重定向的 HTTP 状态301 ※原有的 url 支持正则，重写的 url 不支持正则

2.正则表达式匹配，其中：
* ~ 为区分大小写匹配
* ~* 为不区分大小写匹配
* !~和!~* 分别为区分大小写不匹配及不区分大小写不匹配

3.文件及目录匹配，其中：
* -f和!-f用来判断是否存在文件
* -d和!-d用来判断是否存在目录
* -e和!-e用来判断是否存在文件或目录
* -x和!-x用来判断文件是否可执行

4.Nginx 的一些可用的全局变量，可用做条件判断：
- $args
- $content_length
- $content_type
- $document_root
- $document_uri
- $host
- $http_user_agent
- $http_cookie
- $limit_rate
- $request_body_file
- $request_method
- $remote_addr
- $remote_port
- $remote_user
- $request_filename
- $request_uri
- $query_string
- $scheme
- $server_protocol
- $server_addr
- $server_name
- $server_port
- $uri

举个例子，将所有 `ithov.com` 与 `netseek.ithov.com` 域名全部自跳转到 `http://www.ithov.com`
```bash
server
{
  listen 80;
  server_name ithov.com netseek.ithov.com;
  index index.html index.php;
  root /data/www/wwwroot;
  if ($host !~ "^www.linxtone.org$") {
    rewrite ^(.*) http://www.ithov.com$1 redirect;
  }
  ...
}
```

深入了解，请参考：
[Nginx Rewrite 和 Redirect 模块配置方法说明](https://www.server110.com/nginx/201403/8413.html)
[Nginx rewrite 指令](http://www.nginx.cn/216.html)
[Nginx 中文文档](https://wizardforcel.gitbooks.io/nginx-doc/content/Text/3.21_rewrite.html)

#### 八、Nginx open 异常
```bash
nginx: [error] open() "/usr/local/var/run/nginx.pid" failed (2: No such file or directory)
```

解决方法：
```bash
[root@localhost nginx]# /usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
```

使用 `nginx -c` 的参数指定 `nginx.conf` 文件的位置
```bash
[root@localhost nginx]# cd logs/
[root@localhost logs]# ll
```

总用量 12
```
-rw-r--r-- 1 root root 1246 12月  9 18:10 access.log
-rw-r--r-- 1 root root  516 12月 10 15:39 error.log
-rw-r--r-- 1 root root    5 12月 10 15:38 nginx.pid
```

可以看到 `nginx.pid` 文件已经有了，之后执行 `nginx -s reload` 重启 Nginx，问题搞定。

#### 九、Nginx 启动异常
当我们运行 `nginx -s reload` 的时候，会报这样的错误：
```bash
nginx: [alert] kill(647, 1) failed (3: No such process)
```
这是因为，我们并没有启动 `Nginx` 服务，执行 `nginx` 命令启动服务。

#### 十、invalid PID number
在 `Mac` 上用 `brew` 安装 `Nginx`，然后修改 `Nginx` 配置文件，再重启时报出如下错误：

```sh
nginx: [error] invalid PID number “” in “/usr/local/var/run/nginx/nginx.pid”
```

解决方案：
```sh
$ sudo nginx -c /usr/local/etc/nginx/nginx.conf
$ sudo nginx -s reload
```

#### 十一、Nginx 403 forbidden 原因
引起 `nginx 403 forbidden` 可能有二种原因，具体如下：
1.缺少 `index.html` 或者 `index.php` 文件
```bash
server {
 listen       80;
 server_name  localhost;
 index  index.php index.html;
 root  /home/jartto/www;
}
```
{% alert info %}
如果在 `/home/jartto/www` 下面没有 `index.php,index.html` 的时候，直接访问域名，找不到文件，会报 `403 forbidden` 。
{% endalert %}

2.权限问题[详情请参考](http://blog.51yip.com/apachenginx/1512.html)
因为权限问题引起的403，比较难查找，但是值得我们注意。
```bash
server {
 listen       80;
 server_name  localhost;
 index  index.php index.html;
 root  /home/jartto/www;
}
```
{% alert danger %}
可以看到， `web` 目录放在用户的所属目录下面，`Nginx` 的启动用户默认是 `Nginx`的，所以对目录根本没有读的权限，这样就会报403错误了。
{% endalert %}

解决方案：
这个时候，把 `web` 目录的权限改大，或者是把 `Nginx` 的启动用户改成目录的所属用户，重启一下。


#### 十二、Nginx 资源汇总
- [Nginx 初探](http://blog.csdn.net/xlgen157387/article/details/49781487)
- [初识 Nginx](https://lufficc.com/blog/nginx-for-beginners)
- [gitbooks](https://wizardforcel.gitbooks.io/nginx-doc/content/)
- [Nginx 中文文档](http://www.nginx.cn/doc/)
