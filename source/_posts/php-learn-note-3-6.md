title: php学习笔记(3.6)--PHP中的引用
date: 2018-07-08 17:40:28
updated: 2018-07-08 19:40:28
tags:
- php
categories:
- php学习笔记
---
最近再看CI源码, 刚好有碰到引用相关问题, 索性又研究了下.

# 1 什么是引用
引用,第一感觉就是想到C语言中的指针,两个变量指向同一个内存地址.但是php中的引用其实应该是别名, 可以类比的是linux系统中的硬链接,两个文件名实际指向同一个文件.

# 2 引用传值
我们在函数传参的时候可能会用到引用.在不同给的php版本中,写法也不同.
## 2.1 例如,在php5.2中,我们可能用到以下写法.
```php
function foo($var) {
  $var++;
}
$a = 0;
foo(&$a);
echo '$a:'.$a."<br>";//输出$a:1
```
但是在php5.3+中就不推荐这种写法了5.3会报warning,在更高版本中,会报fatal错误`Call-time pass-by-reference has been removed`.
## 2.2 推荐的写法是,在定义函数的时候如果是引用,在参数声明处直接标记为引用.
```php
//在定义函数时,直接声明参数为引用
function foo(&$var) {
  $var++;
}
$a = 0;
foo($a);
echo '$a:'.$a."<br>";//输出$a:1
```
# 3 引用返回
除了引用传值,还有一种常见的情况是引用返回.
```php
function &foo(&$var) {
  $var++;
  return $var;
 
}
$a = 0;
$b=&foo($a);
$b=4;
echo '$a:'.$a."<br>";//输出$a:4
echo '$b:'.$b."<br>";//输出$b:4
```
此处需要注意的是,函数定义处需要显示声明`function &foo`,在调用函数的时候也需要显示声明`$b=&foo($a)`.这个跟上面讲到的引用传值不同.
那么如果我调用函数的时候如果不加`&`调用,会怎么样呢?
```php
function &foo(&$var) {
  $var++;
  return $var;
 
}
$a = 0;
$b=foo($a);
$b=4;
echo '$a:'.$a."<br>";//输出$a:1
echo '$b:'.$b."<br>";//输出$b:4
```
上面例子中的引用传值只是为了突显这种差别. 也可以用对象来表现这个差别.
```php
class foo {
    public $value = 42;
    public function &getValue() {
        return $this->value;
    }
}

$obj = new foo;

$myValue = &$obj->getValue();
$obj->value = 2;
echo $myValue; //输出2
```
上面`public function &getValue() {`或`$myValue = &$obj->getValue();`中的`&`,无论少了谁,都不是引用返回,会导致最后输出`$myValue=42`

# 4 对象的赋值和传值
在php5中,对象的赋值和参数传递并不是引用传递,实际传递的是对象标识符的拷贝.
上面已经讲到php中的引用其实是别名,对于对象而言,php5中对象变量并不直接指向对象内容,而是保存了一个对象标识符,通过标识符来访问对象的内容,在将对象变量赋值的时候,其实是将两个变量指向同一个标识符,下面看例子.
```php
class A {
    public $foo = 1;
}  

$a = new A;
$b = $a;     // $a ,$b都是同一个标识符的拷贝
             // ($a) = ($b) = <id>

             $c = new A;
$d = &$c;    // $c ,$d是引用
             // ($c,$d) = <id>
```
上面的例子比较清晰表明了2者的区别,总结下:
对于普通变量而言:
```php
$a = 1;
$b = &$a;
```
变量$a直接指向内容,当产生引用(即别名),那么`$a`和`$b`都指向同一个内容;
对于对象而言,中间隔着标识符:
```php
class A {
    public $foo = 1;
}
$a = new A;
$b = &$a;    // $a ,$b是引用
$c = $a;     // $c和$a保存的是同一个标识符,保存在不同的地方
```
通俗的讲,我们可以把标识符理解为门牌号,$a和$c中保存的是同样的门牌号,但是并不是记录在同一个地方,是相同内容的拷贝,他们都可以找到同一个人;而$a和$b只是别名的关系,它俩实际指向同一个,记录在同一个地方.
>$a的小本本记录门牌号A1234
>$b的小本本(实际和$a是同一个小本本,彼此互为别名)记录门牌号A1234
>$c的小本本记录门牌号A1234

一图胜千言
```
  $b ---->+      
                |
  $a ---->+
                |  
                v   
          +--------+      
          | object id   |--------+ 
          +--------+               |
                                          v
          +--------+      +--------------+
  $c -->| object id  | ---> | object(Class A) |
          +--------+      +--------------+
 
 ```

# 参考文献
1. [php引用传递](http://php.net/manual/zh/language.references.pass.php)
1. [对象和引用](http://php.net/manual/zh/language.oop5.references.php)
1. [在PHP中对象真的是按引用传递的吗](https://segmentfault.com/a/1190000002928594)





