title: 使用hexo建立自己的Github pages
date: 2014-11-27 14:48:29
tags:
- hexo
- github
categories: hexo
---
昨天经过一天的[折腾](http://jackroyal.github.io/2014/11/26/new-start/ '生命在于折腾'),总算把博客搭建起来了,今天就来写个博客总结一下.
网上的资料很多,我主要参考的是这篇博客,一路很顺利.
>http://zipperary.com/2013/05/28/hexo-guide-2/

一个很重要的原因就是他是针对windows的,刚好我也在用windows.
# 安装过程
# # 1. 安装Github for windows
因为我之前就在用Github,所以早就安装了这个.已经装过的同学请忽略这一段.
下载 [Github for windows](https://windows.github.com/ "Github for windows") 并执行即可完成安装(*在线安装,会有点慢*)。这个软件的的好处是有一个带GUI的界面,还有一个终端界面.如图所示<!-- more -->
![Github for windows](http://ww2.sinaimg.cn/large/692869a3jw1emplp1lz31j204w033mx0.jpg)
![GUI and Bash](http://ww2.sinaimg.cn/large/692869a3gw1empluh6hvej210b0j741r.jpg)
如果你不喜欢用这个,也可以用上面的博客推荐的[msysgit](http://code.google.com/p/msysgit/).

# # 2. 安装Node.js
在 Windows 环境下安装 [Node.js](http://nodejs.org/ "Node.js 官网") 非常简单，仅须下载安装文件并执行即可完成安装。（win下建议下载msi格式的，因为这样可以不用配置环境变量之类的）

# # 3. 测试node.js是否安装
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
*感谢我们家笨笨的反馈：*此处如果npm无效，首先确定win下你采用的是msi格式的安装文件，然后重启下电脑，应该就正常了。
# # 4. 安装hexo
接下来的操作我都是用**Github for windows**自带的Bash来完成的,因为后面会涉及到SSH,用**Github for windows**,就可以避免这个问题.
- 在Bash中输入以下命令
```
npm install -g hexo
```
- 创建hexo文件夹
创建你hexo放置的文件夹,先用Bash进入到目标文件夹,比如我的是F:/blog/,接下来初始化hexo,自动生成相关的文件,在F:/blog/环境下,输入
```
cd /f/blog  # 这个命令表示当前进入目录为f：/blog/
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
**ok**,到这里,博客搭建基本完成,现在要做的就是把它发布到你的Github上去
---
接下来,教你怎么发布到Github上去
# 注册Github
这一步没什么说的,如果你连简单的注册都不会,我也不会教你╮(╯▽╰)╭
# 创建公共库
在自己Github主页右下角，创建一个新的repository([点这里](https://github.com/new '点我新建'))。比如我的Github账号是Jackroyal，那么我应该创建的repository名字应该是Jackroyal.github.io(注意你的repository名字就是Jackroyal.github.io,我之前用的是Jackroyal怎么尝试都不行)。
> PS:有个大小写的问题其实我注册的是Jackroyal,大写的J,但是我访问的时候特别是带https的链接,他会自动转为小写访问.怎么说呢?简单点,你就按照你的用户名来,该大写大写,该小写小写

# 部署
现在万事俱备,只差部署了,我们来配置下`_config.yml`.
这个文件在路径是F:/blog/_config.yml.
用编辑器把它打开,修改最后一段
ps:以下为hexo 2.8x的配置方法,不适用于3.0
```
deploy:
  type: github
  repo: https://github.com/Jackroyal/Jackroyal.github.io.git
  branch: master
```

*update 2015-06-16:*
现在hexo由2.8升级到3.0了,按照上面的方式安装,你的hexo版本是hexo 3.0,3.0的这段代码的设置内容如下:
```
deploy:
  type: git
  repo: https://github.com/Jackroyal/Jackroyal.github.io.git
  branch: master
```
照着我的这个格式修改就好了,把我里面的用户名替换成你的.
至此基本完成所有搭建步骤.
# 上传
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
# 问题
我的中间出过一些问题:
1. 我的`hexo d`的时候出错,可以尝试手动删除`.deploy`文件夹,然后执行`hexo clean`还有可能出现的情况是,`deploy`没错但是一直没有提示`deploy done`,那就是骚年,你访问Github网速太慢
1. 我`deploy d`成功以后,在Github里面已经看到生成的页面了,访问<http://jackroyal.github.io>或者<https://jackroyal.github.io>一直报404的错误,这种时候等一等就好了,一般等几分钟.如果一直不好那就给官方发个邮件,他们很快会回复你的,有什么问题说清楚就行.
# 致谢
这里，要感谢我最亲爱的笨笨，是她给我测试和反馈的<http://huirong.github.io>

