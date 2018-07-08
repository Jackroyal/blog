title: 解决 Github Pages 禁止百度爬虫的方法
date: 2015-11-25 11:06:50
tags:
- hexo
- github
- baidu
categories:
- hexo
---
*update*:因为gitcafe在5月31号就关闭了,所以要将文章中的gitcafe换掉,我重新写了一篇,在这里[解决 Github Pages 禁止百度爬虫的方法2--从gitcafe迁移到coding.net](http://jackroyal.github.io/2016/03/06/migrate-pages-from-gitcafe-to-coding/)

最近,我的github student package里面送的一个namecheap域名终于下来了.That is to say:我终于有自己的域名了,这就是我的域名[http://bblove.me](http://bblove.me),欢迎来访.
<!-- more -->
有域名以后,就在想办法解决百度爬虫的问题.
```
Hi Jackroyal,

We are currently blocking the Baidu user agent from crawling GitHub Pages
sites in response to this user agent being responsible for an excessive amount
of requests, which was causing availability issues for other GitHub customers.
This is unlikely to change any time soon, so if you need the Baidu user agent
to be able to crawl your site you will need to host it elsewhere.

Cheers,
Scott
```
这是我之前跟github反映百度spider无法抓取我的github pages,他们给我的回复邮件.也就是说他们把百度spider给禁掉了,桑不起.
直接说解决方法:
`把博客同时发布到github pages和gitcafe pages.然后使用dnspod设置域名解析,国内线路解析到gitcafe,国外线路解析到github.`
# 实现方法
# 1 准备条件
一共需要一下这几个东西:
+ 一个github账户
+ 一个gitcafe账户
+ 一个dnspod账号
+ 一个域名
+ github for windows(或者git客户端也行)
没了,就这么多就行.

# 2 初始操作
我们在github和gitcafe上面把自己的博客搭建起来,首先要建仓库.
**github**:
    首先登录你的github,然后新建一个公共仓库,仓库名是你的用户名加上`yourname.github.io`,如下图所示,我的用户名是`Jackroyal`
    ![github新建仓库](https://ww1.sinaimg.cn/large/692869a3gw1eye6fpc3w8j20ue0idjv0.jpg)


**gitcafe**:
    操作方法差不多,不过有点区别是,gitcafe新建的公开项目,是和用户名一致的,比如我的用户名是`Jackroyal`,那我新建的仓库名就是`Jackroyal`

仓库新建成功后,你获取到的链接应该是类似下面的
```
https://github.com/Jackroyal/Jackroyal.github.io.git
https://gitcafe.com/Jackroyal/Jackroyal.git

```
这就是你的两个page对应的仓库.
# 3 搭建博客
## 3.1 搭建github pages
当你的仓库建好了,我们就开始搭建博客,因为之前的博客有写,具体过程就不赘述[安装和配置hexo 3.0](http://jackroyal.github.io/2015/06/16/hexo-3-0-update/).

按照上面的教程一步一步,就能搭建好博客,然后你就可以访问你的gihub pages了,访问链接就是`http://yourname.github.io`,比如我的即使`http://jackroyal.github.io`.
现在我们开始搭建gitcafe上面的pages了.由于hexo现在已经支持多发布,所以很简单,我们只需要配置deploy的信息,修改`_config.yml`.添加gitcafe的仓库信息.我的修改结果如下,
```
deploy:
- type: git
  repo: 'https://github.com/Jackroyal/Jackroyal.github.io.git'
  branch: master
- type: git
  repo: 'https://gitcafe.com/Jackroyal/Jackroyal.git'
  branch: gitcafe-pages
```
尤其需要注意的是,两个的branch是不同的,`github`默认分支是`master`,`gitcafe`默认分支是`gitcafe-pages`.
## 3.2 添加gitcafe的ssh公钥
由于我使用的是`github for windows`的终端界面,也就是github官方提供的客户端,他的好处是,我使用github的时候,不需要配置ssh公钥,密钥之类的东西.如果你用的不是这个,用的是git的客户端,那就需要配置ssh公钥,参考这里[github配置公钥](https://help.github.com/articles/generating-ssh-keys/).
对的,你猜到了,我们现在需要配置gitcafe的ssh公钥,因为`github for windows`会自动帮我们上传公钥,gitcafe就只能手动了.

### 3.2.1 查看本地公钥
我们打开你使用的git终端,首先切回用户目录,然后进入`.ssh`目录,里面就有公钥和私钥,如下图所示
![使用git客户端查看ssh公钥私钥](https://ww2.sinaimg.cn/large/692869a3gw1eye75djqo9j20gj09475k.jpg)
![使用github for windows查看ssh公钥私钥](https://ww2.sinaimg.cn/large/692869a3gw1eye78gs0hvj20it0ca761.jpg)
我们就是要读取后缀是`.pub`的那个文件,比如我的读取命令就是`cat github_rsa.pub`,执行结果如下
![查看公钥](https://ww3.sinaimg.cn/large/692869a3gw1eye7c2xpavj20if06dt9w.jpg)
### 3.2.2 添加公钥到gitcafe
我们在[这里](https://gitcafe.com/account/public_keys)来设置gitcafe的公钥.
*PS:需要注意的是,公钥后面的chen@chen-pc是不需要的,我的带上以后,gitcafe说是公钥非法,不过不怕,gitcafe会帮你正确格式化*
![正确的ssh格式](https://ww1.sinaimg.cn/large/692869a3gw1eye7h4ha7dj20v105n0ve.jpg)
反正就是把公钥加上去,就ok了
## 3.4 同步博客到gitcafe
上面的配置好了以后,我们就可以把博客内容同步到gitcafe了,方法很简单,执行
```bash
hexo d -g
```
然后就可以啦.
# 4 配置域名
首先你得有个域名,像我一样,哈哈.
## 4.1 修改域名注册的解析服务器
我们在要把域名解析服务,设置到dnspod,因为他是国内的,而且服务很好.我们登录自己的域名管理后台,选择对应的域名进行管理,然后找到`nameservers`的设置,如图所示
![修改nameservers的设置](https://ww3.sinaimg.cn/large/692869a3gw1eye7orkymjj210j0i7tde.jpg)
修改的值是
```
 f1g1ns1.dnspod.net
 f1g1ns2.dnspod.net
```
常见的域名注册商修改到dnspod,官方提供了教程,看[这里](https://support.dnspod.cn/Kb/guide/)-
改完以后可能要等一段时间才能生效,最多72小时
ok,改完以后,这边就完了,我们登录dnspod

## 4.2 修改dnspod解析记录
我们登录dnspod,然后添加域名
如果4.1中的设置生效了,你的域名就会添加成功了,我们直接进行下一步,修改解析记录,上图
![dns解析记录](https://ww2.sinaimg.cn/large/692869a3gw1eye9mfkri4j20mt0efad6.jpg)
我们一共添加了5条记录,其中一条泛解析和www解析是针对国内的,所以执行gitcafe;2条泛解析和www解析是针对国外的,所以指向github.
就是添加
```
@       A      默认  192.30.252.153
@       A      默认  192.30.252.154
@       CNAME  国内  gitcafe.io.
www     CNAME  默认  jackroyal.github.io.
www     CNAME  国内  gitcafe.io.
```
其他设置项目默认就行了,上面的配置完了,如果你走国外ip访问你的域名的话,会出现404,因为我们还差一步.走国内线路访问一样会出错,都是没绑定的结果.

## 4.3 添加github的CNAME文件,添加gitcafe自定义域名
现在只差最后一步了,我们先来配置github的CNAME文件,官网有个教程[配置CNAME文件](https://help.github.com/articles/adding-a-cname-file-to-your-repository/).说白了,就是在你的网站根目录,添加一个名为`CNAME`的文件,里面的内容就是你的域名
```
bblove.me
```
没有任何多余的信息,如下图
![配置github的CNAME](https://ww1.sinaimg.cn/large/692869a3gw1eyea41g3amj20uz0a4jtg.jpg)
上面的配置现在访问没问题,但是你一重新发布博客,就需要重新手动添加`CNAME`文件,太麻烦.
一劳永逸的方法,在你的`hexo`目录下的`source`目录中,新建一个`CNAME`文件夹,然后他每次在你发布博客的时候,都会在你的网站根目录生成一个`CNAME`文件.
![添加CNAME的目录结构](https://ww2.sinaimg.cn/large/692869a3gw1eyea9pgd8cj20ko06ct9v.jpg)
现在github的配置彻底结束.

接下来配置gitcafe:
1 我们在gitcafe的项目主页,点击`项目设置`,如下图(之所以截图,是因为我找半天才看到...)
![gitcafe点击'项目设置'](https://ww1.sinaimg.cn/large/692869a3gw1eyeadedmh1j20z508eadm.jpg)
2 然后我们点击`pages服务`标签,然后添加自己的域名,这些域名是我们之前在dnspod中设置了解析的
![gitcafe配置自定义域名](https://ww3.sinaimg.cn/large/692869a3gw1eyeaftjlagj20sp0dvn1i.jpg)

彻底打完收工了

# 5 总结
总算写完了,写到这里,我一共写了,141行了,写了个把小时.这次主要参考了下面的几篇文章,都写在友情链接里面了.参考了他们的思路,因为他们的方法里面需要自己单独购买vps或者购买CDN,共同的问题是增加了开销,买vps会导致你每次写博客都需要单独发布一次到vps上太麻烦.我后来在网上看到gitcafe也有`gitcafe pages`服务,如果用这个来处理的话,一来,不会增加多余的开销,他们都是免费的;二来,由于hexo对于多部署的支持,多部署很简单,一次配置,以后就可以不用理会了.
ok,收工


# 友情链接
1 [解决 Github Pages 禁止百度爬虫的方法与可行性分析](http://jerryzou.com/posts/feasibility-of-allowing-baiduSpider-for-Github-Pages/)
2 [利用 CDN 解决百度爬虫被 Github Pages 拒绝的问题](http://www.dozer.cc/2015/06/github-pages-and-cdn.html)
3 [gitcafe官方文档--pages服务](https://help.gitcafe.com/manuals/help/pages-services)
4 [gitcafe官方文档--配置ssh公钥](https://help.gitcafe.com/manuals/help/ssh-key)

