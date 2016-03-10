title: digitalocean下ubuntu切换内核,使用锐速
date: 2016-01-28 16:59:10
tags:
- digitalocean
- Linux
categories:
- 服务器
---
最近在网上看到,锐速可以用来给服务器加速,然后有人用这个软件来给翻墙服务器加速,据说效果还不错,今天就折腾一下.
<!-- more -->
我去锐速官网注册了账号,可惜,在官方支持的linux版本中并没有我这个内核版本.我用的是`3.13.0-37-generic`的`32`位版本,锐速官网并没有支持.`32`位版本支持的相对较少,支持的最新版本是`3.13.0-29-generic`.
![锐速官网linux支持列表](http://ww3.sinaimg.cn/large/692869a3gw1f0fbk1mp65j213c0ozdru.jpg)
所以没办法,只能把我的`ubuntu`降级回去,还是小折腾了一下.下面就来说下降级
# 实验环境：
digitalocean的云服务器
Linux OS:   Ubuntu
Version:    14.04
Kernel:     3.13.0-37-generic
Bits:       32-bit

# 实验步骤
ssh登录服务器,然后安装新内核,blablabla...
你要是这样想就跟我一样,想错了.
因为我们使用的是`digitalocean`的云服务器,不是普通的个人单机,所以那套方法不顶用,我照着那个方法,安装了新的内核,然后更新`grub`,怎么弄都没用,最后才找到答案.
**争取的做法:**
1 登录你的`digitalocean`控制台
2 点击`setting->kernel`,效果如下图
![控制台的kernel页面](http://ww2.sinaimg.cn/large/692869a3gw1f0fbvo209sj20vd0kkdkf.jpg)
3 点击下拉列表,找到你要的`kernel`,比如我要的是`Ubuntu 14.04 x32 vmlinuz-3.13.0-29-generic`,然后点击`change`,等一会儿,页面会自动刷新,内核更新就成功了
4 重要的一步,我就是这步有点问题,又浪费了点时间.更新完内核,我们发现自己的服务器查看内核的时候并没有变化,我们首先想到的是重启服务器.我们直接在终端`sudo reboot`.但是启动以后,你会发现还是没有变化,什么鬼?
真正的答案是,我们现在终端关机,也即是执行`sudo poweroff`,先关机.然后我们在控制台的power选项卡中,有个`power on`的按钮,我们启动服务器,就发现内核已经更新了.
ok 打完收工


# 参考文献
1 [How to Upgrade Ubuntu 12.04 LTS to Ubuntu 14.04 LTS](https://www.digitalocean.com/community/tutorials/how-to-upgrade-ubuntu-12-04-lts-to-ubuntu-14-04-lts)
2 [How To Update a DigitalOcean Server's Kernel](https://www.digitalocean.com/community/tutorials/how-to-update-a-digitalocean-server-s-kernel)
