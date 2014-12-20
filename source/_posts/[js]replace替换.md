title: '[js]replace替换'
date: 2014-11-25 22:32
tags:
- javascript
- 正则表达式
- 前端
- js
categories:
- javascript
---

今天做前端试题,用到了replace函数,来记录一下.
(以下部分知识点来自[http://www.w3cschool.cn/jsref_replace.html](http://www.w3cschool.cn/jsref_replace.html))


##定义和用法




replace() 方法用于在字符串中用一些字符替换另一些字符，或替换一个与正则表达式匹配的子串。

###语法



<table class="dataintable " style="margin-top:10px; border-collapse:collapse; border:1px solid rgb(136,136,136); width:606px; color:rgb(0,0,0); font-size:12px; background-color:rgb(249,249,249)">
<tbody>
<tr>
<th style="vertical-align:baseline; padding:5px 15px 5px 5px; border:1px solid rgb(136,136,136); background-color:rgb(204,204,204)">
参数</th>
<th style="vertical-align:baseline; padding:5px 15px 5px 5px; border:1px solid rgb(136,136,136); background-color:rgb(204,204,204)">
描述</th>
</tr>
<tr>
<td style="vertical-align:text-top; padding:5px 15px 5px 5px; border:1px solid rgb(170,170,170); background-color:rgb(239,239,239)">
regexp/substr</td>
<td style="vertical-align:text-top; padding:5px 15px 5px 5px; border:1px solid rgb(170,170,170); background-color:rgb(239,239,239)">
<p style="margin-top:0px; margin-bottom:0px; line-height:18px">必需。规定子字符串或要替换的模式的 RegExp 对象。</p>
<p style="margin-top:0px; margin-bottom:2px; line-height:18px">请注意，如果该值是一个字符串，则将它作为要检索的直接量文本模式，而不是首先被转换为 RegExp 对象。</p>
</td>
</tr>
<tr>
<td style="vertical-align:text-top; padding:5px 15px 5px 5px; border:1px solid rgb(170,170,170); background-color:rgb(239,239,239)">
replacement</td>
<td style="vertical-align:text-top; padding:5px 15px 5px 5px; border:1px solid rgb(170,170,170); background-color:rgb(239,239,239)">
必需。一个字符串值。规定了替换文本或生成替换文本的函数。</td>
</tr>
</tbody>
</table>

###返回值


一个新的字符串，是用 _replacement_ 替换了 regexp 的第一次匹配或所有匹配之后得到的。

###说明


字符串 stringObject 的 replace() 方法执行的是查找并替换的操作。它将在 stringObject 中查找与 regexp 相匹配的子字符串，然后用 _replacement_ 来替换这些子串。如果 regexp 具有全局标志 g，那么 replace() 方法将替换所有匹配的子串。否则，它只替换第一个匹配子串。

_replacement_ 可以是字符串，也可以是函数。如果它是字符串，那么每个匹配都将由字符串替换。但是 replacement 中的 $ 字符具有特定的含义。如下表所示，它说明从模式匹配得到的字符串将用于替换。
<table class="dataintable " style="margin-top:10px; border-collapse:collapse; border:1px solid rgb(136,136,136); width:606px; color:rgb(0,0,0); font-size:12px; background-color:rgb(249,249,249)">
<tbody>
<tr>
<th style="vertical-align:baseline; padding:5px 15px 5px 5px; border:1px solid rgb(136,136,136); background-color:rgb(204,204,204)">
字符</th>
<th style="vertical-align:baseline; padding:5px 15px 5px 5px; border:1px solid rgb(136,136,136); background-color:rgb(204,204,204)">
替换文本</th>
</tr>
<tr>
<td style="vertical-align:text-top; padding:5px 15px 5px 5px; border:1px solid rgb(170,170,170); background-color:rgb(239,239,239)">
$1、$2、...、$99</td>
<td style="vertical-align:text-top; padding:5px 15px 5px 5px; border:1px solid rgb(170,170,170); background-color:rgb(239,239,239)">
与 regexp 中的第 1 到第 99 个子表达式相匹配的文本。</td>
</tr>
<tr>
<td style="vertical-align:text-top; padding:5px 15px 5px 5px; border:1px solid rgb(170,170,170); background-color:rgb(239,239,239)">
$&</td>
<td style="vertical-align:text-top; padding:5px 15px 5px 5px; border:1px solid rgb(170,170,170); background-color:rgb(239,239,239)">
与 regexp 相匹配的子串。</td>
</tr>
<tr>
<td style="vertical-align:text-top; padding:5px 15px 5px 5px; border:1px solid rgb(170,170,170); background-color:rgb(239,239,239)">
$`</td>
<td style="vertical-align:text-top; padding:5px 15px 5px 5px; border:1px solid rgb(170,170,170); background-color:rgb(239,239,239)">
位于匹配子串左侧的文本。</td>
</tr>
<tr>
<td style="vertical-align:text-top; padding:5px 15px 5px 5px; border:1px solid rgb(170,170,170); background-color:rgb(239,239,239)">
$'</td>
<td style="vertical-align:text-top; padding:5px 15px 5px 5px; border:1px solid rgb(170,170,170); background-color:rgb(239,239,239)">
位于匹配子串右侧的文本。</td>
</tr>
<tr>
<td style="vertical-align:text-top; padding:5px 15px 5px 5px; border:1px solid rgb(170,170,170); background-color:rgb(239,239,239)">
$$</td>
<td style="vertical-align:text-top; padding:5px 15px 5px 5px; border:1px solid rgb(170,170,170); background-color:rgb(239,239,239)">
直接量符号。</td>
</tr>
</tbody>
</table>

__注意：__ECMAScript v3 规定，replace() 方法的参数 replacement 可以是函数而不是字符串。在这种情况下，每个匹配都调用该函数，它返回的字符串将作为替换文本使用。该函数的第一个参数是匹配模式的字符串。接下来的参数是与模式中的子表达式匹配的字符串，可以有 0 个或多个这样的参数。接下来的参数是一个整数，声明了匹配在 stringObject 中出现的位置。最后一个参数是
 stringObject 本身。



下面给几个简单的例子来说明一下

ps:介绍一下正则的小知识,\w表示匹配一个英文字符,+表示匹配数量必须大于一个,\b就是分节符,单词中间分隔用的,连在一起就是取出前面字符串中所有的单词


```javascript
'my name is'.replace(/(\w+)\b/g,'haha  ');//输出结果为   "haha   haha   haha  "
```

```javascript
'my name is'.replace(/(\w+)\b/g,'$1aa');//输出结果为"myaa nameaa isaa"
```

```javascript
'my name is'.replace(/((\w)(\w+?))\b/g,function(m,a,b,c,d,e){return b.toUpperCase()+c;});//输出结果为"My Name Is"
```

重点讲解一下第三个,replace返回值为函数的例子(function必须要return)

当replace返回值为函数的时候

如果只有一个分组的话,他的参数是funciton(match,pos,originalText),分别表示模式的匹配项,模式匹配项在字符串中的位置和原始字符串

如果有多个分组的话,他的参数是funciton(match,arr1,arr2,arr3,....arrn,pos,originalText),分别表示模式的匹配项,模式匹配项,还有n个匹配的分组,最后的两个参数是在字符串中的位置和原始字符串

比如下面的例子,执行的结果就是

'my name is imweba '.replace(/((\w)(\w+?))\b/g,function(m,a,b,c,d,e){

console.log(m);

console.log(a);

console.log(b);

console.log(c);

console.log(d);

console.log(e);

});
执行结果



my//模式匹配的值,因为上面匹配了整个单词,虽然分了多个组

my//第一个分组的值

m//第二个分组的值

y//第三个分组的值

0//模式匹配在字符串中的起始位置

my name is imweba  //原始字符串

name

name

n

ame

3

my name is imweba 

is

is

i

s

8

my name is imweba 

imweba

imweba

i

mweba

11

my name is imweba 

"undefined undefined undefined undefined "//这一行是因为function必须要有return,我没写return,所以就都是undefined









ok  打完收工






