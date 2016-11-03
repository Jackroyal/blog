title: php中的bool值和类型转换
date: 2015-07-20 14:23:21
tags:
- php
- bool
categories:
- php
---
最近在写php,发现有个问题一直无法避免,那就是关于php的bool值和类型转换.
以前每次遇到要校验一个变量`$a`,是否为空,都是使用`$a==''`去判断,感觉不是很懂,反正一直这样用,也没出太多纰漏,而没有理会什么NUll,'',0之类的其他值.今天决定研究下,把它彻底搞清楚.
<!-- more -->
# 1 php的数据类型
php支持原始的8总数据类型.
4总标量类型:
- boolean
- integer
- float(也称作double类型)
- string
2种复合类型
- array(数组)
- object(对象)
2种特殊类型
- resource(资源)
- NULL(无类型)
实际上 double 和 float 是相同的，由于一些历史的原因，这两个名称同时存在。
变量的类型通常不是由程序员设定的，确切地说，是由PHP根据该变量使用的上下文在运行时决定的。
前面几种类型很好理解,现在重点看一下resource类型和NULL类型
1. `resource` 是一种特殊变量，保存了到外部资源的一个引用。资源是通过专门的函数来建立和使用的。由于资源类型变量保存有为打开文件、数据库连接、图形画布区域等的特殊句柄，因此将其它类型的值转换为资源没有意义。
2. `NULL`,特殊的NULL值表示一个变量没有值。NULL类型唯一可能的值就是 NULL。在下列情况下一个变量被认为是NULL：
- 被赋值为 NULL。
- 尚未被赋值。
- 被 unset()。

# 2 常用的比较函数
### empty()
这个函数最近经常见到,它比较有特点.
如果 var 是非空或非零的值，则empty()返回`FALSE`。
换句话说，""、0、"0"、NULL、FALSE、array()、var $var以及没有任何属性的对象都将
被认为是空的，返回 TRUE。
如果变量没有赋值,empty()也不会报错.

### is_set()
这个函数和is_null()刚好相反,如果一个函数的is_null()返回`true`,那么它的is_set()则必定返回相反值,`false`.

### is_null()
所有的`is_`开头的函数,都不会进行类型转换,例如is_bool('0'),返回false,因为'0'不是一个布尔类型的值.
详细对比看下图
![使用 PHP 函数对变量 $x 进行比较](http://ww4.sinaimg.cn/large/692869a3gw1euak04nwitj20nf0i2432.jpg)


更多更详细的比较,大家看这里[php类型比较表](http://php.net/manual/zh/types.comparisons.php)
