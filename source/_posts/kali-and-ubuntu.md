title: kali和ubuntu双系统安装
date: 2015-03-03 21:17:41
tags:
- kali
- Linux
categories: Linux
---

时间忽快忽慢，一转眼，年过完了，又回来学校了，又一个多月没有push了，重新回到github。今天我们家笨笨给我找了点kali的资料，索性就把kali捡起来，第一步安装kali的系统。
此处背景不再介绍，直奔主题。kali的安装过程和ubuntu的安装过程类似，应该说原理上是一模一样的，只是界面有些不同。我的电脑当前已经安装了win8和ubuntu，现在需要再加一个kali的系统，三系统共存。
大体分为如下几个步骤：
###1 下载kali镜像
###2 刻录u盘
###3 安装kali
<!-- more -->
---
#1 下载kali镜像
这步很简单，我们去kali的官网下载<https://www.kali.org/downloads/>,下载对应的版本。我这里下载的是第一个64位版本（<http://cdimage.kali.org/kali-1.1.0/kali-linux-1.1.0-amd64.iso>）,因为是在ubuntu下，我们可以顺手校验一下文件的hash值，防止文件损坏。
在终端中输入``` sha1sum /home/chen/kali-linux-1.1.0-amd64.iso```
查看输出是否是`40a1fd1d4864e7fac70438a1bf2095c8c1a4e764`，若正确，则第一步完成。
#2 刻录u盘
如果采用硬盘安装的话，我们需要在win下面操作，使用easybcd来编辑grub引导，还需要解压文件，相对比较麻烦，我这里采用u盘安装，相对比较简单。
和安装ubuntu不同，我之前在win下用UltraISO来刻录,
![刻录ubuntu](http://ww3.sinaimg.cn/large/692869a3gw1epsvwgs1rqj20fa0c676e.jpg)如图所示，便捷启动是可以不修改的，直接默认，点击写入就行。我今天在刻录kali的时候，发现这样写的u盘无法启动，开机的时候会提示`failed to boot from USB disk with error: gfxboot.c32: not a COM32R Image boot:`的错误。
解决方法有两个
1 linux的用mkusb，windows的用Win32DiskImager 来制作U盘启动
具体用法：
https://wiki.ubuntu.com/Win32DiskImager/iso2usb
https://help.ubuntu.com/community/mkusb
2 在win下用UltraISO来刻录，记得更改便捷启动的设置，如图所示
![更改便捷启动](http://ww4.sinaimg.cn/large/692869a3gw1epsw409qr3j20go09rjtp.jpg)，点击便捷启动->选择写入新的启动器引导扇区——>syslinus——>写入

*我采用的是法一，我在ubuntu中用mkusb来刻录*
#3 安装kali
下面开始安装kali（以下图片是我用virtualbox虚拟机中安装拍摄的）
##1 引导成功以后，开机画面如图所示
![开机画面](http://ww1.sinaimg.cn/large/692869a3gw1eptme5jxk5j20ne0hcjwf.jpg)
我们选择`Graphical install`,图形化安装，也可以选择`install`那是文字界面安装
##2 选择语言，地区
语言： 选择 `chinese（simplified）简体中文`
地区： 选择 `中国`
##3 配置网络名称和domain
这个代表你的电脑在网络上的名称，比如win默认的就是pc-2000123131，也就是别人在网上邻居中看到你的电脑的名称。我们就用默认的`localhost`
domain我们留空不管他，下一步
##4 设置root密码
我们设置两次一样的密码就行，不要忘记了
![设置root密码](http://ww2.sinaimg.cn/large/692869a3gw1eptmmnvzlnj20ne0hcdih.jpg)
##5 磁盘分区
我们选择第三项`手动`
![磁盘分区选项](http://ww4.sinaimg.cn/large/692869a3gw1eptmp2uw4ij20ne0hc42t.jpg)
**分区**，这里我们一共分`三个区`，一个300M的`/boot`分区，一个2048M的`swap`分区，其他的分为一个`/`（你也可以把/home单独分区出来），因为分区方法类似，所以我只讲一个`/boot`和`swap`分区的步骤
**boot分区**
**注意:**选择**可启动标志**，我们只有/boot设置为`开`，其他分区的这个选项都是`关`
![boot分区设置](http://ww1.sinaimg.cn/large/692869a3gw1eptmu4nonsj20ne0hctbb.jpg)
设置完成后，选择`分区设定结束`，点击继续

**交换空间**
**注意:**交换空间是一种**文件类型**，其他的分区是**属于载点**
![交换空间swap分区设置](http://ww1.sinaimg.cn/large/692869a3gw1eptn2gehkdj20ne0hc77a.jpg)
设置完成后，选择`分区设定结束`，点击继续


等分区完成，就选择`分区设定结束并将修改写入磁盘`，点击继续，就开始安装kali

##6 启动引导
当系统快安装完成的时候，会出现grub安装的选择,如图所示
![交换空间swap分区设置](http://ww3.sinaimg.cn/large/692869a3gw1eptnhakprmj20ns0j7dij.jpg)

因为我的系统是`win8`和`ubuntu`和`kali`多系统共存，当前是由`win`来引导（当前系统由谁引导就看开机看到的第一个`系统选择`是谁的，如果是红色的ubuntu选项，那就说明由ubuntu引导）。
我这里选择否的话，那么我就需要去`win`里面手动添加`kali`的引导（使用easybcd来操作）。
如果你是`ubuntu`来引导系统的话，一样需要手动去添加`kali`的引导（这里我还不会）。
所以我选择`是`，这样装完三个系统都可以正常开机了。


到此所有的安装结束。

#参考文献
1 安装kali 系统 <http://blog.sina.com.cn/s/blog_779dcd090102va9c.html>
2 [kali系统安装图文教程，kali系统安装图文](http://www.bkjia.com/Linuxjc/844530.html)

#致谢
这里，要感谢我最亲爱的笨笨<http://huirong.github.io>







