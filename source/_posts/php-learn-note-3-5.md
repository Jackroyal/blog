title: php学习笔记(3.5)--关于引用赋值和一般赋值
date: 2016-03-05 13:52:28
tags:
- php
categories:
- php学习笔记
---
上一篇提到了,对象赋值的时候有两种赋值方式,一种是直接`$a=$b`,第二种是引用赋值`$a=&$b`.
不仅对象赋值的时候会有这两种情况,普通变量赋值也会有这两种情况,为了简单说明,我们先使用简单的普通变量来做说明.
<!-- more -->
# 1 普通变量的结构
在PHP中,所有的变量都是用一个结构zval来保存的,zval大概的结构如下所示:
```c
struct _zval_struct {
        /* Variable information */
        zvalue_value value;             /* value */
        zend_uint refcount;
        zend_uchar type;        /* active type */
        zend_uchar is_ref;
};
```
我们这里主要用到的是两个变量,一个是refcount,即是引用计数,一个是is_ref,用来标记是否是引用.

# 2 php环境的设置
我们在实验过程中,需要去查看refcount和is_ref的变化,所以需要使用php的
`xdebug_debug_zval`函数,你的环境可能默认没有开启这个配置,你需要修改`php.ini`文件开启这个配置.
![修改php.ini文件](https://ww4.sinaimg.cn/large/692869a3gw1f1m1ximwj1j20wn098dj6.jpg)
# 3 refcount和is_ref
我们通常的赋值方式:一般赋值和引用赋值.
# 3.1 一般赋值
当使用一般给新变量赋值的时候,php的内存策略,并不是重新申请一个新的空间,而是把原来的那个recount+1.比如下面的例子:
```php
$a='aaa';//此时$a的recount=1,ref=0
$b=$a;//赋值过后,$a的recount=2,ref=0,$b因为指向和$a一样,所以结果和$a一样
xdebug_debug_zval('a','b');
```
输出结果如下
```
(refcount=2, is_ref=0),string 'aaa' (length=3)
```
# 3.2 引用赋值
```php
$a='aaa';//此时$a的recount=1,ref=0
$c=&$a;//赋值过后,$a的recount=2,ref=0,$b因为指向和$a一样,所以结果和$a一样
xdebug_debug_zval('a','b');//
```
输出结果如下
```
(refcount=2, is_ref=1),string 'aaa' (length=3)
```
由上面可知,也就是每次进行赋值的时候,refcount都会+1,如果是引用赋值,is_ref就会被置为一.
# 3.3 两种赋值方式混合
那如果我们混合上面的两种赋值方式呢?比如下面的代码
```php
$a='aaa';
$b=&$a;
$c=$a;
xdebug_debug_zval('a','b','c');
```
你猜输出结果是什么?
```
a: (refcount=2, is_ref=1),string 'aaa' (length=3)

b: (refcount=2, is_ref=1),string 'aaa' (length=3)

c: (refcount=1, is_ref=0),string 'aaa' (length=3)
```
为什么会这样呢?
我们一行一行的分析.
1. 首先第一行代码执行完以后,$a的结果应该是`(refcount=1, is_ref=0),string 'aaa' (length=3)`
2. 第二行代码执行引用赋值,因为refcount=1,所以$a的refcount直接+1,is_ref变成1,输出即是`(refcount=2, is_ref=1),string 'aaa' 
3. 第三行代码执行一般赋值,那我们是不是再把refcount+1呢?不是的,因为此时的refcount>1并且is_ref不为0,所以不能执行那个操作,而是直接把$a复制了另外一份,而不是修改$a的计数器.
所以,最后的输出结果,$c的refcount是1,因为它完全另外申请了一个副本

我们把上面的代码换个位置
```php
$a='aaa';
$c=$a;
$b=&$a;
xdebug_debug_zval('a','b','c');
```
你再猜猜输出结果是什么?
```
a: (refcount=2, is_ref=1),string 'aaa' (length=3)

b: (refcount=2, is_ref=1),string 'aaa' (length=3)

c: (refcount=1, is_ref=0),string 'aaa' (length=3)
```
咦?结果是一样的哈?
对的,结果是一样的,不过执行的过程却是不同的,我们再来一行一行的分析下
1. 首先第一行代码执行完以后,$a的结果应该是`(refcount=1, is_ref=0),string 'aaa' (length=3)`
2. 第二行代码执行一般赋值,同理,因为refcount=1,所以$a的refcount直接+1,is_ref不变,输出即是`(refcount=2, is_ref=0),string 'aaa' 
3. 第三行代码执行引用赋值,跟上面一样,因为refcount=2了,所以不能直接进行计数器+1,我们先检查is_ref,发现是0.这时,php首先做一个分离操作,也就是把上面一般赋值的那个值,给分离出去,分离后,$a的refcount减去1,也就是$c独立了,那么此时$a和$c的输出结果应该是`(refcount=1, is_ref=0),string 'aaa' (length=3)`.在分离操作完成后,再做引用赋值的问题,这时候就简单了,因为$a的refcount是1,所以我们直接refcount+1,is_ref=1就搞定
4. 最后输出结果就是上面的结果了

**php把上面的过程叫做写时复制(copy on write),也就是说,一般赋值$c=$a时候,php并不是立即去申请一个新的变量空间.,而是等到执行引用赋值或者其他写操作的时候(比如这个时候修改$c的值),php才会去开辟一个新的空间给赋值后的变量,也就是分离操作.**

# 4 推广到对象
上面普通变量的赋值,应该说清楚了哈.
其实对象的赋值和普通变量赋值是同一个道理.下面直接贴代码,不在赘述
```php
class A
{
    public $aa='我是类属性';
}
$a=new A();
echo '刚执行完$a定义<br>';
xdebug_debug_zval('a');
$b=$a;
echo '刚执行完$b赋值<br>';
xdebug_debug_zval('b');
$c=&$a;
echo '刚执行完$c赋值<br>';
xdebug_debug_zval('c');
$d=$b;
echo '刚执行完$d赋值<br>';
xdebug_debug_zval('a','b','c','d');
```
输出结果如下
```
刚执行完$a定义
a: 
(refcount=1, is_ref=0),
object(A)[1]
  public 'aa' => (refcount=1, is_ref=0),string '我是类属性' (length=15)

刚执行完$b赋值
b: 
(refcount=2, is_ref=0),
object(A)[1]
  public 'aa' => (refcount=1, is_ref=0),string '我是类属性' (length=15)

刚执行完$c赋值
c: 
(refcount=2, is_ref=1),
object(A)[1]
  public 'aa' => (refcount=1, is_ref=0),string '我是类属性' (length=15)

刚执行完$d赋值
a: 
(refcount=2, is_ref=1),
object(A)[1]
  public 'aa' => (refcount=1, is_ref=0),string '我是类属性' (length=15)

b: 
(refcount=2, is_ref=0),
object(A)[1]
  public 'aa' => (refcount=1, is_ref=0),string '我是类属性' (length=15)

c: 
(refcount=2, is_ref=1),
object(A)[1]
  public 'aa' => (refcount=1, is_ref=0),string '我是类属性' (length=15)

d: 
(refcount=2, is_ref=0),
object(A)[1]
  public 'aa' => (refcount=1, is_ref=0),string '我是类属性' (length=15)


```

# 5 关于debug_zval_dump函数
php中还有个关于计数的函数叫做`debug_zval_dump`,它只返回refcount,不过这个函数好奇葩,输出的数据有时候跟想的都不一样.
比如:
```php
<?php
$a='aaa';
$b=$a;
debug_zval_dump($a);//输出3
?>
```
原因是:执行`debug_zval_dump`相当于执行了一次一般赋值操作,所以,refcount会再加1
再比如
```php
$a='aaa';
$c=$a;
$c='bbb';
debug_zval_dump($a,$c);//输出refcount=2,refcount=2
xdebug_debug_zval('a','c');//两个值完全相同,都是refcount=1, is_ref=0),string 'bbb' (length=3)
```
额,这是不是很奇葩?再来一波
```php
$a='aaa';
$b=&$a;
$c=$a;
debug_zval_dump($a,$b,$c);//refcount分别是1,1,2
xdebug_debug_zval('a','b','c');//refcount和is_ref分别是(2,1),(2,1),(1,0)
```
这是什么鬼?我表示都看不懂
稍微总结了下,`debug_zval_dump`这个函数,如果只是一般赋值,那么还比较正常,赋值一次就是refcount+1操作,最后调用`debug_zval_dump`的再+1就好了
但是一旦涉及到引用赋值,这货就全变成1了,(引用赋值不+1就算了,说好的调用`debug_zval_dump`的+1呢?被狗吃了?),完全搞不懂是在搞啥,还是`xdebug_debug_zval`函数好.
打完收工
ps:其实我在知乎提了个问题,不过没人回答还[debug_zval_dump的计数问题?](http://www.zhihu.com/question/41044486)


# 参考文献
1 [鸟哥的博客--深入理解PHP原理之变量分离/引用](http://www.laruence.com/2008/09/19/520.html)
2 [PHP扩展编写第二步：参数，数组，以及ZVAL「续」](http://weizhifeng.net/write-php-extension-part2-2.html)
3 [php官方文档--debug_zval_dump函数](http://php.net/manual/zh/function.debug-zval-dump.php)
4 [英文文档--php变量](https://derickrethans.nl/talks/phparch-php-variables-article.pdf)
