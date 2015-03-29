title: hexo发布失败
date: 2015-03-29 18:09:28
tags:
- hexo
categories:
- hexo
---
可能受到上次ddos的问题,这两天国内访问github,总是感觉不顺畅.
今天下午写了一篇博客,可是却发布不成功,一直卡在这一步:
![hexo发布的时候卡住](http://ww3.sinaimg.cn/large/692869a3gw1eqmrrf20j1j20g006676y.jpg)

然后我继续等,得到如下错误:
```
hexo Failed to receive SOCKS4 connect request ack.
```
我执行了`hexo clean`命令,手动删掉了`.deploy`文件夹,可是还是不行
最后报错
无法连接`https://github.com/Jackroyal/Jackroyal.github.io.git`

*(ps:此处已经无法重现了,抽风啊)*

最后我换了下这条链接
我修改了博客目录下的`_config.yml`,改了deploy参数
原参数设置
```
deploy:
  type: github
  repo: https://github.com/Jackroyal/Jackroyal.github.io.git
  branch: master
```
修改以后:
```
deploy:
  type: github
  repo: git@github.com:Jackroyal/Jackroyal.github.io.git
  branch: master
```
然后就deploy成功了.

*PPPS:千万注意,上面的参数设置repo:后面有一个空格,没有空格会报错*
---
###友情链接
[ubuntu  shadowsocks 全局 代理](http://rolight.cn/blog/?p=34)
