---
title: Nginx上多站点的https配置
date: 2017-04-05 13:09:24
tags:
- Nginx
- ubunt
- Linux
categories:
- Linux
---
**更新**
+ 2017-06-13 修改证书生成目录,为方便后续的git push自动更新,将acme验证目录移出网站根目录


此处接上篇[在ubuntu上安装和配置Nginx](https://bblove.me/2017/04/05/nginx-installation-and-setting-in-ubuntu-server),本文在之前的基础上配置https.

# 1 环境和目标
服务器:ubuntu server 16.04.2 LTS
主要工具:xshell
本文的目标是使用Nginx的TLS SNI技术,使用一个服务器(即一个ip),给多个域名配置https.我有一个腾讯云服务器,买了两个域名,都指向这台服务器,我现在想两个域名都上https,而且`www.bblove.me`跳转到`bblove.me`,我只想使用`bblove.me`这个较短的域名.
但是不是所有的浏览器都支持SNI,下面的截图来自屈大的博客
![SNI支持情况](https://ws1.sinaimg.cn/large/692869a3gy1febqcpfjoaj20kv08mjrs.jpg)
由于是个人博客,随意折腾,所以不犹豫,直接上https.

# 2 证书
我们使用Let's Encrypt提供的免费证书,每次有90天的有效期,过期之后需要再更新,不过这对于程序员来说,根本就不是事儿对吧.点击[ACME](https://github.com/Neilpang/acme.sh),我们使用ACME来自动获取证书.主要步骤分三步:
+ 安装 acme.sh
+ 生成证书
+ copy 证书到 nginx
具体步骤,参考github上的wiki  [ACME使用说明](https://github.com/Neilpang/acme.sh/wiki/%E8%AF%B4%E6%98%8E)
下面我简单说下我这边的配置:
## 2.1 安装acme.sh
安装的命令很简单
```bash
curl  https://get.acme.sh | sh
```
建议将脚本放在自己的home目录下,并且建立一个alias
```bash
acme.sh=~/.acme.sh/acme.sh
```
操作的目录都只会在`~/acme.sh`目录,不会污染系统其他的文件夹和目录

## 2.2 生成证书
脚本安装好后,下一步开始生成证书,生成证书的过程中,Let's Encrypt需要对域名的所有权进行验证,这个很好理解,证书发行机构需要确定你是否对该域名拥有所有权.验证的方式有两种:
+ 使用http验证
acme脚本会生成一个验证文件,并放到网站根目录,然后Let's Encrypt会尝试访问这个文件,完成验证
+ 使用DNS验证
顾名思义,只需要在DNS设置中添加一条txt解析记录,即可

本文采用的是http验证,DNS验证就不细说了,想使用DNS验证的直接看 [ACME使用说明](https://github.com/Neilpang/acme.sh/wiki/%E8%AF%B4%E6%98%8E)

我一共有两个域名`bblove.me`和`huirongis.me`,主域名加上带`www`的子域名,也就是一共有4个域名.
生成证书有下面三种思路:
+ 所有的4个域名都使用同一个证书,此法最简单,可以避免发生域名和证书不匹配的情况
+ `www.bblove.me`和`bblove.me`使用同一个证书,另外一个域名同理,也就是总共使用两个证书
+ 使用4个不同的证书,这种情况比较复杂,如果想实现`www`跳转到不带`www`的,处理不好容易出现证书错误的问题

由于我最后只使用`https://bblove.me`这个域名,验证方式选择的http方式,`bblove.me`和`huirongis.me`指向不同的根目录,为了方便后续管理和维护,所以我选择方案二,即`bblove.me`和`www.bblove.me`共用一个证书,`huirongis.me`同理.
确定好方案后,下面开始生成证书,使用如下命令即可.
```bash
acme.sh --issue -d bblove.me -d www.bblove.me -w /home/chen/www/nginx_acme/bblove_me/ --force
```
其中`-d`表示域名,可以添加多个;`-w`表示网站的根目录,一条命令执行完成,即可得到证书.
顺利的话,执行结果如下(此处配图输入命令未更新,输出结果是一致的):
![生成证书](https://ws1.sinaimg.cn/large/692869a3gy1fecw65xpvrj20lv0ll0vo.jpg)
如果出错的话,在那条命令后面追加`--debug`,查看详细的信息,我有遇到过文件权限的错误,只要网站根目录权限和当前home目录对应的用户权限一样就可以了;我还遇到过验证不通过的问题,大家可以在浏览器中访问那个路径,看是否正常,类似`http://bblove.me/.well-known/acme-challenge/dfdfdfdafdfdfdfdfdfdfdfd`这样的格式
## 2.3 安装证书
在上一步中,我们已经成功的生成了我们要的证书.这一步我们将它安装在我们域名所对应的根目录中,操作很简单.
```bash
acme.sh --installcert -d bblove.me --keypath /home/chen/www/nginx_ssl/bblove_me/bblove_me.key --fullchainpath /home/chen/www/nginx_ssl/bblove_me/bblove_me.cer --reloadcmd "service nginx force-reload"
```
执行结果如图所示:
![安装证书](https://ws1.sinaimg.cn/large/692869a3gy1fecwexr6nej20g606gaaf.jpg)
至此,证书部分已经操作完成,第二个域名操作同理.
```bash
#生成证书
acme.sh --issue -d bblove.me -d www.bblove.me -w /home/chen/www/nginx_acme/bblove_me/ --force
#安装证书
acme.sh --installcert -d bblove.me --keypath /home/chen/www/nginx_ssl/bblove_me/bblove_me.key --fullchainpath /home/chen/www/nginx_ssl/bblove_me/bblove_me.cer --reloadcmd "service nginx force-reload"

#生成证书
acme.sh --issue -d huirongis.me -d www.huirongis.me -w /home/chen/www/huirongis_me/ --force
#安装证书
acme.sh --installcert -d huirongis.me --keypath /home/chen/www/nginx_ssl/huirongis_me/huirongis_me.key --fullchainpath /home/chen/www/nginx_ssl/huirongis_me/huirongis_me.cer --reloadcmd "service nginx force-reload"

```

# 3 修改Nginx配置
证书生成完成后,我们需要修改Nginx配置,主要就是启用之前注释掉的HTTPS相关的配置以及,修改对应的目录.
下面是我的第一个站点的配置:
```bash
server {
    listen 80;
    server_name       www.bblove.me bblove.me;
    server_tokens     off;

    access_log /home/chen/www/nginx_log/bblove_me.log;
    #access_log        /dev/null;

    if ($request_method !~ ^(GET|HEAD|POST)$ ) {
        return        444;
    }

    location ^~ /.well-known/acme-challenge/ {
        alias         /home/chen/www/nginx_acme/bblove_me/.well-known/acme-challenge/;
        try_files     $uri =404;
    }

    location / {
        rewrite       ^/(.*)$ https://bblove.me/$1 permanent;
    }
}
server{
        listen 443 ssl http2 fastopen=3 reuseport;

        server_name bblove.me;
        server_tokens   off;
        access_log /home/chen/www/nginx_log/bblove_me.log;


        ssl_certificate /home/chen/www/nginx_ssl/bblove_me/bblove_me.cer;
        ssl_certificate_key     /home/chen/www/nginx_ssl/bblove_me/bblove_me.key;

		#使用openssl dhparam -out dhparams.pem 2048命令生成该文件,将下面配置注释也许
        ssl_dhparam    /home/chen/www/nginx_ssl/dhparams.pem;

        ssl_prefer_server_ciphers  on;


		#下面是推荐的加密算法配置,但是在我的机器上有bug,chacha20算法无法正常工作,所以我去掉了其中包含chacha20的算法
		# https://github.com/cloudflare/sslconfig/blob/master/conf
    	#ssl_ciphers                EECDH+CHACHA20:EECDH+CHACHA20-draft:EECDH+AES128:RSA+AES128:EECDH+AES256:RSA+AES256:EECDH+3DES:RSA+3DES:!MD5;
        ssl_ciphers                EECDH+AES128:RSA+AES128:EECDH+AES256:RSA+AES256:EECDH+3DES:RSA+3DES:!MD5;


        ssl_protocols              TLSv1 TLSv1.1 TLSv1.2;

        ssl_session_cache          shared:SSL:50m;
        ssl_session_timeout        1d;

        ssl_session_tickets        on;
        ssl_stapling    on;
        ssl_stapling_verify     on;
        ssl_trusted_certificate /home/chen/www/nginx_ssl/bblove_me/bblove_me.cer;
        resolver        114.114.114.114 valid=300s;
        resolver_timeout        10s;
        if ($request_method !~ ^(GET|HEAD|POST|OPTIONS)$ ) {
                return           444;
        }
        if ($host != 'bblove.me'){
                rewrite ^/(.*)$ https://bblove.me/$1 permanent;
        }
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
下面是我第二个站点的配置:
```bash
server{
	listen 443 ssl http2;

	server_name www.huirongis.me huirongis.me;
	server_tokens	off;
	access_log /home/chen/www/nginx_log/huirongis_me.log;

	
	ssl_certificate	/home/chen/www/nginx_ssl/huirongis_me/huirongis_me.cer;
	ssl_certificate_key	/home/chen/www/nginx_ssl/huirongis_me/huirongis_me.key;
	#ssl_dhparam	/home/chen/www/nginx_ssl/huirongis_me/huirongis_me.cer;
	ssl_ciphers                EECDH+AES128:RSA+AES128:EECDH+AES256:RSA+AES256:EECDH+3DES:RSA+3DES:!MD5;
	#ssl_ciphers                EECDH+CHACHA20:EECDH+CHACHA20-draft:EECDH+AES128:RSA+AES128:EECDH+AES256:RSA+AES256:EECDH+3DES:RSA+3DES:!MD5;
	ssl_prefer_server_ciphers  on;

	ssl_protocols              TLSv1 TLSv1.1 TLSv1.2;

	ssl_session_cache          shared:SSL:50m;
	ssl_session_timeout        1d;

	ssl_session_tickets        on;
	ssl_stapling	on;
	ssl_stapling_verify	on;
	ssl_trusted_certificate	/home/chen/www/nginx_ssl/huirongis_me/huirongis_me.cer;
	resolver	114.114.114.114 valid=300s;
	resolver_timeout	10s;
	if ($request_method !~ ^(GET|HEAD|POST|OPTIONS)$ ) {
        	return           444;
	}
	if ($host != 'huirongis.me'){
		rewrite ^/(.*)$ https://huirongis.me/$1 permanent;
	}	
	location / {
		root /home/chen/www/huirongis_me;
		index index.html index.htm;
	        add_header               Strict-Transport-Security "max-age=31536000; includeSubDomains; preload";
		add_header               X-Frame-Options deny;
	        add_header               X-Content-Type-Options nosniff;
		add_header               Cache-Control no-cache;
		add_header		 x-xss-protection " 1; mode=block";
		add_header		 x-content-type-options nosniff;
	}
}
server {
    server_name       www.huirongis.me huirongis.me;
    server_tokens     off;

    access_log        /dev/null;

    if ($request_method !~ ^(GET|HEAD|POST)$ ) {
        return        444;
    }

    location ^~ /.well-known/acme-challenge/ {
        alias         /home/chen/www/nginx_acme/huirongis_me/.well-known/acme-challenge/;
        try_files     $uri =404;
    }

    location / {
        rewrite       ^/(.*)$ https://huirongis.me/$1 permanent;
    }
}
```
该配置的主体内容,都是参考的屈大的配置,但是由于屈大的是单站点,所以为了对应多站点,做了一些改动.

+ 改动1: server的第一行listen `fastopen=3 reuseport`的选项,只能用在一个站点上,不能两个站点都加这段配置
+ 改动2: ssl_dhparam	配置,另外一个站点我懒得弄了,你可以自己生成文件,两个站点都配上.

有些配置的含义和原理,屈大都给了详细的分析,想了解的可以去看看.

再贴一下本站目录结构:
![本站目录设置](https://ws1.sinaimg.cn/large/692869a3gy1fgivj9x7g0j20nj0ert9g.jpg)

# 4 修改http资源为https
ok,都这里基本都搞定了,应该可以看到小绿锁了.在chrome中按`F12`->`console`,查看原因.如果你的网站中包含其他第三方http资源,可能会没有看到小绿锁,只需要将http资源修改为https的就行,比如图片资源,本站使用新浪微博图床,只需要将原来的http格式的图片加个s,变成https就可以了.如果资源没有https的,那就需要将资源下载到服务器本地.
![小绿锁](https://ws1.sinaimg.cn/large/692869a3gy1fecyz408dqj20yc0ga431.jpg)
至此,整个网站的https配置完成.

# 5 问题和优化
现在还有问题没有解决:
+ 加密算法的问题,我的服务器配置chacha20算法不成功,只要客户端协商的算法中有chacha20,服务器就会返回`bad record mac`错误,现在我只能把它禁用
+ 证书的自动更新问题,由于我后期还配置了其他的东西,所以证书自动更新一直不成功,需要我手动去更新
+ 将域名提交到https://hstspreload.org/,具体原理请参考[合理使用](https://imququ.com/post/sth-about-switch-to-https.html#toc-2)

# 参考文献
+ [关于启用 HTTPS 的一些经验分享（二）](https://imququ.com/post/sth-about-switch-to-https-2.html)
+ [本博客 Nginx 配置之完整篇](https://imququ.com/post/my-nginx-conf.html)
+ [关于启用 HTTPS 的一些经验分享（一）](https://imququ.com/post/sth-about-switch-to-https.html#toc-2)