title: 安装和配置hexo 3.0
date: 2015-06-16 22:22:43
tags:
- hexo
categories:
- hexo
---
update：2015-06-24 16:56
额，谁会相信，其实这篇文章是拖了一周之后才写的。

因为最近换回了win7，所以之前搭建的hexo环境都没了，需要重新搭建。在网上看到hexo 3.0 还可以，而且，我妹子也换3.0了，so  我也换成hexo 3.0的。
<!-- more -->
#安装环境
**操作系统**：win7
**相关版本**：  
            hexo-cli: 0.1.7
            os: Windows_NT 6.1.7601 win32 x64
            node: 0.12.4


#安装过程
## 1. 安装Github for windows
因为我之前就在用Github,所以早就安装了这个.已经装过的同学请忽略这一段.
下载 [Github for windows](https://windows.github.com/ "Github for windows") 并执行即可完成安装(*在线安装,会有点慢*)。这个软件的的好处是有一个带GUI的界面,还有一个终端界面.如图所示
![Github for windows](http://ww2.sinaimg.cn/large/692869a3jw1emplp1lz31j204w033mx0.jpg)
![GUI and Bash](http://ww2.sinaimg.cn/large/692869a3gw1empluh6hvej210b0j741r.jpg)

## 2. 安装Node.js
在 Windows 环境下安装 [Node.js](http://nodejs.org/ "Node.js 官网") 非常简单，仅须下载安装文件并执行即可完成安装。（win下建议下载msi格式的，因为这样可以不用配置环境变量之类的）

## 3. 测试node.js是否安装
在任何控制台输入(可以按windows键+R,输入cmd,然后输入npm,一般来说不会有问题)
```
npm
```
返回值如下
```
Usage: npm <command>

where <command> is one of:
    add-user, adduser, apihelp, author, bin, bugs, c, cache,
    completion, config, ddp, dedupe, deprecate, docs, edit,
    explore, faq, find, find-dupes, get, help, help-search,
    home, i, info, init, install, isntall, issues, la, link,
    list, ll, ln, login, ls, outdated, owner, pack, prefix,
    prune, publish, r, rb, rebuild, remove, repo, restart, rm,
    root, run-script, s, se, search, set, show, shrinkwrap,
    star, stars, start, stop, submodule, t, tag, test, tst, un,
    uninstall, unlink, unpublish, unstar, up, update, v,
    version, view, whoami

npm <cmd> -h     quick help on <cmd>
npm -l           display full usage info
npm faq          commonly asked questions
npm help <term>  search for help on <term>
npm help npm     involved overview

Specify configs in the ini-formatted file:
    C:\Users\chenhao\.npmrc
or on the command line via: npm <command> --key value
Config info can be viewed via: npm help config

npm@1.4.28 D:\Program Files (x86)\nodejs\node_modules\npm
```
看到这个结果,就表示你的node.js已经安装上去了
*感谢我们家笨笨的反馈：*此处如果npm无效，首先确定win下你采用的是msi格式的安装文件，然后**重启**下电脑，应该就正常了。
## 4. 安装hexo
接下来的操作我都是用**Github for windows**自带的Bash来完成的,因为后面会涉及到SSH,用**Github for windows**,就可以避免这个问题.
- 在Bash中输入以下命令
```
npm install hexo-cli -g
```
- 创建hexo文件夹
创建你hexo放置的文件夹,先用Bash进入到目标文件夹,比如我的是F:/blog/,接下来初始化hexo,自动生成相关的文件,在F:/blog/环境下,输入
```
cd /f/blog  #这个命令表示当前进入目录为f：/blog/
hexo init
```
- 安装依赖包
```
npm install
```
- 本地预览,做完以上操作,可以本地预览一下
```
hexo g
hexo s
```
以上两条命令的意思是:
生成相关文件(就是生成目标html,静态博客嘛,就是很多html组成)
打开本地服务器预览(node.js就是干这事的,点击访问<http://localhost:4000>,就可以看到了)
**update-2015-09-29:**在3.0版本中将`hexo server`(简写命令就是`hexo s`),独立成模块,需要手动安装,不然你执行`hexo s`,就会出现无法识别这个命令
安装方法和后面配置模块一样
```bash
npm install hexo-server --save
```
**ok**,到这里,博客搭建基本完成,现在要做的就是把它发布到你的Github上去
---
接下来,教你怎么发布到Github上去
#注册Github
这一步没什么说的,如果你连简单的注册都不会,我也不会教你╮(╯▽╰)╭
#创建公共库
在自己Github主页右下角，创建一个新的repository([点这里](https://github.com/new '点我新建'))。比如我的Github账号是Jackroyal，那么我应该创建的repository名字应该是Jackroyal.github.io(注意你的repository名字就是Jackroyal.github.io,我之前用的是Jackroyal怎么尝试都不行)。
> PS:有个大小写的问题其实我注册的是Jackroyal,大写的J,但是我访问的时候特别是带https的链接,他会自动转为小写访问.怎么说呢?简单点,你就按照你的用户名来,该大写大写,该小写小写

#部署
现在万事俱备,只差部署了.
hexo3.0,跟2.0不同,deploy插件我们需要手动去安装,执行如下命令:
```
npm install hexo-deployer-git --save
```
接下来,我们来配置deploy的信息,修改`_config.yml`.
这个文件在路径是F:/blog/_config.yml.
用编辑器把它打开,修改最后一段
```
deploy:
  type: git
  repo: https://github.com/Jackroyal/Jackroyal.github.io.git
  branch: master
```
照着我的这个格式修改就好了,把我里面的用户名替换成你的.
至此基本完成所有搭建步骤.
#上传
我们开始上传项目的代码,再重复一次,我一直以来用的工具都是_Github for windows_自带的Bash,所以我没有配置SSH,如果你用的windows自带的终端或者其他比如msysgit,可能需要配置SSH,不然无法使用Github(点击[`这里`](https://help.github.com/articles/generating-ssh-keys/ "https://help.github.com/articles/generating-ssh-keys/")查看官方教程).
我们输入以下命令
```
hexo g
hexo d
```
或者偷个懒
```
hexo d -g
```
ok,现在就可以去看看你的个人主页了,逼格满满有木有.
如果发布不成功,继续往下面看.
#配置hexo
上面的内容和之前一样,接下来写,配置hexo 3.0的步骤.

##1 安装rss支持
执行如下命令:
```
npm install hexo-generator-feed --save
```
我们接下来修改配置文件,编辑`_config.yml`
```
feed:
  type: atom
  path: atom.xml
  limit: 20
```
##2 安装sitemap支持
执行如下命令:
```
npm install hexo-generator-sitemap --save
```
我们接下来修改配置文件,编辑`_config.yml`
```
sitemap:
    path: sitemap.xml
```

**PS:类似的,还可以安装其他的插件,尤其注意`hexo-deployer-git`这个插件,如果你发布不成功,可能是这个插件没有装**
```
npm install hexo-generator-index --save
npm install hexo-generator-archive --save
npm install hexo-generator-category --save
npm install hexo-generator-tag --save
npm install hexo-deployer-git --save
```
##3 最终修改后的配置文件
我把我的配置文件分享出来给大家参考下:
```
# Hexo Configuration
## Docs: http://hexo.io/docs/configuration.html
## Source: https://github.com/hexojs/hexo/

# Site
title: 搁浅St的blog
subtitle: 我最喜欢笨笨
description: jackroyal 博客 搁浅St 笨笨
author: 搁浅St
author: John Doe
language: zh-CN
timezone:

# URL
## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
url: http://jackroyal.github.io
root: /
permalink: :year/:month/:day/:title/
permalink_defaults:

# Directory
source_dir: source
public_dir: public
tag_dir: tags
archive_dir: archives
category_dir: categories
code_dir: downloads/code
i18n_dir: :lang
skip_render:

# Writing
new_post_name: :title.md # File name of new posts
default_layout: post
titlecase: false # Transform title into titlecase
external_link: true # Open external links in new tab
filename_case: 0
render_drafts: false
post_asset_folder: false
relative_link: false
future: true
highlight:
  enable: true
  line_number: true
  auto_detect: true
  tab_replace:

# Category & Tag
default_category: uncategorized
category_map:
tag_map:

# Date / Time format
## Hexo uses Moment.js to parse and display date
## You can customize the date format as defined in
## http://momentjs.com/docs/#/displaying/format/
date_format: YYYY-MM-DD
time_format: HH:mm:ss

# Pagination
## Set per_page to 0 to disable pagination
per_page: 10
pagination_dir: page


# Extensions
## Plugins: http://hexo.io/plugins/

index_generator:
  per_page: 10 ##首页默认10篇文章标题 如果值为0不分页

archive_generator:
    per_page: 10 ##归档页面默认10篇文章标题
    yearly: true  ##生成年视图
    monthly: true ##生成月视图

tag_generator:
    per_page: 10 ##标签分类页面默认10篇文章

category_generator:
    per_page: 10 ###分类页面默认10篇文章

sitemap:
    path: sitemap.xml

feed:
  type: atom
  path: atom.xml
  limit: 20

duoshuo_shortname: jackroyal  ##这里填写你的多说的短网址
## Themes: http://hexo.io/themes/
theme: light   ##主题的名称,我用的是light,默认的是landscape

# Deployment
## Docs: http://hexo.io/docs/deployment.html
deploy:
  type: git
  repo: https://github.com/Jackroyal/Jackroyal.github.io.git
  branch: master
```

hexo 3.0有一些新的特性,插件的封装更好了,插件安装和管理很方便.
hexo还支持多发布,可以同时发布到github和gitcafe.

#参考文献
1 [hexo 服务器](https://hexo.io/zh-cn/docs/server.html)
