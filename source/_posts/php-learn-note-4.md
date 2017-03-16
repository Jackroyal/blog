title: php学习笔记(4)--php使用mysqli连接数据库
date: 2016-03-12 10:18:31
tags:
- php
categories:
- php学习笔记
---
long time no see,用上框架以后,好久没有写原生的sql处理了,想当年用的还是mysql_connect,如今,都已经换mysqli了.
<!-- more -->
# 1 关于php的数据库连接方式
当考虑连接到MySQL数据库服务器的时候，有三种主要的API可供选择：
1 PHP的MySQL扩展
2 PHP的mysqli扩展
3 PHP数据对象(PDO)
## 1.1 什么是PHP的MySQL扩展?
这是设计开发允许PHP应用与MySQL数据库交互的早期扩展。mysql扩展提供了一个面向过程 的接口，并且是针对MySQL4.1.3或更早版本设计的。因此，这个扩展虽然可以与MySQL4.1.3或更新的数据库服务端 进行交互，但并不支持后期MySQL服务端提供的一些特性。
## 1.2 什么是PHP的mysqli扩展?
mysqli扩展，我们有时称之为MySQL增强扩展，可以用于使用MySQL4.1.3或更新版本中新的高级特性。mysqli扩展在PHP 5及以后版本中包含。
1   mysqli扩展有一系列的优势，相对于mysql扩展的提升主要有：
2   面向对象接口
3   prepared语句支持（译注：关于prepare请参阅mysql相关文档）
4   多语句执行支持
5   事务支持
6   增强的调试能力
7   嵌入式服务支持
在提供了面向对象接口的同时也提供了一个面向过程的接口。
## 1.3 什么是PDO?

PHP数据对象，是PHP应用中的一个数据库抽象层规范。PDO提供了一个统一的API接口可以使得你的PHP应用不去关心具体要 连接的数据库服务器系统类型。也就是说，如果你使用PDO的API，可以在任何需要的时候无缝切换数据库服务器，比如从Firebird 到MySQL，仅仅需要修改很少的PHP代码。

其他数据库抽象层的例子包括Java应用中的JDBC以及Perl中的DBI。

当然，PDO也有它自己的先进性，比如一个干净的，简单的，可移植的API，它最主要的缺点是会限制让你不能使用 后期MySQL服务端提供所有的数据库高级特性。比如，PDO不允许使用MySQL支持的多语句执行。



![PHP中三种主要的MySQL连接方式的功能的比较](https://ww2.sinaimg.cn/large/692869a3gw1f1tvea7khyj20hm0dhac0.jpg)
