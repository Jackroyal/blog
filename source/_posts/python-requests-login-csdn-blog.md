title: 基于python的requests库，模拟登录csdn博客
date: 2015-05-23 19:06:04
tags:
- python
- csdn
- 模拟登录
- requests
categories:
- python 学习笔记
---
还是继续我的python学习。以前写的爬虫用的urllib2来实现，也用过scrapy的爬虫框架，这次试试requests，刚开始用，用起来确实比urllib2好，封装的更好一些，使用起来简单方便很多。
<!-- more -->
#安装requests库
在ubuntu下面安装很简单
```bash
pip install requests
```
就搞定了

#快速上手的小例子
下面给个快速入门，最简单的例子
```python
import requests
r = requests.get('http://www.baidu.com')
print r.text
```
这段代码，很简单。
第一行，引入requests库，这是必然的。
第二行，通过get方式获取baidu首页的内容。
第三行，把返回的response内容，输出出来

果然很简单，这样就可以发送一个get请求，同理，你还可以使用
`requests.post`,`requests.put`,`requests.options`,`requests.head`，发送请求，没错就是这么简单，果然比urllib2好用。
#模拟登录csdn
我们需要其他的辅助工具
###浏览器：Firefox
###浏览器插件：tamper data，firebug
我们需要tamper data来拦截请求，因为chrome没有这个功能的插件，所以这个只能使用firefox来做（除了拦截请求chrome没有，其他的工作都可以用chrome，看个人喜好吧）。

#分析登录过程
##1 打开登录页面
我们首先打开csdn的登录页面`https://passport.csdn.net/account/login?ref=toolbar`
这个链接，前面部分是登录的网址，问号后面的参数，顾名思义，referer，就是你从哪里跳过来的，也许是一个页面跳转到登录的，toolbar就是我自己点击顶部导航栏，然后跳转到登录页面的。
##2 清除相关的cookie
为了排除不必要的干扰，我们先清除掉所有的相关的cookie，这样方便我们分析哪些参数是必须的。
![firefox中，清除cookie](http://ww1.sinaimg.cn/large/692869a3gw1esegc1itw2j20qu075779.jpg)
##3 登录过程分析
清除了cookie后，我们刷新一下页面`https://passport.csdn.net/account/login?ref=toolbar`，重新获取对应的cookie。
然后我们就开始用tamper data来拦截请求。
![使用tamper data](http://ww2.sinaimg.cn/large/692869a3gw1esegfztiz7j20s40ftdjw.jpg)
我们点击`start tamper`，在网页中填写用户名和密码。点击`登录`，会发出一个请求，然后tamper data会拦截下这个请求，询问我们是否拦截，点击tamper，我们可以在这个请求提交之前，查看请求的内容，还可以做删改。
![发出第一个登录请求前拦截下来，查看表单内容](http://ww1.sinaimg.cn/large/692869a3gw1esegkslypyj20y20gntdt.jpg)
csdn的登录过程比较简单，发送一个登录表单过去，就登录成功了，不过记得修改headers，这是后话。
##4 开始模拟登录
知道登录过程了，我们就开始写登录的代码。
```python
import requests
#使用beautifulsoup来处理获取的html内容，这个库需要安装，还是使用pip install beautifulsoup4来安装
from bs4 import BeautifulSoup as bs
#这个函数使用来提取流水号的
def toJson(str):
    '''
    提取lt流水号，将数据化为一个字典
    '''
    soup = bs(str)
    tt = {}
    #提取form表单中所有的input标签，以字典的形式来保存name：value
    for inp in soup.form.find_all('input'):
        if inp.get('name') != None:
            tt[inp.get('name')] =inp.get('value')
    return tt
    
#这行代码，是用来维持cookie的，你后续的操作都不用担心cookie，他会自动带上相应的cookie
s = requests.Session()
#我们需要带表单的参数,这里面有个参数lt,登录操作的流水号，我们需要从html页面中进行提取
payload ={'username':'jackroyal','password':'123456','lt':soup["lt"],'execution':'e1s1','_eventId':'submit'}
r = s.post("http://passport.csdn.net/account/login",data=payload,headers=header)
print r.text
```
ok，至此，登录就成功了
##5 优化
当你登录成功后，你会问，我怎么知道登录成功了呢？当你试图去抓取`http://write.blog.csdn.net/postlist`的内容的时候，你会发现一个403的错误，这是为啥呢？
很简单，`user agent`没有修改，我们用的是默认的`user agent`，这可不是一个正常的用户，所以被网站拒绝了。我们加上他就好了
```python
header = {'User-Agent':'Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:38.0) Gecko/20100101 Firefox/38.0'}
r = s.post("http://passport.csdn.net/account/login",data=payload,headers=header)
print r.text
```
#后话
上面的代码还是太简单，我们都知道cookie是有有效期的，我在做调试的时候，没修改一次，就要模拟登录一次，这样不好，我们要保存cookie，这样下次就不需要重新发送登录请求了
分享出完整的代码
```python
#!/usr/bin/env python
#-*- coding: utf-8 -*-

import requests
import time
import os
from bs4 import BeautifulSoup as bs
from cookielib import LWPCookieJar


def toJson(str):
    '''
    提取lt流水号，将数据化为一个字典
    '''
    soup = bs(str)
    tt = {}
    for inp in soup.form.find_all('input'):
        if inp.get('name') != None:
            tt[inp.get('name')] =inp.get('value')
    return tt


#cookie setting
s = requests.Session()
s.cookies = LWPCookieJar('cookiejar')
header = {'User-Agent':'Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:38.0) Gecko/20100101 Firefox/38.0'}
if not os.path.exists('cookiejar'):
    print "there is no cookie,setting"
    r = s.get("http://passport.csdn.net/account/login")
    soup = toJson(r.text)
    payload ={'username':'jackroyal','password':'123456','lt':soup["lt"],'execution':'e1s1','_eventId':'submit'}

    print payload
    r = s.post("http://passport.csdn.net/account/login",data=payload,headers=header)
    s.cookies.save(ignore_discard=True)

    print r.text
else:
    print "cookie exists,restore"
    s.cookies.load(ignore_discard=True)

#r = s.get("https://passport.csdn.net/content/loginbox/loginapi.js")
r = s.get("http://write.blog.csdn.net/postlist",headers=header)
print r.text
```




#参考文献
1 [requests官方文档快速上手——中文版](http://requests-docs-cn.readthedocs.org/zh_CN/latest/user/quickstart.html)
2 [beautifuisoup官方文档](http://www.crummy.com/software/BeautifulSoup/bs4/doc/index.zh.html)
3 [python的requests如何保存cookie到文件](http://stackoverflow.com/questions/13030095/how-to-save-requests-python-cookies-to-a-file)







