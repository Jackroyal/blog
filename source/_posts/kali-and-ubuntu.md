title: kali和ubuntu双系统安装
date: 2015-03-03 21:17:41
tags:
- kali
- linux
categories: linux
---

时间忽快忽慢，一转眼，年过完了，又回来学校了，又一个多月没有push了，重新回到github。今天我们家笨笨给我找了点kali的资料，索性就把kali捡起来，第一步安装kali的系统。
此处背景不再介绍，直奔主题。kali的安装过程和ubuntu的安装过程类似，应该说原理上是一模一样的，只是界面有些不同。我的电脑当前已经安装了win8和ubuntu，现在需要再加一个kali的系统，三系统共存。
大体分为如下几个步骤：
1 下载kali镜像
2 刻录u盘
3 安装kali

#1 下载kali镜像
这步很简单，我们去kali的官网下载<https://www.kali.org/downloads/>,下载对应的版本。我这里下载的是第一个64位版本（<http://cdimage.kali.org/kali-1.1.0/kali-linux-1.1.0-amd64.iso>）,因为是在ubuntu下，我们可以顺手校验一下文件的hash值，防止文件损坏。
使用``` sha1sum /home/chen/kali-linux-1.1.0-amd64.iso```
查看输出是否是`40a1fd1d4864e7fac70438a1bf2095c8c1a4e764`，若正确，则第一步完成。
#2 刻录u盘
如果采用硬盘安装的话，我们需要在win下面操作，使用easybcd来编辑grub引导，还需要解压文件，相对比较麻烦，我这里采用u盘安装，相对比较简单。
和安装ubuntu不同，我之前在win下用UltraISO来刻录,![刻录ubuntu](http://ww3.sinaimg.cn/large/692869a3gw1epsvwgs1rqj20fa0c676e.jpg)如图所示，便捷启动是可以不修改的，直接默认，点击写入就行。我今天在刻录kali的时候，发现这样写的u盘无法启动，开机的时候会提示`failed to boot from USB disk with error: gfxboot.c32: not a COM32R Image boot:`的错误。
解决方法有两个
1 linux的用mkusb，windows的用Win32DiskImager 来制作U盘启动
具体用法：
https://wiki.ubuntu.com/Win32DiskImager/iso2usb
https://help.ubuntu.com/community/mkusb
2 在win下用UltraISO来刻录，记得更改便捷启动的设置，如图所示
![更改便捷启动](http://ww4.sinaimg.cn/large/692869a3gw1epsw409qr3j20go09rjtp.jpg)，点击便捷启动->选择写入新的启动器引导扇区——>syslinus——>写入

*我采用的是法一，我在ubuntu中用mkusb来刻录*
#3 安装kali

