title: 'chrome扩展开发手记<1>-需求分析'
date: 2014-11-21 18:17
tags:
- chrome
- cmcc-edu
- 中国移动
categories:
- chrome扩展
---

本人的学校在武汉,有CMCC-EDU的网络覆盖,中国移动和武汉市政府搞了一个活动,可以免费公益上网,说白了,就是可以免费使用EDU的网络,如图.
![](http://img.blog.csdn.net/20141121181910580?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSmFja3JveWFs/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)



以上是前提.

这个网络的使用流程是这样子的.






1. 你打开任何一个网址,比如http://www.baidu.com  <br/>
都会跳转到 [](http://120.202.164.10:8080/portal/?wlanacname=1022.0027.270.00&wlanuserip=10.80.97.209&ssid=CMCC-EDU)[http://120.202.164.10:8080/portal/?wlanacname=1022.0027.270.00&wlanuserip=10.80.97.209&ssid=CMCC-EDU](http://120.202.164.10:8080/portal/?wlanacname=1022.0027.270.00&wlanuserip=10.80.97.209&ssid=CMCC-EDU)  界面如图所示<br/>
![](http://img.blog.csdn.net/20141124103645611?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSmFja3JveWFs/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
1. 接下来我们手动更改最上面的网址栏,把ssid改为iWuhan-Free,(注意大小写)<br/>
修改后的网址如下[http://120.202.164.10:8080/portal/?wlanacname=1022.0027.270.00&wlanuserip=10.80.97.209&ssid=iWuhan-Free](http://120.202.164.10:8080/portal/?wlanacname=1022.0027.270.00&wlanuserip=10.80.97.209&ssid=CMCC-EDU)界面如下<br/>
![](http://img.blog.csdn.net/20141124104120281?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSmFja3JveWFs/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
1. 接下来我们点击,请点击登录按钮,系统反馈如下<br/>
![](http://img.blog.csdn.net/20141124104623968?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSmFja3JveWFs/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)<br/>
我们忽略这个警告,可以注意到,上面的地址栏已经变掉了,多了个loginFree.jsp完整的地址[http://120.202.164.10:8080/portal/loginFree.jsp?wlanacname=1022.0027.270.00&wlanuserip=10.80.97.209&ssid=iWuhan-Free](http://120.202.164.10:8080/portal/loginFree.jsp?wlanacname=1022.0027.270.00&wlanuserip=10.80.97.209&ssid=iWuhan-Free)
1. 接下来,我们再改一次ssid,改为CMCC520,完整地址如下[http://120.202.164.10:8080/portal/loginFree.jsp?wlanacname=1022.0027.270.00&wlanuserip=10.80.97.209&ssid=CMCC520](http://120.202.164.10:8080/portal/loginFree.jsp?wlanacname=1022.0027.270.00&wlanuserip=10.80.97.209&ssid=iWuhan-Free) 界面还是和上图一样,就不贴图了,我们再点击一次,请点击登录,然后就会跳到一个计时页面,这样就表示你登录成功,可以上网了






登录的流程走完了,下一篇,接下来我们分析一下登录的原理,简化一下登录的流程

