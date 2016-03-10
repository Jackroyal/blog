title: php学习笔记(2)
date: 2016-02-25 14:36:20
tags:
- php
categories:
- php学习笔记
---
今天看的是最基础的变量.
<!-- more -->
# 1 php变量
和其他语言类似,php中一个有效的变量名由字母或者下划线开头,后面跟上任意数量的字母,数字,或者下划线.按照正常的正则表达式,它将被表述为：'[a-zA-Z_\x7f-\xff][a-zA-Z0-9_\x7f-\xff]*'.
赋值的时候,变量默认总是`传值赋值`.
在php中也可以用`引用赋值`,也就是改动新的变量也会因想到原始变量,反之亦然.
```php
<?php
$foo = 'Bob';              // 将 'Bob' 赋给 $foo
$bar = &$foo;              // 通过 $bar 引用 $foo
$bar = "My name is $bar";  // 修改 $bar 变量
echo $bar;
echo $foo;                 // $foo 的值也被修改
?>
```
ps:只有有名字的变量才可以使用引用赋值,也就是无法对一个表达式使用引用赋值.
使用`isset()`来检测变量是否被初始化.

# 2 预定义变量
php会自动用下划线来替换所有的传入变量中的`.`号,例子如下所示
```php
<?php
example.com/page.php?chuck.norris=nevercries

you can not reference them by the name used in the URI:
//INCORRECT
echo $_GET['chuck.norris'];

instead you must use:
//CORRECT
echo $_GET['chuck_norris'];
?>
```

# 3 变量范围
变量的范围即它定义的上下文背景（也就是它的生效范围）.大部分的 PHP 变量只有一个单独的范围.这个单独的范围跨度同样包含了 include 和 require 引入的文件.例如：
```php
<?php
$a = 1;
include 'b.inc';
?>
```
这里变量 $a 将会在包含文件 b.inc 中生效.但是,在用户自定义函数中,一个局部函数范围将被引入.任何用于函数内部的变量按缺省情况将被限制在局部函数范围内.例如：
```php
<?php
$a = 1; /* global scope */

function Test()
{
    echo $a; /* reference to local scope variable */
}

Test();
?>
```
这个脚本不会有任何输出,因为 `echo`语句引用了一个局部版本的变量$a,而且在这个范围内,它并没有被赋值.你可能注意到 PHP 的全局变量和 C 语言有一点点不同,在 C 语言中,全局变量在函数中自动生效,除非被局部变量覆盖.这可能引起一些问题,有些人可能不小心就改变了一个全局变量.PHP 中全局变量在函数中使用时必须声明为 `global`.
PS:此处需要注意的是,php不存在类似C和java的块级作用域.比如你在循环中定义的一个变量,在循环外还是可以访问的
```php
<?php
for($j=0; $j<3; $j++)
{
     if($j == 1)
        $a = 4;
}
echo $a;//此处将会输出4
?>

```
## 3.2 静态变量
php还支持静态变量,静态变量`仅在局部函数域中`存在,但当程序执行离开此作用域时,其值并不丢失.
```php
<?php
function Test()
{
    $a = 0;
    echo $a;
    $a++;
}
?>
```

# 4 来自PHP之外的变量
此处主要指的是通过表单提交的变量,从cookie读取的变量

## 4.1 图片提交表单
除了常用的通过表单按钮提交表单,我们还可以使用图片的形式提交表单,代码如下:
```php
<input type="image" src="image.gif" name="sub" />
```
这样在后端接收的时候,还会受到一个叫做`sub_x`和`sub_y`的变量,它包含了用户点击图片的坐标.此处如果你不定义`name`为`sub`,那么后端接收变量就是`x`和`y`.
# 4.2 PHP自动转换变量名
当我们通过表单提交变量的时候,如果表单项的名称包含空格,点号,左中括号,php就会自动把他们转换成下划线.
```php
   <input type="text" name= "userName.a"/>
   <input type="text" name= "passWor[d"/>
   //输出[userName_a] => [passWor_d] => 
   //都被转化成下划线了
```
chr(32) ( ) (space)
chr(46) (.) (dot)
chr(91) ([) (open square bracket)

ps:ascll码值在128-159的字符是合法字符,所以并不会被转义成下划线
