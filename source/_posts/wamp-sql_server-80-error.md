title: 'wamp与sql_server_因为端口80冲突,无法开启wamp'
date: 2014-11-14 18:09
tags:
- sql server
- wamp
categories:
- 软件安装与技巧
---

因为80端口占用,导致无法启动,其实不必关闭sql server太多的东西,只需要关闭SQL Server Reporting Services 就行了,这样也不会影响sql server的使用

如图
![](https://img.blog.csdn.net/20141114175909339?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSmFja3JveWFs/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)




打开sqlserver 配置管理器,关闭以上服务

或者打开控制面板->管理->服务工具->SQL Server Reporting Services

然后就可以打开wamp了
