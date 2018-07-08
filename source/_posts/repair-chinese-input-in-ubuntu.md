---
title: 修复ubuntu终端中文显示和输入的问题
date: 2017-04-24 13:37:01
tags:
- Linux
- ubuntu
categories:
- Linux
---
我在管理服务器的时候,一般在win下面使用xshell和xftp两个软件,来进行管理和上传文件.不过一直有个头疼的问题:如果文件名是中文的,上传之后,在ftp软件中看到的是正常的,在xshell终端中看到的却是乱码;而且,在xshell中无法输入中文,所以我在vi中修改文件的时候一般只使用英文.

昨晚没事,就查了一下资料,修复了这个问题.

# 1 目标
本文的目标主要是修复中文文件名乱码和中文输入的问题,本文的目标不是把整个系统的所有语言体系都改成中文,只是单纯的修复中文输入问题和中文文件名显示问题.

# 2 环境
**服务器环境**: Ubuntu Server 16.04 lts
**本地环境**: windows7
**软件环境**: Xshell 和 Xftp

# 3 修改配置
与中文编码相关的配置主要包括: 本地编码和服务器编码.我们首先设置服务器端编码:

## 3.1 服务器编码配置
首先,我们使用xshell连接登录服务器,然后在终端中输入`locale`命令,得到结果如下:
```bash
$ locale
LANG=
LANGUAGE=C:
LC_CTYPE="POSIX"
LC_NUMERIC="POSIX"
LC_TIME="POSIX"
LC_COLLATE="POSIX"
LC_MONETARY="POSIX"
LC_MESSAGES="POSIX"
LC_PAPER="POSIX"
LC_NAME="POSIX"
LC_ADDRESS="POSIX"
LC_TELEPHONE="POSIX"
LC_MEASUREMENT="POSIX"
LC_IDENTIFICATION="POSIX"
LC_ALL=
```
各个选项的含义如下:
+ 语言符号及其分类(LC_CTYPE)
+ 数字(LC_NUMERIC)
+ 比较和排序习惯(LC_COLLATE)
+ 时间显示格式(LC_TIME)
+ 货币单位(LC_MONETARY)
+ 信息主要是提示信息,错误信息, 状态信息, 标题, 标签, 按钮和菜单等(LC_MESSAGES)
+ 姓名书写方式(LC_NAME)
+ 地址书写方式(LC_ADDRESS)
+ 电话号码书写方式(LC_TELEPHONE)
+ 度量衡表达方式(LC_MEASUREMENT)
+ 默认纸张尺寸大小(LC_PAPER)
+ 对locale自身包含信息的概述(LC_IDENTIFICATION)。

关于`locale`更多的信息,请查看[Locale-wiki](http://wiki.ubuntu.org.cn/Locale)
我们要修复的是中文文件名的显示和中文输入的问题,所以我们接下来只需要设置`LC_CTYPE`即可.

查看系统支持的编码方式:
```bash
locale -a
```
看看输出内容中是否包含`zh_CN.utf-8`,如果不包含的话,我们需要添加这个配置项
```bash
sudo locale-gen zh_CN.utf-8
```
修改后,我的输出内容如下,已经包含`zh_CN.utf-8`
![查看系统支持的编码方式](https://ws1.sinaimg.cn/large/692869a3gy1fexql3wkdej2076047744.jpg)
接下来,修改`LC_CTYPE`的值,使用`export LC_CTYPE='zh_CN.UTF-8'`,命令来修改.
直接执行`export LC_CTYPE='zh_CN.UTF-8'`那么只对当前会话有效,
**所有用户(永久)**:修改/etc/profile
**当前用户(永久)**:修改~/.bashrc

建议直接修改`/etc/profile`文件,这样所有用户都可以使用.
修改完成后,退出登录,重新登录一次,再输入`locale`命令,可以得到如下输出:
```bash
LANG=
LANGUAGE=C:
LC_CTYPE=zh_CN.UTF-8
LC_NUMERIC="POSIX"
LC_TIME="POSIX"
LC_COLLATE="POSIX"
LC_MONETARY="POSIX"
LC_MESSAGES="POSIX"
LC_PAPER="POSIX"
LC_NAME="POSIX"
LC_ADDRESS="POSIX"
LC_TELEPHONE="POSIX"
LC_MEASUREMENT="POSIX"
LC_IDENTIFICATION="POSIX"
LC_ALL=
```
应该就可以输入中文了

## 3.2 本地配置
在windows中,中文的默认编码是GBK,所以为了避免出现在ftp中看着正常,在xshell中看着乱码的问题.我们要修改xftp中的编码方式.打开xftp,文件->属性->选项,勾选上`使用UTF-8编码`.
![修改xftp编码方式](https://ws1.sinaimg.cn/large/692869a3gy1fexreyc5kyj20c007wt8w.jpg)

## 3.3 编码转换
上面的修改完成后,基本可以解决之前提到的目标.新上传的文件肯定是正常的,但是之前上传的文件依旧是乱码,所以我们需要使用工具转换一下.
```bash
sudo apt-get install convmv
```
用法很简单:
```bash
convmv -f GBK -t UTF-8 --notest ./*
```
其中,`-f`表示源编码;`-t`表示目标编码;如果没有`--notest`选项,这条命令只会检测,不会执行;后面跟着需要操作的文件目录;如果需要递归执行,可以加上`-r`选项.

打完收工

# 参考文献
+ [Locale-wiki](http://wiki.ubuntu.org.cn/Locale)
+ [MacOS用iTerm连接到Linux上，不能输入中文](https://segmentfault.com/q/1010000000150673)