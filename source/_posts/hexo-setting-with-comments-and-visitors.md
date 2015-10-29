title: 给hexo配置上评论和访问量
date: 2015-05-30 14:08:49
tags:
- hexo
categories:
- hexo
---
哈哈,我们家笨笨,绝对是我学习技术的很大很大很大大的动力.更开始用github pages的时候,就觉得没法看访问量和评论数量这事,很蛋疼,不过也没折腾.
今天上午,我们家笨笨说没有评论和访问量不好,都不知道多少人看了.妹子都发话了,果断要搞定.
<!-- more -->
google一番找到解决方法:
#### 1 关于评论数量的显示,我们可以直接使用多说官网提供的方法,很简单
#### 2 关于访问量统计这事儿,不太好搞,我找到了这个[不蒜子](http://ibruce.info/2015/04/04/busuanzi/),这位非著名码农自己做的访问统计,哈哈,点个赞
#### 3 今天还看到了font-awesome,可以显示一些小图标,为了显示效果更好,一并加上去
先展示一下最终的效果:<br>
![页底关于整站访问量的显示](http://ww1.sinaimg.cn/large/692869a3gw1esm9snxfcuj20su0ad74x.jpg)
![文章页面关于访问量和评论数量的显示](http://ww4.sinaimg.cn/large/692869a3gw1esm9tcczdij20r00doq4t.jpg)

下面,开始动手
# 1 配置评论数量的显示
首先,确认你的评论系统用的是多说,如果不是,那就不用继续往下看了(参考[这里](http://localhost:4000/2014/11/28/custom-hexo/)配置多说).
我使用的主题是`light`,其他主题原理类似.我们打开`themes/light/layout/_partial/article.ejs`,我们在`header`标签的尾部添加上下面的代码:
```html
    <% if (item.excerpt && index){ %>
     <% } else { %>
     <div class="busuanzi_container_page_pv">
       <span class="head-plus">
       <i class="fa fa-user"></i><span id="busuanzi_value_page_pv"><i class="fa fa-spinner fa-spin"></i></span>次访问
       </span>
       <span class="head-plus">
       <i class="fa fa-comments"></i><span class="ds-thread-count" data-thread-key="<%= page.layout %>-<%= page.slug %>"><i class="fa fa-spinner fa-spin"></i></span>条评论
       </span>
     </div>
     <% } %>
```
最终效果如图<br>
![修改后的article.ejs](http://ww2.sinaimg.cn/large/692869a3gw1esmas5d58oj20x50jc46f.jpg)
以上代码,是最终成型的代码,我加入了一些美化的东西,最核心的就是
```html
<span class="ds-thread-count" data-thread-key="<%= page.layout %>-<%= page.slug %>"></span>
```
尤其注意`data-thread-key`的设置,他和你之前配置多说的时候是一致的,每篇文章有一个独一无二的key,你可以去你的`themes/light/layout/_partial/comment.ejs`里找到关于data-thread-key的内容,直接复制过来即可.

Ps:这里我们可能还需要配置下数据显示的格式,在你的多说后台管理里面,进入设置界面如图
![多说后台设置](http://ww3.sinaimg.cn/large/692869a3gw1esmayan2utj20w50mx0ya.jpg)
我们找到`暂无评论`,`1条评论`,`{num}条评论`,这几个设置,修改成自己要的格式,也可以参照我的修改,`0`,`1`,`{num}`

# 2 配置文章访问量的显示
这个更简单了,按照作者的说法,只需要引入一个js,再添加一个span就完成了.由于js一般放置在页面的最底部,所以我们找到`themes/light/layout/_partial/after_footer.ejs`,我们在最后添加上下面的代码:
```javascript
<script async src="https://dn-lbstatics.qbox.me/busuanzi/2.3/busuanzi.pure.mini.js">
</script>
```
上面就完成了js文件的引入,我们再在要显示访问量的地方添加一个span就行了.比如我想显示在标题的下面,那就打开`themes/light/layout/_partial/article.ejs`,添加的代码,就是`第一步`里面添加的那个.如果你想简洁一点,也可以只添加下面核心代码就行:
```html
本站总访问量<span id="busuanzi_value_site_pv"></span>次
本站访客数<span id="busuanzi_value_site_uv"></span>人次
本文总阅读量<span id="busuanzi_value_page_pv"></span>次
```
# 3  经过美化后最终的代码
如果只用官方提供的代码,没有优化,很丑,我优化了下,分享一下.

1.修改`themes/light/layout/_partial/article.ejs`,在header标签的末尾添加以下代码
```html
    <% if (item.excerpt && index){ %>
     <% } else { %>
     <div class="busuanzi_container_page_pv">
       <span class="head-plus">
       <i class="fa fa-user"></i><span id="busuanzi_value_page_pv"><i class="fa fa-spinner fa-spin"></i></span>次访问
       </span>
       <span class="head-plus">
       <i class="fa fa-comments"></i><span class="ds-thread-count" data-thread-key="<%= page.layout %>-<%= page.slug %>"><i class="fa fa-spinner fa-spin"></i></span>条评论
       </span>
     </div>
     <% } %>
```
![修改后的article.ejs](http://ww2.sinaimg.cn/large/692869a3gw1esmas5d58oj20x50jc46f.jpg)<br>2.修改`themes/light/layout/_partial/footer.ejs`,下面是footer.ejs的**全部代码**:
```html
<div class="alignleft">
  <% if (config.author){ %>
  &copy; <%= new Date().getFullYear() %> <%= config.author %>
  <% } else { %>
  &copy; <%= new Date().getFullYear() %> <%= config.title %>
  <% } %>
<span id="busuanzi_container_site_pv">
     <i class="fa fa-flag"></i>    你是第<span id="busuanzi_value_site_pv"><i class="fa fa-spinner fa-spin"></i></span>个到访的小伙伴
</span>
</div>
<div class="clearfix"></div>
```
<br>3.修改`themes/light/layout/_partial/after_footer.ejs`,我们在**最后**添加上下面的代码:

```javascript
<script async src="https://dn-lbstatics.qbox.me/busuanzi/2.3/busuanzi.pure.mini.js">
</script>
```

4.修改`themes\light\source\css\_partial\article.styl`,我们在第124行,在`.entry`之前添加上以下代码,尤其注意与左右的间距,因为它是一个树形结构,它与左侧的距离,影响着它的层级.我们这里应该和entry平级,下面代码应该与`.entry`对齐
```
  .busuanzi_container_page_pv
      margin:20px 0
      color: # 817C7C
      font-size: 12px
  # busuanzi_value_page_pv
      padding-left:4px
  .head-plus
      padding-left:4px
  .ds-thread-count
      padding-left:4px

```
最终效果如图:
![添加css,注意左对齐](http://ww4.sinaimg.cn/large/692869a3gw1esmajbsx38j20k30gwadu.jpg)<br>5.修改`themes\light\source\css\_partial\footer.styl`,在最后添加上以下代码
```
# busuanzi_value_site_pv
  color:black
  padding:4px
# busuanzi_container_site_pv
  padding-left:2em
```
6.修改`themes\light\source\css\_partial\variable.styl`,在最后添加以下代码
```
@import url("http://libs.useso.com/js/font-awesome/4.2.0/css/font-awesome.min.css")
```

ok  打完收工

# 参考文献
1 博客访问技术工具--不蒜子 <http://ibruce.info/2015/04/04/busuanzi/>
2 [多说-代码显示【文章评论数】方法](http://dev.duoshuo.com/docs/5016427f77cf5fa30500000e)

# 致谢
这里，要感谢我最亲爱的笨笨<http://huirong.github.io>
