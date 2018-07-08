title: ubuntu下设置apache的目录映射
date: 2015-10-15 22:12:37
tags:
- ubuntu
- apache
categories:
- 服务器
---
好吧,我的博客一不小心又荒废下来了,话不多说,进入正文.
<!-- more -->
最近的项目一直在忙,发布代码的时候遇到一个麻烦事,由于所有项目代码都放在服务器的根目录,也就是`/var/www/html`下;然后还有一个api的页面,我放在一个`api`文件夹中,然后把整个`api`文件夹放在`html`目录下.由于每次发布代码都是替换整个根目录`html`下的文件,你懂的,我每次发布项目代码后,`api`就没了,需要我单独解压,再复制,blablabla,总之一个字,**  烦  **.
我突然想起来,phpmyadmin安装后就不在网站的根目录,但是访问的时候我却还是直接在ip后面跟目录,比如`http://127.0.0.1/phpmyadmin`,这货应该是用了一个映射,我照着这个样子搞一个,不就行了,说干就干.

# 1 使用的命令
`Alias` 这个是这次主要使用的命令,使用的方法很简单,如下所示
```shell
Alias /api /home/ch/api
```
意思就是如果你在ip后面添加`/api`后缀,也就是你在浏览器访问`http://127.0.0.1/api`,那么实际就会访问到`/home/ch/api`目录下
# 2 使用方法
### Linux下
由于我用的是ubuntu,所以具体说下ubuntu下怎么用.
根据[在Ubuntu上安装LAMP服务器并简单配置](http://huirong.github.io/2015/04/01/installLamp/),这篇文章中关于`phpmyadmin`的设置部分,以下为摘抄
```
配置Apache服务器
如果此时在浏览器中输入http://localhost/phpmyadmin，会提示404错误
此时应该进行简单的配置，将phpMyAdmin的配置文件，复制到Apache2下

cp /etc/phpmyadmin/apache.conf /etc/apache2/conf-enabled/phpmyadmin.conf
然后重启Apache服务器

service apache2 restart

```
我们得到启发,本来phpmyadmin也是不在`html`根目录下的,上面的操作做了什么呢?
我们打开`/etc/apache2/conf-enabled/phpmyadmin.conf`这个文件看看
```
# phpMyAdmin default Apache configuration

Alias /phpmyadmin /usr/share/phpmyadmin

<Directory /usr/share/phpmyadmin>
    Options FollowSymLinks
    DirectoryIndex index.php

    <IfModule mod_php5.c>
        AddType application/x-httpd-php .php

        php_flag magic_quotes_gpc Off
        php_flag track_vars On
        php_flag register_globals Off
        php_admin_flag allow_url_fopen Off
        php_value include_path .
        php_admin_value upload_tmp_dir /var/lib/phpmyadmin/tmp
        php_admin_value open_basedir /usr/share/phpmyadmin/:/etc/phpmyadmin/:/var/lib/phpmyadmin/:/usr/share/php/php-gettext/:/usr/share/javascript/
    </IfModule>

</Directory>

# Authorize for setup
<Directory /usr/share/phpmyadmin/setup>
    <IfModule mod_authn_file.c>
    AuthType Basic
    AuthName "phpMyAdmin Setup"
    AuthUserFile /etc/phpmyadmin/htpasswd.setup
    </IfModule>
    Require valid-user
</Directory>

# Disallow web access to directories that don't need it
<Directory /usr/share/phpmyadmin/libraries>
    Order Deny,Allow
    Deny from All
</Directory>
<Directory /usr/share/phpmyadmin/setup/lib>
    Order Deny,Allow
    Deny from All
</Directory>


```
看看这个文件的第一行,soga,原来他用的也是`Alias`命令,接下来我们继续往下走,下面的代码是什么意思呢?下面的代码是用来设置文件目录的权限的,就是`<Directory>`标签里面的内容.
关于这里的设置,我们可以参考[apache官网](https://httpd.apache.org/docs/2.2/mod/mod_authz_host.html# order)上的描述.简而言之
`Order Deny,Allow`其实是Allow优先,举个例子
```
Order Deny,Allow
Allow from All
Deny from All
```
由于`Deny`规则在`Allow`之前(Order规定的),所以先Deny所有的请求,然后`Allow`规则在其后,最后的结果就是所有的人都可以随意访问.假设你要对访问进行限制,建议按照下面这样设置
```
Order Deny,Allow
Allow from 192.168.1.122
Deny from All
```
也就是指允许ip为`192.168.1.122`的用户去访问,其他的用户都会被`Deny`
# 3 最后的结果
结合上面的分析,给出我最后的结果
```
Alias /api /var/www/api
<Directory /var/www/api>
 Options FollowSymLinks Includes -Indexes

  AllowOverride None
  Order allow,deny
  allow from all
</Directory>

```
这里的`-Indexes`选项的意思是,不要列出目录
打完收工
