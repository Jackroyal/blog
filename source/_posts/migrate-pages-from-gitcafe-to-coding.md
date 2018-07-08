title: 解决 Github Pages 禁止百度爬虫的方法2--从gitcafe迁移到coding.net
date: 2016-03-06 15:50:46
tags:
- hexo
- github
- baidu
- git
categories:
- hexo
---
上一篇文章[解决 Github Pages 禁止百度爬虫的方法](http://jackroyal.github.io/2015/11/25/how-to-solve-the-problem-that-github-blocks-the-baidu-spider/),提到了解决百度爬虫被github禁用掉的方法.
`把博客同时发布到github pages和gitcafe pages.然后使用dnspod设置域名解析,国内线路解析到gitcafe,国外线路解析到github.`
然后今天去上gitcafe,偶然看到这个
![GitCafe 项目迁移至 Coding.net 公告](https://ww4.sinaimg.cn/large/692869a3gw1f1n79skvt3j20zl0m7k07.jpg)
额(⊙o⊙)…,holly shit.不过没办法,人家都被收购了,我们只能改变策略了.
话说,两家都是做代码托管的,这也是顺(bao)应(tuan)潮(qu)流(nuan)咩?
<!-- more -->
思路跟原来差不多,就是我们把其中的gitcafe内容替换成coding.net就好,以下简称coding.
`把博客同时发布到github pages和coding pages.然后使用dnspod设置域名解析,国内线路解析到coding.net,国外线路解析到github.`
1. 注册coding账号
2. 新建一个项目,名字与你的用户名相同,比如我的名字是`Jackroyal`,新建一个项目名为`Jackroyal`
3. 添加ssh公钥
4. 新建coding-pages分支
5. 开启pages服务
6. 修改hexo配置
7. 修改dns设置
# 1 注册coding账号
这个很简单不必说,注册需要填写用户名,邮箱和密码,比如我的用户名就是`Jackroyal`
# 2 新建和用户名同名的项目
在注册好账号以后,我们就去新建一个项目,方法和gitcafe不同和gihub类似,就是和用户名同名,一模一样的就行.我的项目名是`Jackroyal`,项目属性`公开`
![新建同名项目](https://ww4.sinaimg.cn/large/692869a3gw1f1n8d42xljj20qb0hbmzc.jpg)
# 3 添加`ssh`公钥
首先,我们提交代码库有两种方式,一种是https,一种是ssh,两种方式都可以,但是因为我们使用hexo来发布博客的时候是没有地方输入密码的,所以我们才考虑使用`ssh`方式,不过也不一定,我们还是可以使用https方式来提交hexo的.
## 3.1 使用ssh方式发布博客
我们只需要找到本地的ssh公钥,然后上传到coding上去就行,点击[这里](https://coding.net/user/account/setting/keys)设置.查看本地ssh公钥的方法和上篇博客一样.
![使用git客户端查看ssh公钥私钥](https://ww2.sinaimg.cn/large/692869a3gw1eye75djqo9j20gj09475k.jpg)
![查看公钥](https://ww3.sinaimg.cn/large/692869a3gw1eye7c2xpavj20if06dt9w.jpg)
复制那一段,然后在coding上添加上去就行
## 3.2 使用https方式发布
如果非要使用https方式,也是可以的,核心思路就是在本地做一次`push`操作就行.
1. 我们先`git clone`命令,我的项目就是`git clone https://git.coding.net/Jackroyal/Jackroyal.git`得到coding上刚才新建的项目.
2. 克隆完成后,接着我们进到项目目录,我们新建一个文件或者修改一个文件
3. `git add .`,把修改提交到暂存区
4. `git commit -m "测试提交"`,将修改提交到本地仓库
5. `git push`,将修改提交到远程仓库
6. 接下来,会让你输入用户名和密码,你输入
7. 然后项目就会提交,因为这个状态会保留下来,你下次在提交就可以不用输入用户名和密码了,这样为我们使用hexo发布代码提供条件
ps:这种方式,可能过一段时间又不行了,报错`could not read Username for 'https://git.coding.net': Invalid argument`,你再做一次上面的操作就行
# 4 新建coding-pages分支
当我们可以提交代码的时候,我们先来新建分支,和gitcafe差不多,它需要一个特别的分支名字,而不是像github默认的master分支,名字是`coding-pages`,如下图所示
![新建coding分支](https://ww4.sinaimg.cn/large/692869a3gw1f1n965hcj0j210r0g3jvk.jpg)
我们把它设为默认分支
# 5 开启pages服务
接下来我们需要手动去开启pages服务.在项目主页的`pages`选项卡,点击开启服务,同时我们,添加域名绑定
![开启pages服务](https://ww3.sinaimg.cn/large/692869a3gw1f1n98oezpej20sv0kxtdb.jpg)
域名绑定我们还要去修改dns解析,我们先用它分配给我们的域名去访问[http://jackroyal.coding.me/](http://jackroyal.coding.me/),看看能不能正常访问
# 5 修改hexo配置
前面的准备工作做得差不多,我们修改博客的配置信息,打开博客根目录的`_config.yml`文件,格式和之前github的差不多:
```
deploy:
- type: git
  repo: 'https://github.com/Jackroyal/Jackroyal.github.io.git'
  branch: master
- type: git
  repo: 'https://git.coding.net/Jackroyal/Jackroyal.git'
  branch: coding-pages
```
尤其注意在配置的时候注意留空格,这种键值对,比如`type: git`,其中的冒号后面到git,有个空格的,不然配置文件就会读取错误.
还要注意branch名称,分支必须是`coding-pages`
**PS:如上面所说,我选择的是https方式发布,所以我的repo是https的,如果你选择ssh方式去认证,那么你的地址应该是类似这样的`git@git.coding.net:Jackroyal/Jackroyal.git`
**

![添加coding的repo信息](https://ww4.sinaimg.cn/large/692869a3gw1f1n9byao2wj20o00gqn0e.jpg)
# 7 修改dns设置
就像第5步页面提示的,我们要绑定自己的域名,需要去修改dns解析记录,只需要添加一条CNAME指向你的`jackroyal.coding.me`即可.
注意后面加个点,也就是正确的填法是`jackroyal.coding.me.`,如下图所示.修改完成后大概等几分钟才能生效
![修改dns设置](https://ww4.sinaimg.cn/large/692869a3gw1f1n9m6me2ej20nv09c0us.jpg)

# 8 从gitcafe迁移
如果我们选择从gitcafe迁移的话,他会把我们原来的博客迁移过来.为了适应coding,你只需要从上面第二步开始就好,尤其注意的是,`新建coding-pages分支`,`新建coding-pages分支`,`新建coding-pages分支`,重要的事情说三遍,因为你迁移过来的分支名称是`gitcafe-pages`.
也就是核心问题,必须有个和用户名同名的项目,项目必须有个分支是`coding-pages`

其他没提的事情和上一篇是一样的.
最后执行`hexo d -g`,打完收工
