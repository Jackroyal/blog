title: 'chrome扩展开发手记<2>-登录原理分析'
date: 2014-11-24 11:18
tags:
- chrome
- cmcc-edu
- 表单
categories:
- chrome扩展
---

上一篇,我们 介绍了一下登录的流程,但是用程序进行登录的话,我们肯定不能那样一步一步,太过复杂,所以我们接下来分下一下登录的具体过程,看看能不能作一些简化

我们接下来,给几个页面编个号,下面好分析一点
A   最原始的跳转 [http://120.202.164.10:8080/portal/?wlanacname=1022.0027.270.00&wlanuserip=10.80.97.209&ssid=CMCC-EDU](http://120.202.164.10:8080/portal/?wlanacname=1022.0027.270.00&wlanuserip=10.80.97.209&ssid=CMCC-EDU)
B  第一次改ssid  [http://120.202.164.10:8080/portal/?wlanacname=1022.0027.270.00&wlanuserip=10.80.97.209&ssid=iWuhan-Free](http://120.202.164.10:8080/portal/?wlanacname=1022.0027.270.00&wlanuserip=10.80.97.209&ssid=CMCC-EDU)

C 第一次点击 请点击登录  以后的地址
[http://120.202.164.10:8080/portal/loginFree.jsp?wlanacname=1022.0027.270.00&wlanuserip=10.80.97.209&ssid=iWuhan-Free](http://120.202.164.10:8080/portal/loginFree.jsp?wlanacname=1022.0027.270.00&wlanuserip=10.80.97.209&ssid=iWuhan-Free)

D 第二次改ssid  
[http://120.202.164.10:8080/portal/loginFree.jsp?wlanacname=1022.0027.270.00&wlanuserip=10.80.97.209&ssid=CMCC520](http://120.202.164.10:8080/portal/loginFree.jsp?wlanacname=1022.0027.270.00&wlanuserip=10.80.97.209&ssid=iWuhan-Free)





1. 从www.baidu.com跳转到A地址,这个步骤没什么特别的,服务器那边做了一个302跳转,然后我们这边就自动跳转到A地址了
1. 第一次改ssid,也就是从A到B,这个过程也没什么,只是一个单纯的地址跳转,B地址中有一个表单<br/>
![](https://img.blog.csdn.net/20141124134725891?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSmFja3JveWFs/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)<br/>
这个表单里面 主要包含四个字段,
<br/>wlanacname:1022.0027.270.00
<br/>wlanuserip:10.80.97.209
ssid:iWuhan-Free
<br/>userAgent_1:Mozilla/5.0 (Windows NT 6.2)AppleWebKit/537.36 (KHTML, like Gecko) Chrome/38.0.2125.111 Safari/537.36

1. 我们第一次点击 请提交登录,所做的操作就是把上面的表单提交到  [http://120.202.164.10:8080/portal/servlets/SingleLoginServlet](http://120.202.164.10:8080/portal/servlets/SingleLoginServlet)<br/>
服务器返回的地址是C,这一步的表单提交主要是为了获取C的地址,就是因为多了的那个loginFree.jsp,这样下一步才能改ssid为CMCC520,否则,如果你越过这一步直接改ssid为CMCC520,就会跳转到其他的页面,比如下面这个![](https://img.blog.csdn.net/20141124133708801?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSmFja3JveWFs/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)<br/>
这个地址如果点击提交,是没有办法正常登陆的,我们必须要到武汉的那个页面才行.
1. 在我们到达C那个页面以后,我们再次修改ssid,就到了D页面,然后在D页面点击  请点击登录,流程和上面的一样,页面效果也是一样,不过表单的值变了一个,具体表单post内容如下
<br/>wlanacname:1022.0027.270.00
<br/>wlanuserip:10.80.97.209
ssid:CMCC520
userAgent_1:Mozilla/5.0 (Windows NT 6.2)AppleWebKit/537.36 (KHTML, like Gecko) Chrome/38.0.2125.111 Safari/537.36<br/>
提交以后,我们就可以登录成功了,成功以后服务器会做一个302跳转,页面跳转到一个登录计时的页面.




\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*
_<br/>
_
从整体来看,最核心的一个操作就是向[http://120.202.164.10:8080/portal/servlets/SingleLoginServlet](http://120.202.164.10:8080/portal/servlets/SingleLoginServlet)提交一个表单,表单内容是
wlanacname:1022.0027.270.00
wlanuserip:10.80.97.209
ssid:CMCC520
userAgent_1:Mozilla/5.0 (Windows NT 6.2)AppleWebKit/537.36 (KHTML, like Gecko) Chrome/38.0.2125.111 Safari/537.36
然后,然后就没有然后了,那个最后计时页面的跳转没有多大的实际作用,我们只需要post这一个请求就好了
下一步我们开始制作chrome扩展,核心也是要实现这个post的请求
_\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*_
_退出登录也是如此,发一个get请求过去,然后从获得的内容中进行判断,判断成功与否_
__

下线请求
[http://120.202.164.10:8080/portal/servlets/LogoutServlet?wlanacname=1022.0027.270.00&wlanuserip=10.80.97.209&ssid=CMCC520&ATTRIBUTE_USERNAME=iWuhanFree4300&ATTRIBUTE_UUID=26FBE9A694B6221958CF6DE2704F0ECA&ATTRIBUTE_IPADDRESS=10.80.97.209&cancelAutomatismLogin=false](http://120.202.164.10:8080/portal/servlets/LogoutServlet?wlanacname=1022.0027.270.00&wlanuserip=10.80.97.209&ssid=CMCC520&ATTRIBUTE_USERNAME=iWuhanFree4300&ATTRIBUTE_UUID=26FBE9A694B6221958CF6DE2704F0ECA&ATTRIBUTE_IPADDRESS=10.80.97.209&cancelAutomatismLogin=false)

表单实际内容

wlanacname:1022.0027.270.00

wlanuserip:10.80.97.209

ssid:CMCC520

ATTRIBUTE_USERNAME:iWuhanFree4300

ATTRIBUTE_UUID:26FBE9A694B6221958CF6DE2704F0ECA

ATTRIBUTE_IPADDRESS:10.80.97.209

cancelAutomatismLogin:false








