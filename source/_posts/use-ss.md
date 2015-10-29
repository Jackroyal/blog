title: shadowsocks的安装和配置--在ubuntu和ubuntu中
date: 2015-03-09 22:26:21
tags:
- Linux
- ubuntu
- shadowsocks

categories:
- Linux
---
最近我的vpn一直在抽风，几乎没法正常使用，而且我的chrome也是各种花屏，我快疯了，没办法，只能改用shadowsocks了。
搭建过程分为两部分:服务端和客户端(这里是主要是ubuntu的客户端)。
<!-- more -->
# 1 系统环境
**服务器**:DigitalOcean上的Ubuntu 14.04 LTS
**客户端**:Ubuntu 14.04 LTS

# 2 服务器端安装和配置
这里主要是参考github的[官方说明](https://github.com/shadowsocks/shadowsocks/wiki/Shadowsocks-%E4%BD%BF%E7%94%A8%E8%AF%B4%E6%98%8E)
通过ssh登陆的服务器上去,这里不在赘述如何登陆.
以下主要针对linux服务器,windows服务器查看[这里](https://github.com/shadowsocks/shadowsocks/wiki/Install-Shadowsocks-Server-on-Windows)
## 安装
Linux不同的发行版本执行的命令如下
```
Debian / Ubuntu:

apt-get install python-pip
pip install shadowsocks

CentOS:

yum install python-setuptools && easy_install pip
pip install shadowsocks
```

## 配置
```
sudo vi /etc/shadowsocks.json
```
配置文件的内容大致如下:
```
{
    "server":"你的服务器的ip",
    "server_port":8388,
    "local_address": "127.0.0.1",
    "local_port":1080,
    "password":"你设置的密码",
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open": false
}
```
**参数名称       解释**
server         安装shadowsocks服务器ip
server_port    服务器端口号
local_address  本地服务器默认是127.0.0.1
local_port     本地监听的端口号
password       密码
timeout        超时时间,单位是秒
method         加密方法默认是: "aes-256-cfb"可以用其他加密方法
fast_open      是否使用TCP_FASTOPEN,默认为不使用
workers        number of workers, available on Unix/Linux

## 运行
前台运行的命令
`ssserver -c /etc/shadowsocks.json`
后台运行
`ssserver -c /etc/shadowsocks.json -d start`
`ssserver -c /etc/shadowsocks.json -d stop`
ps:我上述两条命令都会出错,这两条命令来自官方的github,我用的是下面的
`nohup ssserver -c /etc/shadowsocks.json > aa.log`

## 开机自启
我们把它写入/etc/rc.local中就可以完成开机自启动了.
```
sudo vi /etc/rc.local  # 打开rc.local文件
# 然后在exit前面加入下面这一行
# nohup /usr/local/bin/ssserver -c /etc/shadowsocks.json > aa.log
```
ps:这里我之前犯了一个错误,没有写`ssserver`的绝对路径,导致开机无法自启动,但是手动执行的话,又是可以执行的
# 3 客户端的安装和配置
客户端按理说和服务器端类似,安装shadowsocks,但是我的就是这个出了问题.
## 1) 安装相关软件
shadowsocks有各种客户端版本,各个系统都有.在ubuntu下带图形化界面的有shadowsocks-qt5,还可以直接用命令行.

**图形化:**
```
sudo add-apt-repository ppa:hzwhuang/ss-qt5
sudo apt-get update
sudo apt-get install shadowsocks-qt5
```
*PS:*我的电脑安装这个以后,会自动卸载我的chrome
*PPS:*我刚才又试了一次,我在安装了命令行模式的shadowsocks以后,现在不会卸载我的chrome,总算正常了
```
The following extra packages will be installed:
  libbotan-1.10-0 libqrencode3 libqtshadowsocks libzbar0
The following packages will be REMOVED:
  libbotan-1.10-0:i386 libqtshadowsocks:i386 libzbar0:i386
The following NEW packages will be installed:
  libbotan-1.10-0 libqrencode3 libqtshadowsocks libzbar0 shadowsocks-qt5
0 upgraded, 5 newly installed, 3 to remove and 19 not upgraded.
Need to get 1,280 kB of archives.
After this operation, 662 kB of additional disk space will be used.
Do you want to continue? [Y/n] 
```

**命令行模式:**
```
sudo apt-get install python-pip python-dev build-essential 
sudo pip install  pip
sudo apt-get install python-m2crypto
sudo pip install shadowsocks
```
我因为之前在环境中就安装过pip,所以我只需要执行倒数第三个和第四个命令.
但是我的倒数第四个命令`pip install shadowsocks`一直报错:
```
Exception: Traceback (most recent call last):
File "/usr/lib/python2.7/dist-packages/pip/basecommand.py", line 122, in main
  status = self.run(options, args)
File "/usr/lib/python2.7/dist-packages/pip/commands/install.py", line 278, in run
  requirement_set.prepare_files(finder, force_root_egg_info=self.bundle, bundle=self.bundle) 
File "/usr/lib/python2.7/dist-packages/pip/req.py", line 1177, in prepare_files 
  url = finder.find_requirement(req_to_install, upgrade=self.upgrade) 
File "/usr/lib/python2.7/dist-packages/pip/index.py", line 256, in find_requirement
  page_versions.extend(self._package_versions(page.links, req.name.lower())) 
File "/usr/lib/python2.7/dist-packages/pip/index.py", line 432, in _package_versions 
  for link in self._sort_links(links): 
File "/usr/lib/python2.7/dist-packages/pip/index.py", line 422, in _sort_links 
  for link in links: 
File "/usr/lib/python2.7/dist-packages/pip/index.py", line 769, in links 
  for anchor in self.parsed.findall(".//a"):
AttributeError: 'Document' object has no attribute 'findall'

Storing debug log for failure in /root/.pip/pip.log
```
网上搜索一番,在[这里](https://github.com/pypa/pip/issues/1742)找到答案.
解决方法很简单,执行`easy_install pip`,就ok(貌似是把pip重新安装了一次).

## 2) 客户端运行
shadowsocks图形化的比较简单,这里不表.
命令行模式,启动如下:
```
sslocal -s 服务器ip -p 8388 -k 密码
```
启动成功后会有如下输出:
```
2015-03-10 11:12:59 INFO     loading libcrypto from libcrypto.so.1.0.0
2015-03-10 11:12:59 INFO     starting local at 127.0.0.1:1080
```

## 3) 浏览器代理设置
一般来说我们不希望shadowsocks做全局的翻墙,那样,访问国内的速度也会变慢,我们在chome浏览器中安装switchysharp,来管理代理.具体设置如下所示:
![swichysharp设置](http://ww3.sinaimg.cn/large/692869a3gw1eq0k16t5ejj20ne0ihmzh.jpg)


enjoy it!

# 后记

安卓客户端安装,[点我点我](https://apps.evozi.com/apk-downloader/?id=com.github.shadowsocks)

# 参考文献
1 这是一篇好博客 <http://mushapi.com/shadowsocks-install-config-using.html>
2 [shadowsocks使用说明](https://github.com/shadowsocks/shadowsocks/wiki/Shadowsocks-%E4%BD%BF%E7%94%A8%E8%AF%B4%E6%98%8E)
3 [修复我pip问题的一个issue](https://github.com/pypa/pip/issues/1742)

# 致谢
这个网站可以下载google play的apk,对于我等天朝良民来说,可真是个好东西
<https://apps.evozi.com/apk-downloader/?id=com.github.shadowsocks>

我们家[笨笨的博客](http://huirong.github.io)弄好了,欢迎访问
