title: ubuntu下使用scrapy抓取cnblogs
date: 2015-04-26 16:52:28
tags:
- python
- scrapy
- 爬虫
categories:
- python学习笔记
---
今天在伯乐在线上看到一篇翻译的博客，讲的是使用scrapy来抓取stackoverflow上的问题，刚好好久没用这个，于是一并捡起来玩一下。
<!-- more -->
#软件安装
我的环境是：ubuntu 14.04 lts
需要安装相关软件
##scrapy
```
pip install Scrapy
```
##PyMongo
```
pip install pymongo

```
##Mongodb
上面安装的是python使用Mongodb的接口，很显然，我们要安装Mongodb才能使用
```
sudo apt-get install mongodb-server
```
至此，要使用的软件都已经安装完毕
#使用scrapy新建工程
使用scrapy新建工程很简单，如下所示，我们新建一个stack的项目，他会在你的当前目录新建一个stack文件夹
```
scrapy startproject stack
```
并且会建成如下所示的目录树结构
```
chen@chen-P31:~$ tree stack
stack
├── stack
│   ├── __init__.py
│   ├── items.py
│   ├── pipelines.py
│   ├── settings.py
│   └── spiders
│       └── __init__.py
└── scrapy.cfg
```
接下来，我们修改items.py的内容，这个文件用于定义存储“容器”，用来存储将要抓取的数据。
```python
from scrapy.item import Item,Field

class StackItem(Item):
    # define the fields for your item here like:
    # name = scrapy.Field()
    title = Field()#我们添加两个字段，我们等会儿会抓取一个标题和url两个字段
    url = Field()
```
接着，还有一个很重要的东西，对，就是我们的蜘蛛，我们在spider目录下，新建一个stack_spider.py文件。顾名思义，这就是我们的蜘蛛。我们需要定义我们爬虫的起点，爬虫的规则等等
```python
from scrapy import Spider
from stack.items import StackItem  #导入我们上面定义的容器类
class StackSpider(Spider):
    name = 'stack'   #定义我们爬虫的名字
    allowed_domains = ["cnblogs.com"]   #规定爬虫爬取的域名
    start_urls = ['http://www.cnblogs.com/geqianst/p/',]   #爬虫工作的起点

    def parse(self, response):#爬虫用来做数据解析的
        questions = response.xpath('//div[@id="myposts"]//a[@id]')
        #xpath选择器，这里的含义是取所有id为myposts的div，在它下面找所有带id的超链接a
        #实际结果是这样的
        #[<Selector xpath='//div[@id="myposts"]//a[@id]' data=u'<a id="PostsList1_rpPosts_TitleUrl_0" hr'>,
        #<Selector xpath='//div[@id="myposts"]//a[@id]' data=u'<a id="PostsList1_rpPosts_TitleUrl_1" hr'>,
        #<Selector xpath='//div[@id="myposts"]//a[@id]' data=u'<a id="PostsList1_rpPosts_TitleUrl_2" hr'>,
        #<Selector xpath='//div[@id="myposts"]//a[@id]' data=u'<a id="PostsList1_rpPosts_TitleUrl_3" hr'>,
        #<Selector xpath='//div[@id="myposts"]//a[@id]' data=u'<a id="PostsList1_rpPosts_TitleUrl_4" hr'>,
        #<Selector xpath='//div[@id="myposts"]//a[@id]' data=u'<a id="PostsList1_rpPosts_TitleUrl_5" hr'>,
        #<Selector xpath='//div[@id="myposts"]//a[@id]' data=u'<a id="PostsList1_rpPosts_TitleUrl_6" hr'>,
        #<Selector xpath='//div[@id="myposts"]//a[@id]' data=u'<a id="PostsList1_rpPosts_TitleUrl_7" hr'>,
        #<Selector xpath='//div[@id="myposts"]//a[@id]' data=u'<a id="PostsList1_rpPosts_TitleUrl_8" hr'>,
        #<Selector xpath='//div[@id="myposts"]//a[@id]' data=u'<a id="PostsList1_rpPosts_TitleUrl_9" hr'>]
        #

        for question in questions:
            item = StackItem()
            item['title'] = question.xpath(
                'text()').extract()[0]
            item['url'] = question.xpath(
                '@href').extract()[0]
            print item
            yield item
```
#测试
ok，上述工作基本完成，我们来测试一下
```bash
scrapy crawl stack
```
还可以这样测试一下，使用shell命令
![用shell测试xpath](http://ww4.sinaimg.cn/large/692869a3gw1erjvd7qeqdj213z0j9h2h.jpg)
妈蛋，我的竟然出错了，输出如下
```
chen@chen-P31:~/stack$ scrapy crawl stack
2015-04-26 16:49:11+0800 [scrapy] INFO: Scrapy 0.24.6 started (bot: stack)
2015-04-26 16:49:11+0800 [scrapy] INFO: Optional features available: ssl, http11
2015-04-26 16:49:11+0800 [scrapy] INFO: Overridden settings: {'NEWSPIDER_MODULE': 'stack.spiders', 'SPIDER_MODULES': ['stack.spiders'], 'BOT_NAME': 'stack'}
Traceback (most recent call last):
  File "/usr/local/bin/scrapy", line 11, in <module>
    sys.exit(execute())
  File "/usr/local/lib/python2.7/dist-packages/scrapy/cmdline.py", line 143, in execute
    _run_print_help(parser, _run_command, cmd, args, opts)
  File "/usr/local/lib/python2.7/dist-packages/scrapy/cmdline.py", line 89, in _run_print_help
    func(*a, **kw)
  File "/usr/local/lib/python2.7/dist-packages/scrapy/cmdline.py", line 150, in _run_command
    cmd.run(args, opts)
  File "/usr/local/lib/python2.7/dist-packages/scrapy/commands/crawl.py", line 60, in run
    self.crawler_process.start()
  File "/usr/local/lib/python2.7/dist-packages/scrapy/crawler.py", line 92, in start
    if self.start_crawling():
  File "/usr/local/lib/python2.7/dist-packages/scrapy/crawler.py", line 124, in start_crawling
    return self._start_crawler() is not None
  File "/usr/local/lib/python2.7/dist-packages/scrapy/crawler.py", line 139, in _start_crawler
    crawler.configure()
  File "/usr/local/lib/python2.7/dist-packages/scrapy/crawler.py", line 46, in configure
    self.extensions = ExtensionManager.from_crawler(self)
  File "/usr/local/lib/python2.7/dist-packages/scrapy/middleware.py", line 50, in from_crawler
    return cls.from_settings(crawler.settings, crawler)
  File "/usr/local/lib/python2.7/dist-packages/scrapy/middleware.py", line 29, in from_settings
    mwcls = load_object(clspath)
  File "/usr/local/lib/python2.7/dist-packages/scrapy/utils/misc.py", line 42, in load_object
    raise ImportError("Error loading object '%s': %s" % (path, e))
ImportError: Error loading object 'scrapy.telnet.TelnetConsole': No module named conch
```
这是什么gui？
还好我有stackoverflow，google一番，找到解决办法（其实这不是最后的解决办法，请往后看）
网上说是twisted的问题，重新安装一下就好了，ok，走起
```
chen@chen-P31:~/stack$ sudo apt-get install twisted
Reading package lists... Done
Building dependency tree       
Reading state information... Done
E: Unable to locate package twisted
chen@chen-P31:~/stack$ sudo apt-get install twisted.conch
Reading package lists... Done
Building dependency tree       
Reading state information... Done
Note, selecting 'python-twisted-conch' for regex 'twisted.conch'
Note, selecting 'python2.7-twisted-conch' for regex 'twisted.conch'
Note, selecting 'python-twisted-conch' instead of 'python2.7-twisted-conch'
The following packages were automatically installed and are no longer required:
  cli-common dockmanager freepats gstreamer1.0-plugins-bad-faad
  gstreamer1.0-plugins-bad-videoparsers libbotan-1.10-0:i386 libcdaudio1
  libdbus-glib2.0-cil libdbus2.0-cil libdbusmenu-glib4:i386
  libdbusmenu-gtk4:i386 libegl1-mesa:i386 libegl1-mesa-drivers:i386 libflite1
  libfluidsynth1 libgbm1:i386 libgconf2.0-cil libgdiplus libgif4
  libgles2-mesa:i386 libglib2.0-cil libgme0 libgmp10:i386
  libgnome-desktop-2-17 libgnome-keyring1.0-cil libgnomedesktop2.20-cil
  libgstreamer-plugins-bad0.10-0 libgstreamer-plugins-bad1.0-0 libgtk2.0-cil
  libicu52:i386 libmimic0 libmms0 libmono-addins0.2-cil libmono-cairo4.0-cil
  libmono-corlib4.0-cil libmono-corlib4.5-cil libmono-data-tds4.0-cil
  libmono-i18n-west4.0-cil libmono-i18n4.0-cil libmono-posix4.0-cil
  libmono-security4.0-cil libmono-sharpzip4.84-cil libmono-sqlite4.0-cil
  libmono-system-configuration4.0-cil libmono-system-core4.0-cil
  libmono-system-data4.0-cil libmono-system-drawing4.0-cil
  libmono-system-enterpriseservices4.0-cil
  libmono-system-runtime-serialization-formatters-soap4.0-cil
  libmono-system-security4.0-cil libmono-system-transactions4.0-cil
  libmono-system-web-applicationservices4.0-cil
  libmono-system-web-services4.0-cil libmono-system-web4.0-cil
  libmono-system-xml-linq4.0-cil libmono-system-xml4.0-cil
  libmono-system4.0-cil libmono-web4.0-cil libmpg123-0 libnotify0.4-cil
  libofa0 libopenal-data libopenal1 libopenvg1-mesa:i386 libqrencode3:i386
  libqt5core5a:i386 libqt5dbus5:i386 libqt5gui5:i386 libqt5network5:i386
  libqt5widgets5:i386 libqtshadowsocks:i386 librsvg2-2.18-cil libslv2-9
  libsoundtouch0 libspandsp2 libsrtp0 libssl1.0.0:i386 libv4l-0:i386
  libv4lconvert0:i386 libvo-aacenc0 libvo-amrwbenc0 libwayland-client0:i386
  libwayland-egl1-mesa:i386 libwayland-server0:i386 libwildmidi-config
  libwildmidi1 libwnck2.20-cil libxcb-icccm4:i386 libxcb-image0:i386
  libxcb-keysyms1:i386 libxcb-randr0:i386 libxcb-render-util0:i386
  libxcb-shape0:i386 libxcb-util0:i386 libxcb-xfixes0:i386 libxcb-xkb1:i386
  libxkbcommon-x11-0:i386 libxkbcommon0:i386 libzbar0:i386 mono-4.0-gac
  mono-gac mono-runtime mono-runtime-common mono-runtime-sgen python-mpd
  python-mutagen python-twisted-names
Use 'apt-get autoremove' to remove them.
The following extra packages will be installed:
  python-pyasn1
The following NEW packages will be installed:
  python-pyasn1 python-twisted-conch
0 upgraded, 2 newly installed, 0 to remove and 6 not upgraded.
Need to get 286 kB of archives.
After this operation, 1,793 kB of additional disk space will be used.
Do you want to continue? [Y/n] 
Get:1 http://mirrors.ustc.edu.cn/ubuntu/ trusty/main python-pyasn1 all 0.1.7-1ubuntu2 [44.2 kB]
Get:2 http://mirrors.ustc.edu.cn/ubuntu/ trusty/main python-twisted-conch all 1:13.2.0-1ubuntu1 [242 kB]
Fetched 286 kB in 0s (1,595 kB/s)         
Selecting previously unselected package python-pyasn1.
(Reading database ... 359746 files and directories currently installed.)
Preparing to unpack .../python-pyasn1_0.1.7-1ubuntu2_all.deb ...
Unpacking python-pyasn1 (0.1.7-1ubuntu2) ...
Selecting previously unselected package python-twisted-conch.
Preparing to unpack .../python-twisted-conch_1%3a13.2.0-1ubuntu1_all.deb ...
Unpacking python-twisted-conch (1:13.2.0-1ubuntu1) ...
Processing triggers for doc-base (0.10.5) ...
Processing 1 added doc-base file...
Processing triggers for man-db (2.6.7.1-1ubuntu1) ...
Setting up python-pyasn1 (0.1.7-1ubuntu2) ...
Setting up python-twisted-conch (1:13.2.0-1ubuntu1) ...
```
安装总算完成，再试一次，妈蛋，又来一个新错误，这是什么gui？？？
```bash
chen@chen-P31:~/stack$ scrapy crawl stack
2015-04-26 16:50:44+0800 [scrapy] INFO: Scrapy 0.24.6 started (bot: stack)
2015-04-26 16:50:44+0800 [scrapy] INFO: Optional features available: ssl, http11
2015-04-26 16:50:44+0800 [scrapy] INFO: Overridden settings: {'NEWSPIDER_MODULE': 'stack.spiders', 'SPIDER_MODULES': ['stack.spiders'], 'BOT_NAME': 'stack'}
Traceback (most recent call last):
  File "/usr/local/bin/scrapy", line 11, in <module>
    sys.exit(execute())
  File "/usr/local/lib/python2.7/dist-packages/scrapy/cmdline.py", line 143, in execute
    _run_print_help(parser, _run_command, cmd, args, opts)
  File "/usr/local/lib/python2.7/dist-packages/scrapy/cmdline.py", line 89, in _run_print_help
    func(*a, **kw)
  File "/usr/local/lib/python2.7/dist-packages/scrapy/cmdline.py", line 150, in _run_command
    cmd.run(args, opts)
  File "/usr/local/lib/python2.7/dist-packages/scrapy/commands/crawl.py", line 60, in run
    self.crawler_process.start()
  File "/usr/local/lib/python2.7/dist-packages/scrapy/crawler.py", line 92, in start
    if self.start_crawling():
  File "/usr/local/lib/python2.7/dist-packages/scrapy/crawler.py", line 124, in start_crawling
    return self._start_crawler() is not None
  File "/usr/local/lib/python2.7/dist-packages/scrapy/crawler.py", line 139, in _start_crawler
    crawler.configure()
  File "/usr/local/lib/python2.7/dist-packages/scrapy/crawler.py", line 46, in configure
    self.extensions = ExtensionManager.from_crawler(self)
  File "/usr/local/lib/python2.7/dist-packages/scrapy/middleware.py", line 50, in from_crawler
    return cls.from_settings(crawler.settings, crawler)
  File "/usr/local/lib/python2.7/dist-packages/scrapy/middleware.py", line 29, in from_settings
    mwcls = load_object(clspath)
  File "/usr/local/lib/python2.7/dist-packages/scrapy/utils/misc.py", line 42, in load_object
    raise ImportError("Error loading object '%s': %s" % (path, e))
ImportError: Error loading object 'scrapy.contrib.memusage.MemoryUsage': No module named mail.smtp
```
最后的最后，我在我们万能的github上找到[答案](https://github.com/scrapy/scrapy/issues/958)，原来是我们没有安装python-twisted，安装一下，世界都美好了
```
sudo apt-get install python-twisted
```
##输出到文件
为了更直观的看到结果，我们将结果输出到一个json文件
```
scrapy crawl stack -o items.json -t json
```
噢耶，第一个爬虫成功
#存储到mongodb
接下来，我们做最后一件事，我们将结果存储到mongodb的数据库中
在这里，我遇到一个大坑，无论是伯乐在线翻译的博客
还是网上搜索到的一般教程，都是使用pymongo.Connection来连接数据库，可是妈蛋，你使用`pip install pymongo`安装的版本都是最新版本3.0.1，那个Connection的写法已经不支持，被丢弃了，擦。
我们来看一下版本，我学到一个新命令`pip show pymongo`，用来查看某一个包的版本的。
![查看pymongo版本](http://ww4.sinaimg.cn/large/692869a3gw1erjct36jnrj20df038dgf.jpg)
在pymongo 3.0的版本中，已经不再支持pymongo.Connection，而是使用pymongo.MongoClient来替代。
##第一步
创建一个用来保存我们抓取数据的数据库。打开`settings.py`,指定管道，然后加入数据库的相关设置
```python

BOT_NAME = 'stack'

SPIDER_MODULES = ['stack.spiders']
NEWSPIDER_MODULE = 'stack.spiders'

# Crawl responsibly by identifying yourself (and your website) on the user-agent
#USER_AGENT = 'stack (+http://www.yourdomain.com)'

ITEM_PIPELINES = ['stack.pipelines.MongoDBPipeline', ]
#关于mongodb的相关设置，包括服务器的ip，端口号，数据库名，表名，
#我也是第一次使用mongodb竟然不需要用户验证信息，而且这表名确实奇怪，叫做MONGODB_COLLECTION
MONGODB_SERVER = "localhost"
MONGODB_PORT = 27017
MONGODB_DB = "stackoverflow"
MONGODB_COLLECTION = "questions"

DOWNLOAD_DELAY = 5  #抓取的延迟

```
##第二步
我们已经能够爬取和解析html数据了，而且已经配置了数据库，接下来，我们通过`pipelines.py`中建立一个管道去连接这两个部分。
我们首先来完成数据库的连接部分
```python
import pymongo

from scrapy.conf import settings
from scrapy.exceptions import DropItem
from scrapy import log


class MongoDBPipeline(object):

    def __init__(self):
        connection = pymongo.MongoClient(
            settings['MONGODB_SERVER'],
            settings['MONGODB_PORT']
        )
        db = connection[settings['MONGODB_DB']]
        self.collection = db[settings['MONGODB_COLLECTION']]
```
接下来定义一个处理函数
```python
    def process_item(self, item, spider):
        for data in item:
            if not data:
                raise DropItem("Missing data!")
        self.collection.update({'url': item['url']}, dict(item), upsert=True)
        log.msg("Question added to MongoDB database!",
                level=log.DEBUG, spider=spider)
        return item

```
ok,搞定，我们再测试一把
```python
scrapy crawl stack
```
##执行效果如下
![mongodb数据库管理](http://ww1.sinaimg.cn/large/692869a3gw1erjv5to5w3j20w70g8wio.jpg)
#参考文献
1 [ImportError: Error loading object 'scrapy.contrib.memusage.MemoryUsage': No module named mail.smtp](https://github.com/scrapy/scrapy/issues/958)
2 <http://stackoverflow.com/questions/8671071/error-to-execute-python-scrappy-module>
3 [Python下用Scrapy和MongoDB构建爬虫系统（1）](http://python.jobbole.com/81320/)
