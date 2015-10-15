title: php学习笔记--htmlentities和htmlspecialchars的区别
date: 2015-09-29 20:00:25
tags:
- php
categories:
- php学习笔记
---
开始看php手册了,今天看到htmlentities和htmlspecialchars这两个函数,刚好研究一下
<!-- more -->
+ htmlentities
+ htmlspecialchars

php4和php5支持,`htmlentities`会将所有适用的字符串转换为html实体,和`htmlspecialchars`很像,
**区别**就是:所有有**html实体**的字符,都会被它转化
如果想解码使用, `html_entity_decode`来解码

##参数
```
string htmlentities ( string $string [, int $flags = ENT_COMPAT | ENT_HTML401 [, string $encoding = ini_get("default_charset") [, bool $double_encode = true ]]] )
```
```
string htmlspecialchars ( string $string [, int $flags = ENT_COMPAT | ENT_HTML401 [, string $encoding = ini_get("default_charset") [, bool $double_encode = true ]]] )
```
**string**:输入字符串
**flags**:这些标志用来指定如何,处理引号,无效代码单元序列和使用的文件类型,默认是`ENT_COMPAT | ENT_HTML401`

