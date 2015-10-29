title: ubuntu 14.04配置thinkphp路由,隐藏index.php
date: 2015-06-29 20:16:17
tags:
- ubuntu
- thinkphp
categories:
- Linux
---
今天把代码发布到服务器上去,发现服务器的rewrite有问题,无法实现隐藏index.php的功能.
<!-- more -->
# 环境
服务器:ubuntu 14.04 lts
apache:2.4.7
php:5.5.9-
mysql:5.5.43

# 开启rewrite模块
在ubuntu中,开启很简单,执行以下bash命令即可
```bash
sudo a2enmod rewrite
```
# 添加.htaccess支持
默认apache会忽视所有的规则重写,即使添加了`.htaccess`文件,他不认.
在ubuntu 14.04中设置,跟其他版本的ubuntu有点不同
核心操作还是修改 `AllowOverride None`为` AllowOverride All`.
问题是这个文件到底在哪儿.
在网上找的教程中,有的说是在`/etc/apache2/sites-available/000-default.conf`中,在我这个版本的apache中,它不在这里.
他在`/etc/apache2/apache2.conf`中.
我们打开这个文件,大概在166行.
```bash
<Directory /var/www/>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
</Directory>
```
# 重启apache服务器
ok,都改完了,就剩一步,重启apache服务器.
```bash
sudo service apache2 restart
```
搞定!
![热疯了,空调房都热](http://ww3.sinaimg.cn/large/692869a3gw1etl9v3c6wzj20go09dmx6.jpg)






[](http://www.dev-metal.com/enable-mod_rewrite-ubuntu-14-04-lts/)
