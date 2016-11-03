title: php学习笔记(1)
date: 2015-12-10 15:46:03
tags:
- php
categories:
- php学习笔记
---
php文档学习笔记,正式开始连载了哈,终于开始学习了.
<!-- more -->
# 布尔值
要明确地将一个值转换成 boolean，用 (bool) 或者 (boolean) 来强制转换。但是很多情况下不需要用强制转换，因为当运算符，函数或者流程控制结构需要一个 boolean 参数时，该值会被自动转换。
当转换为 boolean 时，以下值被认为是 FALSE：
+ 布尔值 **FALSE** 本身
+ 整型值 **0**（零）
+ 浮点型值 **0.0**（零）
+ 空字符串，以及字符串 **"0"**
+ 不包括任何元素的数组
+ 不包括任何成员变量的对象（仅 PHP 4.0 适用）
+ 特殊类型 **NULL**（包括尚未赋值的变量）
+ 从空标记生成的 **SimpleXML** 对象
+ 所有其它值都被认为是 **TRUE**（包括任何资源）。

**PS:**
-1 和其它非零值（不论正负）一样，被认为是 **TRUE**！
接下来我们可以试试,自己写一些常见的布尔值
```php
var_dump((bool)'0');        //false
var_dump((bool)'0.0');      //true
var_dump((bool)'0.1');      //true
var_dump((bool)array());    //false
var_dump((bool)"false");    //true,因为这个字符串不为空,所以为true
```
