---
title: linux下nginx部署配置多站点
date: 2018-05-13 11:25:50
description: 在nginx上面配置部署多个站点
tags:
  - linux
  - nginx
---

    摘要:
      有时候你想在一台服务器上为不同的域名运行不同的站点。
      比如www.siteA.com作为博客，www.siteB.com作为论坛。你可以把两个域名的IP都解析到你的服务器上，
      但是没法在Nginx的根目录里同时运行两个不同的网站.
      这时可以在nginx上面配置部署多个站点(使用nginx虚拟目录),为你节省服务器费用.
      假设你把博客放在”/home/user/www/blog”下，论坛放在”/home/user/www/forum”下。下面我们就开始进行配置:

1.在Nginx配置目录下，创建一个”vhost”目录。本例假设Nginx是默认安装，配置目录在”/etc/nginx”

`sudo mkdir /etc/nginx/vhost #创建保存站点配置文件的目录`

2.创建siteA的配置文件

'sudo vim /etc/nginx/vhost/siteA.conf #打开该文件(没有的话保存后会自动新建)'

在文件里面输入以下配置内容(具体的相关目录及location内容根据自己实际情况修改,下面只是nginx配置文件的基本结构,其实可以拷贝nginx自带的配置文件到vhost目录下,然后对文件内容进行修改):

    server {
      listen 80; # 监听端口
      server_name www.siteA.com siteA.com; # 站点域名
      root /home/user/www/blog; # 站点根目录
      index index.html index.htm index.php; # 默认导航页

      #rewrite ^(.*) https://$host$1 permanent; #重定向到https

      location / {
        # WordPress固定链接URL重写
        if (!-e $request_filename) {
          rewrite (.*) /index.php;
        }
      }
      location / {
        # WordPress固定链接URL重写
        if (!-e $request_filename) {
          rewrite (.*) /index.php;
        }
      }
      location ^~ /device/ {
    	  proxy_pass http://127.0.0.1:8080;
      }
      location ^~ /upload/ {  
        root  /aaa/bbb;
        expires   7d;
      }
    }

3.跟第二步一样,创建siteB的配置文件.("server_name”和"root”目录的内容和siteA不同)

'sudo vim /etc/nginx/vhost/siteB.conf #打开该文件(没有的话保存后会自动新建)'

在文件里面输入以下配置内容(具体的相关目录及location内容根据自己实际情况修改,下面只是nginx配置文件的基本结构,其实可以拷贝nginx自带的配置文件到vhost目录下,然后对文件内容进行修改):

    server {
      listen 80; # 监听端口
      server_name www.siteB.com siteB.com; # 站点域名
      root /home/user/www/blog; # 站点根目录
      index index.html index.htm index.php; # 默认导航页

      #rewrite ^(.*) https://$host$1 permanent; #重定向到https

      location / {
        # WordPress固定链接URL重写
        if (!-e $request_filename) {
          rewrite (.*) /index.php;
        }
      }
      location ^~ /device/ {
    	  proxy_pass http://127.0.0.1:8080;
      }
      location ^~ /upload/ {  
        root  /aaa/bbb;
        expires   7d;
      }
      # websocket地址
      location ^~ /ws {
		    proxy_pass http://127.0.0.1:10002;
	    }
    }

4.打开编辑nginx的配置文件

`sudo vim /etc/nginx/nginx.conf`

将我们第一步创建的虚拟目录的路径增加到nginx.conf文件中去,将下面的内容加入到”http {}”部分的末尾

    http {
      ...
      include /etc/nginx/vhost/*.conf;
    }

5.重启nginx服务(注意:所有的配置文件修改保存后,先不急重新加载配置,先使用`nginx -t`测试下文件内容是否有错在进行重新加载配置操作)

`sudo service nginx restart`

6.访问www.siteA.com和www.siteB.com，你将发现浏览器会打开不同的站点

nginx禁止ip访问的小技巧:

假如你的Nginx根目录设在”/home/user/www”，你想阻止别人通过”http://IP地址/blog”或”http://IP地址/forum”来访问你的站点，最简单的方法就是禁止IP地址访问。方法如下：

打开Nginx网站默认配置文件，记得先备份

`sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/default_bak #备份原来的默认文件`

`sudo vim /etc/nginx/sites-available/default #编辑文件`

将其所有内容删除，只留以下配置

    server {
      listen 80 default_server;
      server_name _;
      return 404;
    }

然后重启nginx或者`nginx -s reload`使配置文件生效,别人将无法通过IP地址访问网站了

如果你不想禁止IP地址访问整个目录，只是要防止别人通过IP访问你的博客和论坛。那就需要禁止”/blog”和”/forum”的目录访问

打开Nginx网站默认配置文件，同上面一样，记得先备份一下

在"server { }"节点的部分加上以下配置,然后重启nginx或者reload nginx配置即可.

    location ^~ /blog/ {
      deny all;
    }
    location ^~ /forum/ {
      deny all;
    }

7.nginx常用的配置选项模板
    /etc/nginx/nginx.conf

    http {
      sendfile on;
      tcp_nopush on;
      tcp_nodelay on;
      keepalive_timeout 65;
      types_hash_max_size 2048;
      server_tokens off; # 关闭nginx版本标识

      underscores_in_headers on; #自定义Header中含有下划线的情况 必须定义
      gzip  on;
      gzip_min_length 1k;
      gzip_buffers 16 64k;
      gzip_http_version 1.1;
      gzip_comp_level 6;
      gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;
      gzip_vary on;
      gzip_disable "MSIE [1-6]\.";

      #proxy_connect_timeout 600;  #nginx跟后端服务器连接超时时间(代理连接超时)
      
      proxy_buffer_size     32k;  #设置代理服务器（nginx）保存用户头信息的缓冲区大小
      proxy_buffers         4 32k;#proxy_buffers缓冲区，网页平均在32k以下的话，这样设置
      proxy_busy_buffers_size  64k;           #高负荷下缓冲大小（proxy_buffers*2）
      proxy_temp_file_write_size  1024m;       #设定缓存文件夹大小，大于这个值，将从upstream服务器传
      client_max_body_size 100M;

      # 给后端服务器暴露获取客户端真实IP地址的头
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header REMOTE-HOST $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

      # websocket 支持
      proxy_http_version 1.1;
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection "upgrade";
      proxy_read_timeout    600;  #连接成功后，后端服务器响应时间(代理接收超时)
      proxy_send_timeout    600;  #后端服务器数据回传时间(代理发送超时)
    }

8.站点配置文件样例,例如:/etc/nginx/vhost/a.conf

    server {
      listen          80;
      server_name     www.aaa.com  aaa.com;
      root            /opt/pages/;
      index           index.html index.htm;

      # Cookie的HttpOnly属性，指示浏览器不要在除HTTP（和HTTPS)请求之外暴露Cookie。一个有HttpOnly属性的Cookie，是不可以通过例如调用JavaScript(引用document.cookie)这种非HTTP方式来访问。因此，也不可能通过跨域脚本（一种非常普通的攻击技术）来偷走这种Cookie。
      add_header                  Set-Cookie "HttpOnly";
      # Cookie的Secure属性，意味着保持Cookie通信只限于加密传输，指示浏览器仅仅在通过安全/加密连接才能使用该Cookie。如果一个Web服务器从一个非安全连接里设置了一个带有secure属性的Cookie，当Cookie被发送到客户端时，它仍然能通过中间人攻击来拦截
      add_header                  Set-Cookie "Secure";
      # X-Frame-Options HTTP 响应头是用来给浏览器指示允许一个页面可否在 <frame>, <iframe> 或者 <object> 中展现的标记。网站可以使用此功能，来确保自己网站的内容没有被嵌到别人的网站中去，也从而避免了点击劫持 (clickjacking) 的攻击。它有三个可选择项：(DENY：表示该页面不允许在 frame 中展示，即便是在相同域名的页面中嵌套也不允许；SAMEORIGIN：表示该页面可以在相同域名页面的 frame 中展示；ALLOW-FROM uri地址：表示该页面可以在指定来源的 frame 中展示；)
      add_header                  X-Frame-Options "SAMEORIGIN";

      # 禁用OPTIONS TRACE不安全方法,屏蔽GET、POST、之外的HTTP方法
      if ($request_method !~* GET|POST) {
          return 403;
      }

      # 跨域配置
      location / {
        add_header Access-Control-Allow-Origin *;
        add_header Access-Control-Allow-Methods 'GET, POST, OPTIONS';
        add_header Access-Control-Allow-Headers 'DNT,X-Mx-ReqToken,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Authorization';

        if ($request_method = 'OPTIONS') {
          return 204;
        }
      }

      # 转发以/api/开头的请求
      location ^~ /api/ {
        proxy_pass http://127.0.0.1:8080;
      }

      # 以/upload开头的请求
      location ^~ /upload/ {
        root  /opt/dir/;
        expires   7d;
      }
    }

9.nginx负载均衡配置
  
  在http节点下配置服务器列表

    http {
      # upstream模块：配置反向代理服务器组，Nginx会根据配置，将请求分发给组里的某一台服务器。serverGroup是服务器组的名称.
      upstream serverGroup {
        server 192.168.0.100:8080;
        server 192.168.0.101:8080;
      }
      # serverGroup内部的server指令：配置处理请求的服务器IP或域名，端口可选，不配置默认使用80端口。通过上面的配置(默认的是轮询策略,把每个请求逐一分配到不同的server，如果分配到的server不可用，则分配到下一个，直到可用)，Nginx默认将请求依次分配给100，101来处理，可以通过修改下面这些参数来改变默认的分配策略：

      1.weight权重,默认为1，将请求平均分配给每台server.值越大，则被访问的概率越大.下面标示101访问数量是100的2倍
      upstream serverGroup {
        server 192.168.0.100:8080 weight=1;
        server 192.168.0.101:8080 weight=2 max_fails=3 fail_timeout=15;
        server 192.168.0.102:8080 down; #down 表示当前服务器不参与负载均衡，也就是说不会被访问到
        server 192.168.0.103:8080 backup; #backup 表示备份机，所有服务器挂了之后才会生效
      }
      max_fails:默认为1。某台Server允许请求失败的次数，超过最大次数后，在fail_timeout时间内，新的请求将不会分配给这台机器。如果设置为0，Nginx会将这台Server置为永久无效状态，然后将请求发给定义了proxy_next_upstream fastcgi_next_upstream, uwsgi_next_upstream, scgi_next_upstream, and memcached_next_upstream指令来处理这次错误的请求
      fail_timeout:默认为10秒。某台Server达到max_fails次失败请求后，在fail_timeout期间内，nginx会认为这台Server暂时不可用，不会将请求分配给它

      2.最少连接,把请求分配到连接数最少的server
      upstream serverGroup {
        least_conn;
        server 192.168.0.100:8080;
        server 192.168.0.101:8080;
      }

      3.ip_hash,根据访问客户端ip的hash值分配，这样同一客户端的请求都会被分配到同一个server上，如果牵扯到session的问题，用这个是最好的选择
      upstream serverGroup {
        ip_hash;
        server 192.168.0.100:8080;
        server 192.168.0.101:8080;
      }
    }

  在server节点下配置proxy_pass

    server {
        listen  80;
        server_name serverGroup;
        location / {
          proxy_pass   http://serverGroup; # 表示将所有请求转发到tomcats服务器组中配置的某一台服务器上
        }
    }