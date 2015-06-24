title: 配置hexo
date: 2014-11-28 21:07:27
tags:
- hexo
categories: hexo
---
经过[上篇博客](/how-to-build-a-blog-with-hexo.html),我们搭建起了自己的博客,接下来我们对它做些个性化的定制.
在hexo中,配置文件一共两个(我的hexo安装在F:/blog/),分别是`F:/blog/_config.yml`和`F:/blog/themes/light/_config.yml`.第一个是全局的配置文件,第二个是主题的配置文件,在继续说之前,我们先来说一下主题安装.<!-- more -->
#主题安装
这个很简单,在hexo的Github的主页上有个主题栏目,<https://github.com/hexojs/hexo/wiki/Themes>,里面列出了很多主题.
安装方法很简单
```
$ git clone <repository> themes/<theme-name>
```
举个简单例子,我安装的主题名为`light`,请在`F:/blog/`目录下执行以下代码
```
$ git clone https://github.com/hexojs/hexo-theme-light themes/light
```
如果你不是在`F:/blog/`中执行,请修改后面的路径themes/light为你的路径.基本原理就是把主题下下来,放在themes目录下就OK了,主题安装完毕.
#修改全局配置文件 `F:/blog/_config.yml`
```
# Hexo Configuration
## Docs: http://hexo.io/docs/configuration.html
## Source: https://github.com/hexojs/hexo/

# Site
title: 搁浅St的blog  #站点的名称
subtitle: 我最喜欢笨笨   #站点的副标题
description:             #站点的描述,有利于搜索引擎的抓取
author: 搁浅St       #作者
email: geqianst@qq.com          #你的邮箱
language: zh-CN           #语言,一般应该都是这个吧

# URL
## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
url: http://jackroyal.github.io       #网站的url,在页面上,可以调用配置中的url参数,就是这个,比如google自定义搜索,需要制定搜索范围,就是通过这个设置的
root: /
permalink: :year/:month/:day/:title/
tag_dir: tags
archive_dir: archives
category_dir: categories
code_dir: downloads/code
permalink_defaults:

# Directory
source_dir: source
public_dir: public

# Writing
new_post_name: :title.md # File name of new posts
default_layout: post  
titlecase: false # Transform title into titlecase
external_link: true # Open external links in new tab
filename_case: 0
render_drafts: false
post_asset_folder: false
relative_link: false
highlight:
  enable: true
  line_number: true
  tab_replace:

# Category & Tag
default_category: uncategorized  
category_map:
tag_map:

# Archives 默认值为2，这里都修改为1，相应页面就只会列出标题，而非全文
## 2: Enable pagination
## 1: Disable pagination
## 0: Fully Disable
archive: 1
category: 1
tag: 1

# Server
## Hexo uses Connect as a server
## You can customize the logger format as defined in
## http://www.senchalabs.org/connect/logger.html
port: 4000
server_ip: localhost
logger: false
logger_format: dev

# Date / Time format
## Hexo uses Moment.js to parse and display date
## You can customize the date format as defined in
## http://momentjs.com/docs/#/displaying/format/
date_format: MMM D YYYY
time_format: H:mm:ss

# Pagination
## Set per_page to 0 to disable pagination
per_page: 5
pagination_dir: page

# Disqus
disqus_shortname:
#这一行是我添加的duoshuo_shortname,因为天朝disqus不好用,用多说
duoshuo_shortname: jackroyal
# Extensions
## Plugins: https://github.com/hexojs/hexo/wiki/Plugins
## Themes: https://github.com/hexojs/hexo/wiki/Themes
theme: light
exclude_generator:

# Deployment  发布相关设置
## Docs: http://hexo.io/docs/deployment.html
deploy:
  type: github
  repo: https://github.com/Jackroyal/Jackroyal.github.io.git
  branch: master
```
至此,全局配置文件修改完毕,你可以`hexo g`和`hexo s`进行查看.
```
menu:#导航栏的设置默认只有这两个,还可以添加更多的导航
  Home: /
  Archives: /archives
#它就是你页面右边的侧边项目,比如搜索之类,你可以根据自己的需求进行修改,你能用几个widgets可以在F:\blog\themes\light\layout\_widget中进行查看
widgets:#我这里使用了所有的widgets
- search
- category
- tag
- recent_posts
- tagcloud

excerpt_link: Read More  #可以换成中文的  阅读全文

twitter:
  username:
  show_replies: false
  tweet_count: 5
//默认的一个分享组件,因为主要针对国外,不适合国内我们不使用它
addthis:
  enable: false  #把true改为false
  pubid:
  facebook: true
  twitter: true
  google: true
  pinterest: true

fancybox: true

google_analytics:
rss:
duoshuo_shortname: jackroyal  #多说的用户名

comment_provider:
# Facebook comment
facebook:
  appid: 123456789012345
  comment_count: 5
  comment_width: 840
  comment_colorscheme: light
```
至此,配置文件修改完毕,上面提到了我们不适用disqus的评论组件,使用多说,下面教大家来配置多说

#创建多说
首先我们去多说注册一个账号,[点击这里](http://duoshuo.com/ "多说官网")
我们点击[我要安装](http://duoshuo.com/create-site/ "我要安装"),界面如下
![duoshuo 创建界面](http://ww4.sinaimg.cn/large/692869a3gw1emtdlnpsqaj20wo0lp783.jpg "创建多说账号")
shortname就是jackroyal,创建完成后,跳转到如下界面
![获取多说代码](http://ww1.sinaimg.cn/large/692869a3gw1emtdp96x54j20us0jxn18.jpg "获取多说代码")
我们选择`通用代码`,点击复制,就行了
#配置多说
这里说一个前提,我使用的是light主题,如果你用的是其他主题,接下来的设置可能给我有点区别,但是原理差不多,参考看看
1. 我们打开`F:\blog\themes\light\layout\_partial\comment.ejs`这个文件,然后修改后代码如下(如果你不是light主题,可能跟这个不一样,你去找下包含comment的section在哪里,改法还是这样.例如系统默认的landscape主题,下面这段代码就是在article.ejs,它没有comment.ejs)
```
<% if (page.comments){ %>
<!-- 这里添加了一个导航,页面的下面会有一个上一篇,下一篇 -->
 <nav id="pagination" >
    <% if (page.prev) { %>
    <a href="<%- config.root %><%- page.prev.path %>" class="alignleft prev" > <%=page.prev.title %> </a>
    <% } %>
    <% if (page.next) { %>
    <a href="<%- config.root %><%- page.next.path %>" class="alignright next" > <%=page.next.title %> </a>
    <% } %>
    <div class="clearfix"></div>
</nav>
<!-- 导航结束 -->
<section id="comments">
<!-- 这里是多说的代码,直接把你的代码粘贴到这里就行 -->
    <!-- 多说评论框 start -->
    <div class="ds-thread" data-thread-key="<%= page.layout %>-<%= page.slug %>" data-title="<%= page.title %>" data-url="<%= page.permalink %>"></div>
    <!-- 多说评论框 end -->
    <!-- 多说公共JS代码 start (一个网页只需插入一次) -->
    <script type="text/javascript">
    var duoshuoQuery = {short_name:'<%= config.duoshuo_shortname %>'};
      (function() {
        var ds = document.createElement('script');
        ds.type = 'text/javascript';ds.async = true;
        ds.src = (document.location.protocol == 'https:' ? 'https:' : 'http:') + '//static.duoshuo.com/embed.js';
        ds.charset = 'UTF-8';
        (document.getElementsByTagName('head')[0]
         || document.getElementsByTagName('body')[0]).appendChild(ds);
      })();
      </script>
    <!-- 多说公共JS代码 end -->
  <!-- 多说结束 -->
  </section>
<% } %>
```
眼尖的同学可能已经看到我的上面第16行代码与你们的不同,这行代码包括了页面的标题和url,它会根据hexo的配置,由hexo动态生成,所以你把你的代码替换成我的这行代码.
```
<div class="ds-thread" data-thread-key="<%= page.layout %>-<%= page.slug %>" data-title="<%= page.title %>" data-url="<%= page.permalink %>"></div>
```
至此,多说添加完毕
#修复bug
我发现light的主题貌似有个小bug,在`F:\blog\themes\light\layout\_partial\article.ejs`中间第27行有这样一行代码
```
        <% if (item.comment && config.disqus_shortname){ %>
```
首先我们,替换config.disqus_shortname为config.duoshuo_shortname.
然后修改`item.comment`为`item.comments`,因为系统中没有comment这个变量,只有comments这个变量,如果不修改comments,那么`item.comment`一直为假,所以一直不成立,就不会显示comments字段了.修改后代码如下
```
 <% if (item.comments && config.duoshuo_shortname){ %>
```

产生的效果如图![开启和关闭comment的区别](http://ww3.sinaimg.cn/large/692869a3gw1emteitia5uj20tl0fn412.jpg "开启和关闭comment的区别").
至此,配置hexo,打完收工
enjoy it

---
#参考文献
1 <http://zipperary.com/2013/05/29/hexo-guide-3/>
2 [duoshuo官方Hexo使用教程](http://dev.duoshuo.com/threads/541d3b2b40b5abcd2e4df0e9)

