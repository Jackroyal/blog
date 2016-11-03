title: '[js]前端试题'
date: 2014-11-25 22:26
tags:
- javascript
- 前端
- 正则
categories:
- javascript
---

```javascript
// 1、获取页面id为 hello 的节点
var he=document.getElementById('hello');
// 2、获取页面所有 div 标签
var di=document.getElementsByTagName('div');
// 3、给 id 为hello的节点绑定click 事件，点击的时候弹出 "hello"
document.getElementById('hello').onclick=function(){alert('helo')};
// 4、将页面上 id为hello的节点隐藏
document.getElementById('hello').style.visibility=none;
// 5、将 "hello world" 用正则替换成 "hello javascript”
var str='hello world';
str.replace(/hello[\s]+world/g,'hello javascript');
// 6、给节点（假设为element）绑定click事件，当被点击时候，alert 弹出事件的类型（通过jquery）
$(element).click(function(event) {
    alert(event.type);
});
// 7、给一个节点添加一个类 "hello"
document.getElementById('kw').className=document.getElementById('kw').className+" hello";
// 8、给节点（假设为element）绑定click事件，当被点击时候，alert 弹出事件的类型（要求兼容IE、标准浏览器）
element.onclick=function(event){var e=event||window.event};
// 9、假设页面有这么两个节点将 id为world的节点的 内容设置成 跟 id 为 hello 的节点一样。
document.getElementById('world').innerHTML=document.getElementById('hello').innerHTML;
// 10、通过 js 将element的margin-left 设置为 20px
element.style.marginLeft="20px";



// 1.[{a:3},{a:4},{a:1},{a:2}] sort排序
function sortnum(a,b){
    return a.a-b.a;
}
//   [{a:3,b:5},{a:4,b:6},{a:1},{a:2,b:8}]按a的正向排序
function sortab(a,b){
    return a.a-b.a!=0?a.a-b.a:a.b-b.b;
}
//   sort 排序原理  使用的算法
// 2.my name is imweba ... => my6 name7 is8 imweba9 ...
//   用正则实现
'my name is imweba '.replace(/([\w]+)\b/g,'$1')
var attr='my name is imweba '.match(/([\w]+)\b/g);
for (var i = 0;i < attr.length; i++) {
    attr[i]=attr[i]+(6+i);
};
attr.join(' ');//感觉不对,只能做到这个程度

// 3.实现function test(str)(){},将str的每个单词的首字母大写（str为英文字符串）
function test(str){
    return str.replace(/((\w)(\w+?))\b/g,function(m,a,b,c,d,e){return b.toUpperCase()+c;});
}

// 4.写一个二分查找的算法   （假设这个数组不是有序的呢）
\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*
// 5.123+”+34  直接说结果   12334
12334
// 加法会将数字转化为字符串，减法和乘法等会将字符串转化为数字
// "4"\*"5"=20
// 6.实现function find(attr,val){},找到当前页面里具备属性为attr且值为val的节点，输出其父元素名称
function find(attr,val){
    var dom=document.getElementsByTagName('\*');
    for (var i = 0; i<dom.length; i++) {
        if(dom[i].getAttribute(attr)==val){
            return dom[i].parentNode.tagName;
        }
   }
}

```


