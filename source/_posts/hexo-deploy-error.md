title: hexo发布失败
date: 2015-03-29 18:09:28
tags:
- hexo
categories:
- hexo
---
可能受到上次ddos的问题,这两天国内访问github,总是感觉不顺畅.
<!-- more -->
今天下午写了一篇博客,可是却发布不成功,一直卡在这一步:
![hexo发布的时候卡住](https://ww3.sinaimg.cn/large/692869a3gw1eqmrrf20j1j20g006676y.jpg)

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


# update:
repo的两种方式分别为ssh和https
昨天没搞清楚,专门去查了一下,ssh和https两种提交的区别
官方推荐用https,因为这回要求你输入用户名和密码,这样更安全
用ssh的话,只要你的ssh-key(可以设置一道类似密码的东西,和你的key一起加密,这样使用的时候会要求输入这段密码)对,那么就都可以提交,没有了更多的验证过程(可以设置一个para加密,提交会要求输入这段para)

2015-05-06
最近又发现，使用ssh提交的话，github不会计算到你的conribute里面去，也就是你今天提交了，但是github的contribute不会变化，所以还是改成https吧，不然怎么装B呢？

### 友情链接
[ubuntu  shadowsocks 全局 代理](http://rolight.cn/blog/?p=34)
