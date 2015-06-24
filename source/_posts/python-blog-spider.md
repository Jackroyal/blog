title: python学习笔记--python和beautifulsoup遇到的编码问题
date: 2015-03-29 14:43:45
tags:
- python
- 爬虫
categories:
- python学习笔记
---
在刚开始使用github pages的时候,我用python写了一个爬虫,计划是从csdn和cnblogs等博客网站上,把自己之前写的博客爬取下来,然后再转换成hexo用的markdown格式,样就可以直接添加到我的github pages.
>项目主页: https://github.com/Jackroyal/blog2markdown

最近刚好在学习python,刚好就把它给优化了一下,顺便做了个跨平台(哈哈,win和ubuntu都可以跑哈),昨天遇到很蛋疼的问题,一它给了我很多思路帮助我定位问题,.直搞到凌晨两点才弄好.
非常非常感谢[【总结】Python 2.x中常见字符编码和解码方面的错误及其解决办法](http://www.crifan.com/summary_python_2_x_common_string_encode_decode_error_reason_and_solution/)
作者做了一个很好的总结,帮助我们定位问题.
<!-- more -->

#1 编码类型
首先确定好你的编码类型,比如一般推荐用utf-8.当确定编码类型后,就要保持统一,不要又弄些GBK的编码在里面.
+ 1.1  编辑器编码
    * 我们有时候会犯一个错误,我在py文件的头部声明当前文件是按照utf-8来编码.但是文件实际保存的编却不是utf-8,这样也会导致乱码.建议使用可以查看当前文件编码的编辑器,比如sublime text或者notepad++ 或者pycharm.<br>在sublime下如图所示<br>![sublime显示当前文件编码](http://ww2.sinaimg.cn/large/692869a3gw1eqmn535qb7j208u028t8k.jpg)
    * 文件编码声明,我们要在py文件的头部添加一行`# -*- coding: utf-8 -*-`,表明我接下来要使用utf-8编码
+ 1.2  python解释器
    * 如果是Python的IDLE，如果你没修改defaultencoding，那么就使用默认的字符编码可以通过sys.getdefaultencoding()而获得，比如此处获得是：ascii<br>![win中python解释器编码](http://ww4.sinaimg.cn/large/692869a3gw1eqmnd8fgrjj20b205kabi.jpg)<br>![ubuntu终端解释器的编码](http://ww3.sinaimg.cn/large/692869a3gw1eqmnfd29u7j20k5047wfv.jpg)
+ 1.3  执行python代码
    * 其中，很常见的几种动作是：
        * 打印print对应的所获得的字符
            * 对于字符串打印,Python的逻辑:
                * 如果是Unicode字符串,则可以,自动地,编码为对应的终端所用编码,然后正确的显示出来
                * 比如unicode的字符串,输出到windows的默认编码为GBK的cmd中,则Python可以自动将Unicode编码为GBK,然后输出到cmd中
                * 个别特殊情况,也会出错:
                    * 当此unicode字符串中包含某特殊字符,而目标终端的编码集合中,没有此字符,则很明显也是无法实现将Unicode编码为对应的特定编码的字符串,无法正确显示的
                * 如果是某种编码类型的str,则需要该str的编码类型,和目标终端编码匹配
                    * 比如GBK的字符串,输出到windows的默认编码为GBK的cmd,则是可以正常输出的
                    * 此处后来经过代码测试，就发现一个有趣或者说诡异的问题，虽然我们python文件声明的UTF-8编码，但是实际上实际上是用GBK编码，而此时，文件中的字符串，很明显是用GBK存储的，所以，将此GBK字符，输出到GBK的cmd中，是可以正常输出的。即，此处字符串的类型，很明显只和文件所用的实际编码有关，而和文件所声明的代码无关。
                * 如果是UTF-8的字符串,输出到windows的默认编码为GBK的cmd,就会出错
                    * 对相应的字符，进行编码（为某种特定类型的字符str），或解码（为对应的unicode类型的字符）
                    * 比如将当前的某种编码的字符串，解码为Unicode字符串
                        * 很明显，也是要保证，你字符串本身的编码和所指定的编码，两者之间要一致的
                        * 比如：decodedUnicode = someUtf8Str.decode("UTF-8")
                        * 而如果用这样的：decodedUnicode = someGbkStr.decode("UTF-8")，那就会出现错误

#2 常用方法
###2.1 encode和decode
encode()  unicode编码->其他编码

decode()  其他编码->unicode编码

使用这两个方法的前提是,你要知道当前是什么编码.然后用对应的编码去进行解码
比如对于s字符串可以用
```
s.encode('utf-8') #将s由unicode转码成utf-8
s.decode('GBK') #s是GBK编码,将s转换成unicode
```
###2.2 isinstance()
```
isinstance(s , unicode) #检测s是否是unicode编码

isinstance(s , str) #检测s是否是str格式
```


#3 beautifulsoup编码问题

###Beautiful Soup 会按顺序尝试不同的编码将你的文档转换为Unicode：
+   可以通过from_encoding参数传递编码类型给soup的构造器
+   通过文档本身找到编码类型：例如XML的声明或者HTML文档http-equiv的META标签。 
+   如果Beautiful Soup在文档中发现编码类型，它试着使用找到的类型转换文档。 +
+   但是，如果你明显的指定一个编码类型， 
+   并且成功使用了编码：这时它会忽略任何它在文档中发现的编码类型。
+   通过嗅探文件开头的一下数据，判断编码。如果编码类型可以被检测到，
+   它将是这些中的一个：UTF-*编码，EBCDIC或者ASCII。
+   通过chardet库,嗅探编码，如果你安装了这个库。
+   UTF-8
+   Windows-1252

一般来说,bs的自动识别,是不会有问题的,但是在我这里除了问题,具体原因不太清楚
我的网页上已经声明了是`utf-8`编码
原来代码如下
```
#这是原来的编码,在win下面乱码
self.soup = bs((response.read()))
print self.soup.originalEncoding   #此处结果竟然返回Windows-1252
```
修改后代码如下
```
#win下乱码的关键在这里,beautifulsoup解析的编码不对,我们这里直接指定编码
self.soup = bs((response.read()), from_encoding='utf-8')
print self.soup.originalEncoding   #修正后代码正确返回'utf-8'
```

看来beautifulsoup的自动识别编码不能全部依赖.
好不容易才定位到这里的问题,折腾了一天啊
这里用了一个方法来检测编码.就是soup.iriginalEncoding属性
```
print self.soup.originalEncoding   #修正后代码正确返回'utf-8'
```

这篇博客好水,毕竟不是很懂,所以说不出来

update:2015-03-30
#4 新技能get
之前都没好好理解原作者的博客,觉得没办法一个程序在win和ubuntu中不更改正常运行,现在发现,如果把编码改为unicode格式输出,那么系统会自动转换,这样就不存在utf-8编码在windows下cmd乱码了.


#参考文献
1 [【总结】Python 2.x中常见字符编码和解码方面的错误及其解决办法](http://www.crifan.com/summary_python_2_x_common_string_encode_decode_error_reason_and_solution/)
2 [【已解决】python中文字符乱码（GB2312，GBK，GB18030相关的问题）](http://www.crifan.com/resolved_python_garbled_chinese_characters_gb2312_gbk_gb18030-related_issues/)
参考资料太多,贴不过来啊,主要都是cifan的博客,里面资料很多,一步一步都有过程,非常好用,谢谢cifan作者
