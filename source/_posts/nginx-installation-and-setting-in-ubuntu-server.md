---
title: 在ubuntu上安装和配置Nginx
date: 2017-04-05 10:51:24
tags:
- Nginx
- ubuntu
- 多站点
- Linux
categories:
- Linux
---
废话:最近一直想写博客,憋了好几篇,但是一直没时间,从年前拖到了年后,一直拖到4月份,真是伤不起,不过,总算开动了

# 1 系统环境
服务器:ubuntu server 16.04.2 LTS
主要工具:xshell

我这个服务器是腾讯云的主机,装的最新的ubuntu server lts发行版,32位系统.

# 2 系统安装
我这里是完全参考的屈大的教程,因为屈大教程会不断更新,所以我这里就不赘述,直接贴地址
[https://imququ.com/post/my-nginx-conf.html](https://imququ.com/post/my-nginx-conf.html "本博客Nginx配置之完整篇")

ps:由于屈大的Nginx配置是https一步到位,我的建议是,刚开始玩的话,先把所以https相关的配置注释掉,等一切顺利跑起来了,后期再加上https的内容

`Nginx全局配置`,直接抄屈大的就行,只需要把最后一行,改成你自己的服务器的路径
```
    include            /home/jerry/www/nginx_conf/*.conf;
```
`web站点配置`注释掉https相关内容后,如下(我修改了屈大部分目录的路径,原理一样的):

```
server{
        listen 80;

        server_name bblove.me www.bblove.me;
        server_tokens   off;
        access_log /home/chen/www/nginx_log/bblove_me.log;


#        ssl_certificate /home/chen/www/nginx_ssl/bblove_me/bblove_me.cer;
#        ssl_certificate_key     /home/chen/www/nginx_ssl/bblove_me/bblove_me.key;
#        ssl_dhparam    /home/chen/www/nginx_ssl/dhparams.pem;
#        ssl_prefer_server_ciphers  on;
#        ssl_ciphers                EECDH+AES128:RSA+AES128:EECDH+AES256:RSA+AES256:EECDH+3DES:RSA+3DES:!MD5;

#       ssl_protocols              TLSv1 TLSv1.1 TLSv1.2;

#       ssl_session_cache          shared:SSL:50m;
#        ssl_session_timeout        1d;

#       ssl_session_tickets        on;
#        ssl_stapling    on;
#        ssl_stapling_verify     on;
#        ssl_trusted_certificate /home/chen/www/nginx_ssl/bblove_me/bblove_me.cer;
        resolver        114.114.114.114 valid=300s;
        resolver_timeout        10s;
        if ($request_method !~ ^(GET|HEAD|POST|OPTIONS)$ ) {
                return           444;
        }
#        if ($host != 'bblove.me'){
#                rewrite ^/(.*)$ https://bblove.me/$1 permanent;
#        }
        location / {
                root /home/chen/www/bblove_me;
                index index.html index.htm;
                add_header               Strict-Transport-Security "max-age=31536000; includeSubDomains; preload";
                add_header               X-Frame-Options deny;
                add_header               X-Content-Type-Options nosniff;
                add_header               Cache-Control no-cache;
                add_header               x-xss-protection " 1; mode=block";
                add_header               x-content-type-options nosniff;
        }
}

```
上面的配置完成后,使用`sudo service nginx reload`,重新加载配置文件,如果没有生效,就重启一下服务器.如果服务出错,可以使用`sudo service nginx status`查看是哪一行的配置出错,修改以后再reload.
到此步,服务器应该就配置好了,可以使用ip访问你的服务器了`http://x.x.x.x`,此处如果无法访问,请查看
[安全组配置](#4-结尾),检查是否开放了80端口

# 3 修改DNS配置
我使用的DNSPOD的服务,直接去管理后台,添加两条解析记录,将`www`和`@`添加A记录,指向自己的服务器ip.
如果你不添加`www`的话,那访问你的博客只能使用`bblove.me`访问.添加两条记录的话,`www.bblove.me`和`bblove.me`都可访问你的博客.
而且,后面配置`https`的时候,如果要上HSTS的功能,就必须要配置`www`.
![DNSPOD配置](https://ws1.sinaimg.cn/large/692869a3gy1febmtogaq5j20v904gglw.jpg)
配置好之后,可能要等一会儿才会生效
到这一步,你的服务器应该就可以使用域名访问了,http://域名

# 4 本站目录设置
本站因为后期会布置多站点,所以设置目录结构如下,`www`目录下包含两个域名对应的网站根目录,同时还设置了配置文件的目录,日志文件的目录,https证书相关目录等,如图所示.

![本站目录设置](https://ws1.sinaimg.cn/large/692869a3gy1fecyn6c5hyj20fg0hn0tc.jpg)

# 5 结尾
我把多站点配置和https配置放到下一篇博客,这篇就简单讲这么多吧.

小建议:
+ 配置Nginx的时候善用`sudo service nginx status`,这个用来定位错误很方便
+ 如果使用云服务器,比如腾讯云的话,需要修改安全组策略,把80端口和443端口放开![安全组配置](https://ws1.sinaimg.cn/large/692869a3gy1febolh4yq0j21170fiq4b.jpg)
+ 如果想查看端口是否打开,可以使用[站长工具-在线端口扫描](http://tool.chinaz.com/port/)