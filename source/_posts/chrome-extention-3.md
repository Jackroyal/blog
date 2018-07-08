title: 'chrome扩展开发手记-chrome扩展开始第一步manfest.json'
date: 2014-11-24 15:16
tags:
- chrome
- cmcc-edu
- 扩展
- 脚本
- 中国移动
categories:
- chrome扩展
---
参考文档   [https://lmk123.duapp.com/](https://lmk123.duapp.com/)

下面开始写chrome扩展了,第一步就是看文档,官方文档有入门指导,先要过一遍

我们首先来编写第一个文件manfest.json,这是一个清单文件,告诉系统,我有哪些文件,需要申请哪些权限都在里面,下卖弄给出我的manfest.json





```javascript
{
    "name":"cmcc520",//扩展的名称
    "version":"2.0",//扩展的版本号,随便写
    "description":"powered by 搁浅St",//扩展的描述,就是在chrome://extension页面中,扩展名称下面的几个字
    "background": {//background.js,整个扩展的运行,有个页面在后台运行
        "scripts": ["js/function.js","bg.js"],
        "persistent": false
    },
    "page_action":{//比较常见有page_action和browser_action有,page会出现在地址栏末端,browser会在浏览器上显示一个图标
        "default_icon":"icon-48.png",
        "default_popup": "popup.html",//popup.html点击弹出的页面
        "default_title":"cmcc520"
    },
    "permissions" : [//申请内容脚本的权限,在插入内容到页面时,需要用哪些权限,此处必须声明
        "tabs", "http://\*/\*"
    ],
    "icons" : {//图标设置
        "48" : "icon-48.png",
        "128" : "icon-128.png"
    },
    "commands": {//快捷键设置
        "logout_cmcc": {
            "description": "退出CMCC登录",
            "suggested_key": {
                "default": "Alt+X"
            }
        }
    },
    "manifest_version":2//manifest_version,现在版本1已经不支持,统一都写成2

}

```


简单的属性,这里不再赘述,我重点说一下background,page_action,permissions和commands.

background  它是一个包含扩展程序主要逻辑的不可见页面。扩展程序也可以包含其他页面，展现扩展程序的用户界面。如果扩展程序需要与用户加载的网页交互（相对于包含在扩展程序中的页面），扩展程序必须使用内容脚本。后台网页分两种：持续运行的后台网页与事件页面(设置persistent属性)。正如它们的名称所述，持续运行的后台网页保持打开状态，事件页面根据需要打开与关闭。除非您绝对需要您的后台网页一直运行，请首选事件页面。


```javascript
    "background": {//background.js,整个扩展的运行,有个页面在后台运行
        "scripts": ["js/function.js","bg.js"],
        "persistent": false
    },
```
比如我的这个 后台执行就是这样的

![](https://img.blog.csdn.net/20141124194616706?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSmFja3JveWFs/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


采用pageaction还是browseraction取决于你是否想让图标一直可见.`![](https://img.blog.csdn.net/20141124193151343?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSmFja3JveWFs/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

如图,绿色的是广告终结者,他的图标是pageAction,后面的印象笔记还有switchproxy是browserAction.一般来说,适用于少数页面的扩展建议使用pageaction,适用于大多数页面的建议使用browseraction






permissions属性,用来设置和conten_script相关的选项,举个例子,如果你想插入一段代码到页面中去执行,操作页面上的dom,那就写个content_script就行了,使用 content_scripts 字段，扩展程序可以向一个页面中插入多个内容脚本，每个内容脚本可以有多个
 JavaScript 和 CSS 文件,详细情况请看这里[https://lmk123.duapp.com/extensions/content_scripts](https://lmk123.duapp.com/extensions/content_scripts)

我现在在做的一个扩展就是做这样的事,百度的搜索结果会先跳到一个百度的网址,然后才是目标网址,我可以使用cotent_scripts来插入脚本,修改百度搜索结果的链接,让他直接跳转到目标页面,而不是百度的搜索结果页面.







commands选项是设置快捷键,然后给快捷键设置事件 监听,这样我们按快捷键就会执行相应操作.


```javascript
        "logout_cmcc": {//某个快捷操作的名称
            "description": "退出CMCC登录",//快捷操作的描述
            "suggested_key": {//默认推荐的快捷键,不能与系统默认的快捷键冲突,否则就会失灵,需要用户手动去设置
                "default": "Alt+X"
            }
        }
```



