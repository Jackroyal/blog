title: HTTP1.0和HTTP1.1的区别
date: 2016-03-25 13:36:53
tags:
- HTTP
- apache
- Linux
categories:
- 服务器
---
今天研究了下HTTP,还是挺有意思的.
接下来,就比较一下两者的区别.
<!-- more -->
# 1 长连接
这个应该是变化最大的一个了.在1.0的版本中,如果客户端请求头没有设置`Connection: Keep-Alive`的话,那么每次请求完成都会立即断开连接,然后客户端又要重新建立一个HTTP连接.假设一个网页包含了10个图片,那么为了请求图片,客户端必须要发送10次请求,无疑这对带宽和资源是极大的浪费,TCP的优势就没有体现出来.
在HTTP1.1中,`keep-Alive`已经被弃用(但是大多数服务器和浏览器都还保留这个选项).在1.1的版本中,持久连接默认就是启用的,除非你显式在响应头部包含`Connection: close`,客户端收到响应后才会关闭连接.
# 2 host头域
在HTTP1.0中认为每台服务器都绑定一个唯一的IP地址,因此,请求消息中的URL并没有传递主机名(hostname).但随着虚拟主机技术的发展,在一台物理服务器上可以存在多个虚拟主机(Multi-homed Web Servers),并且它们共享一个IP地址.
HTTP1.1的请求消息和响应消息都应支持`Host`头域,且请求消息中如果没有`Host`头域会报告一个错误(400 Bad Request).此外,服务器应该接受以绝对路径标记的资源请求.
# 3 请求方法
HTTP1.1增加了`OPTIONS`, `PUT`, `DELETE`, `TRACE`, `CONNECT`这些Request方法.
       Method         = **"OPTIONS"**               ; Section 9.2
                      | "GET"                    ; Section 9.3
                      | "HEAD"                   ; Section 9.4
                      | "POST"                   ; Section 9.5
                      | **"PUT"**                   ; Section 9.6
                      | **"DELETE"**                 ; Section 9.7
                      | **"TRACE"**               ; Section 9.8
                      | **"CONNECT"**                ; Section 9.9
                      | extension-method
       extension-method = token
HTTP1.1 增加的新的status code：
```
(HTTP1.0没有定义任何具体的1xx status code, HTTP1.1有2个)
100 Continue
101 Switching Protocols
203 Non-Authoritative Information
205 Reset Content
206 Partial Content
302 Found (在HTTP1.0中有个 302 Moved Temporarily)
303 See Other
305 Use Proxy
307 Temporary Redirect
405 Method Not Allowed
406 Not Acceptable
407 Proxy Authentication Required
408 Request Timeout
409 Conflict
410 Gone
411 Length Required
412 Precondition Failed
413 Request Entity Too Large
414 Request-URI Too Long
415 Unsupported Media Type
416 Requested Range Not Satisfiable
417 Expectation Failed
504 Gateway Timeout
505 HTTP Version Not Supported
```
# 4 内容长度
通常,HTTP应答消息中发送的数据是整个发送的,`Content-Length`消息头字段表示数据的长度.数据的长度很重要,因为客户端需要知道哪里是应答消息的结束,以及后续应答消息的开始.但是在一些动态网页中,由于网页是动态生成的,所以没法计算出准确的`Content-Length`,这样导致的后果是:如果 `Content-Length` 比实际长度短,会造成内容被截断;如果比实体内容长,会造成 pending,浏览器一直转圈圈.
所以在HTTP1.1中引入了`Transfer-Encoding`,如果一个HTTP消息(请求消息或应答消息)的`Transfer-Encoding`消息头的值为chunked,那么,消息体由数量未定的块组成,并以最后一个大小为0的块为结束.
ps:如果同时设置了`Content-Length` 和`Transfer-Encoding`,那么`Transfer-Encoding`的优先级更高,`Content-Length`会被忽略.
# 5 缓存
+ 在HTTP/1.0中,使用`Expire`头域来判断资源的`fresh`或`stale`,并使用条件请求(conditional request)来判断资源是否仍有效.例如,cache服务器通过`If-Modified-Since`头域向服务器验证资源的`Last-Modefied`头域是否有更新,源服务器可能返回304(Not Modified),则表明该对象仍有效;也可能返回200(OK)替换请求的Cache对象.
+ 此外,HTTP/1.0中还定义了`Pragma:no-cache`头域,客户端使用该头域说明请求资源不能从cache中获取,而必须回源获取.
+ HTTP/1.1在1.0的基础上加入了一些cache的新特性,当缓存对象的Age超过`Expire`时变为`stale`对象,cache不需要直接抛弃`stale`对象,而是与源服务器进行重新激活(revalidation).
+ HTTP/1.0中,`If-Modified-Since`头域使用的是绝对时间戳,精确到秒,但使用绝对时间会带来不同机器上的时钟同步问题.而HTTP/1.1中引入了一个`ETag`头域用于重激活机制,它的值`entity tag`可以用来唯一的描述一个资源.请求消息中可以使用`If-None-Match`头域来匹配资源的`entitytag`是否有变化.
+ 为了使caching机制更加灵活,HTTP/1.1增加了`Cache-Control`头域(请求消息和响应消息都可使用),它支持一个可扩展的指令子集：例如`max-age`指令支持相对时间戳;`private`和`no-store`指令禁止对象被缓存;`no-transform`阻止Proxy进行任何改变响应的行为.
+ Cache使用关键字索引在磁盘中缓存的对象,在HTTP/1.0中使用资源的URL作为关键字.但可能存在不同的资源基于同一个URL的情况,要区别它们还需要客户端提供更多的信息,如`Accept-Language`和`Accept-Charset`头域.为了支持这种内容协商机制(content negotiation mechanism),HTTP/1.1在响应消息中引入了Vary头域,该头域列出了请求消息中需要包含哪些头域用于内容协商.




# 参考文献

1 [HTTP 协议中的 Transfer-Encoding](HTTPs://imququ.com/post/transfer-encoding-header-in-HTTP.html)
2 [HTTP/1.1与HTTP/1.0的区别](http://blog.csdn.net/forgotaboutgirl/article/details/6936982)
