title: html5在ie中遇到的那些坑
date: 2015-05-15 23:19:43
tags:
- 前端
- css
- html5
- ie
- 兼容性
categories:
- 前端
---
这段时间,笨笨找了个活儿,做一个页面,能够适配所有的主流浏览器,包括pc,平板和手机等终端(这和当年某刘提的要求一样啊).
我第一个想到的是bootstrap,可惜这货太大了,我只是几个简单的页面,没必要用这么大体量的东西.
搜索一番以后,我找到了我的解决方案,media query,支付宝也是这么解决的.
<!-- more -->
# 前提条件
以下所给出的例子,都是html5的页面,不合适html4.0的页面,也就是说你的头部必须是
`<!DOCTYPE html>`才行,如果是`<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">`,是不行的,要仔细看清楚哈.
下面就来罗列一下,这次经过的那些坑:
# 1  自动适配终端屏幕宽度
```
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
```
content属性值 :
     width:可视区域的宽度，值可为数字或关键词device-width
     height:同width
     intial-scale:页面首次被显示是可视区域的缩放级别，取值1.0则页面按实际尺寸显示，无任何缩放
     maximum-scale=1.0, minimum-scale=1.0;可视区域的缩放级别，
              maximum-scale用户可将页面放大的程序，1.0将禁止用户放大到实际尺寸之上。
     user-scalable:是否可对页面进行缩放，no 禁止缩放

# 2  使ie6-8支持html5的元素
```
<!-- HTML5 shim, for IE6-8 support of HTML5 elements -->
<!--[if lt IE 9]>
<script src="http://html5shim.googlecode.com/svn/trunk/html5.js"></script>
<![endif]-->
```
以上代码,顾名思义,当ie的版本是9一下的时候,加载html5.js这个文件(建议最好下载下来在本地调用),他是用来帮助ie6-8支持html元素的.

# 3  ie10以下的版本不支持placeholder(不完美解决)
placeholder是html5里面才有的东东,ie8不支持就算了,ie9都不支持,伤不起.这个没办法,只能用js去写,网上有很多类似的东西,不过不是很好用,我找到的都不完美.
```
(function($) {
  /**
   * 没有开花的树
   * http://blog.csdn.net/mycwq/
   * 2012/11/28 15:12
   */

  var placeholderfriend = {
    focus: function(s) {
      s = $(s).hide().prev().show().focus();
      var idValue = s.attr("id");
      if (idValue) {
        s.attr("id", idValue.replace("placeholderfriend", ""));
      }
      var clsValue = s.attr("class");
      if (clsValue) {
        s.attr("class", clsValue.replace("placeholderfriend", ""));
      }
    }
  }

  //判断是否支持placeholder
  function isPlaceholer() {
    var input = document.createElement('input');
    return "placeholder" in input;
  }
  //不支持的代码
  if (!isPlaceholer()) {
    $(function() {

      var form = $(this);

      //遍历所有文本框，添加placeholder模拟事件
      var elements = form.find("input[type='text'][placeholder]");
      elements.each(function() {
        var s = $(this);
        var pValue = s.attr("placeholder");
        var sValue = s.val();
        if (pValue) {
          if (sValue == '') {
            s.val(pValue);
          }
        }
      });

      elements.focus(function() {
        var s = $(this);
        var pValue = s.attr("placeholder");
        var sValue = s.val();
        if (sValue && pValue) {
          if (sValue == pValue) {
            s.val('');
          }
        }
      });

      elements.blur(function() {
        var s = $(this);
        var pValue = s.attr("placeholder");
        var sValue = s.val();
        if (!sValue) {
          s.val(pValue);
        }
      });

      //遍历所有密码框，添加placeholder模拟事件
      var elementsPass = form.find("input[type='password'][placeholder]");
      elementsPass.each(function(i) {
        var s = $(this);
        var pValue = s.attr("placeholder");
        var sValue = s.val();
        if (pValue) {
          if (sValue == '') {
            //DOM不支持type的修改，需要复制密码框属性，生成新的DOM
            var html = this.outerHTML || "";
            html = html.replace(/\s*type=(['"])?password\1/gi, " type=text placeholderfriend")
              .replace(/\s*(?:value|on[a-z]+|name)(=(['"])?\S*\1)?/gi, " ")
              .replace(/\s*placeholderfriend/, " placeholderfriend value='" + pValue
              + "' " + "onfocus='placeholderfriendfocus(this);' ");
            var idValue = s.attr("id");
            if (idValue) {
              s.attr("id", idValue + "placeholderfriend");
            }
            var clsValue = s.attr("class");
            if (clsValue) {
              s.attr("class", clsValue + "placeholderfriend");
            }
            s.hide();
            s.after(html);
          }
        }
      });

      elementsPass.blur(function() {
        var s = $(this);
        var sValue = s.val();
        if (sValue == '') {
          var idValue = s.attr("id");
          if (idValue) {
            s.attr("id", idValue + "placeholderfriend");
          }
          var clsValue = s.attr("class");
          if (clsValue) {
            s.attr("class", clsValue + "placeholderfriend");
          }
          s.hide().next().show();
        }
      });

    });
  }
  window.placeholderfriendfocus = placeholderfriend.focus;
})(jQuery);
```
我最终选择这段代码,他能够在ie的各版本中正常运行,支持`text`和`password`标签.
唯一的不足是,你直接点提交,你的text里面会有内容,内容就是placeholder中设置的提示内容.
# 4  background-size在ie9一下不支持
background-size属性是用来让背景图片平铺的.不过ie9一下并不支持这个属性,我们使用滤镜来实现平铺的效果,示例代码如下.
```
background-size: cover;
    filter:progid:DXImageTransform.Microsoft.AlphaImageLoader(src='./assets/images/01.png', sizingMethod='scale')\9;
```
需要注意的是,这里的图片路径是相对于html文件的,不是css的路径,如果设置了没效果,先检查下你的路径.
# 5  input输入框 光标不居中
这个问题来自于魅族手机,还有老大难的ie6-8的测试中.
解决方法是设置`height`和`line-height`等高,没那么简单,这里有技巧的
```css
    line-height: normal;/*这行代码解决了魅族手机中的不居中问题*/
    line-height: 42px\9;/*这行css代码的含义是ie9以下设置line-height为42px*/
```
# 6  opacity的继承问题
```css
<div id='a' style="">
    <div id='b'>
    hello moto
    </div>
</div>
```

如果我们给父div设置了一个`opacity`为0.1,即使我们给子div设置`opacity`为1,也是不管用的,如图
![未设置父div的opacity之前](http://ww1.sinaimg.cn/large/692869a3gw1es5fm0jafhj20iv0ecq46.jpg)
设置父div的`opacity`之后
![设置父div的opacity之后](http://ww4.sinaimg.cn/large/692869a3gw1es5fnsk3pvj20hy0fdwfm.jpg)
我们可以清楚看到子div受到了影响.
解决办法:解除父子关系,把子div从父div中挪出来

# 7  border-radius在ie9以下不支持
输入框等其他地方用到圆角的话,在老版本ie中果断是没有支持的.
我们需要用hack的方式[pie](http://css3pie.com/),去实现border-radius和box-shadow等功能.加入pie以后的css代码如下:
```
    -moz-border-radius: 6px;
    -webkit-border-radius: 6px;
    border-radius: 6px;
    behavior: url(assets/css/pie.htc);/*这一行是核心*/
```
缺点:设置的border-radius四角的设置值都是相同的

# 8 css递进关系
挖坑,以后填


ps:晚安,亲爱的



# 参考文献
1 [meta name="viewport" content="width=device-width,initial-scale=1.0" 解释](http://www.cnblogs.com/yuzhongwusan/p/4184923.html)
2 [pie用hack方式在ie中实现border-radius](http://css3pie.com/)
