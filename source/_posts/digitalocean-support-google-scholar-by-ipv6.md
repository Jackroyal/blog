title: digitalocean配置ipv6
date: 2015-06-16 22:24:18
tags:
- ubuntu
- ipv6
- digitalocean
- shadowsocks
categories:
- Linux
---
之前配置了ubuntu的shadowsocks,用来做代理翻墙.不过,google scholar一直无解,因为digitalocean的ipv4的地址都被google scholar墙掉了.今天(其实离现在已经好多天了),看到一篇文章,很受启发,决定使用ipv6来试试.
<!-- more -->
#前提条件
首先你得有一台digitalocean的vps,建议选择洛杉矶的服务器,
选个5美元的就够了.

#1 新建droplet
如果你以前没有搭建droplet,今天新建的话,比较简单,我们直接启用ipv6支持.
![新建droplet,尤其注意勾选ipv6](http://ww3.sinaimg.cn/large/692869a3gw1etfo3h9svlj211h1tidny.jpg)
注意勾选ipv6,后面的步骤,请参考第3步
#2 修改droplet
我的droplet已经搭建好了,所以只能修改,添加ipv6的支持.
你打开droplet的`setting`,在`network`中应该可以看到`enable ipv6`
![带有启用ipv6选项的页面](http://ww1.sinaimg.cn/large/692869a3gw1etfosgf63kj207w05ujrn.jpg)
启用后,稍后片刻,页面如下.
![在droplet的setting中启用ipv6后的页面](http://ww4.sinaimg.cn/large/692869a3gw1etfohrljtmj20se0i2tb7.jpg)
#3 设置ipv6
当我们启用ipv6支持以后,还需要对服务器进行设置.
##第一步  我们把ipv6的地址添加到网卡中
我们编辑`/etc/network/interfaces`这个文件,使用命令`sudo vi /etc/network/interfaces`
修改后内容如下
```
# This file describes the network interfaces available on your
# system and how to activate them. For more information, see
# interfaces(5).

# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
auto eth0
iface eth0 inet static
        address 你的ipv4
        netmask 255.255.192.0
        gateway 你的ipv4网关
        dns-nameservers 8.8.8.8 8.8.4.4
#以上内容不需要修改,只需要添加下面的部分
iface eth0 inet6 static
    address 上图中的ipv6地址
    netmask 64
    gateway 网关地址
    autoconf 0
    dns-nameservers 2001:4860:4860::8844 2001:4860:4860::8888 209.244.0.3

```
修改完成后,我们重启下服务器(因为我的重启网络服务没有效果,所以我重启了),<br>如果你看到如下图片,就表示你的ipv6配置好了,我们后面来测试一下
![ipv6成功启用后](http://ww2.sinaimg.cn/large/692869a3gw1etfp7pj65dj20hs04fzlv.jpg)
##第二步 测试ipv6 是否配置成功
我们使用ping命令来测试,ping是测试ipv4的,测试ipv6是用ping6,命令如下:
```bash
ping6 ipv6.google.com

PING ipv6.google.com(nuq04s29-in-x0e.1e100.net) 56 data bytes
64 bytes from nuq04s29-in-x0e.1e100.net: icmp_seq=1 ttl=57 time=2.23 ms
64 bytes from nuq04s29-in-x0e.1e100.net: icmp_seq=2 ttl=57 time=1.97 ms
64 bytes from nuq04s29-in-x0e.1e100.net: icmp_seq=3 ttl=57 time=1.95 ms
64 bytes from nuq04s29-in-x0e.1e100.net: icmp_seq=4 ttl=57 time=2.14 ms
64 bytes from nuq04s29-in-x0e.1e100.net: icmp_seq=5 ttl=57 time=1.93 ms

```
如果发包成功,那就可以进行下一步了
#4 修改hosts
因为ipv4无法访问google scholar,所以我们配置下hosts,让所有对访问google学术的,都使用ipv6去访问.执行命令如下:
```bash
sudo vi /etc/hosts
```
我们在hosts文件的后面,添加如下内容:
```
2607:f8b0:4007:805::100f scholar.google.cn
2607:f8b0:4007:805::100f scholar.google.com
2607:f8b0:4007:805::100f scholar.google.com.hk
2607:f8b0:4007:805::100f scholar.l.google.com
```
ok,打完收工,睡觉


#参考文献
1 [一个解决 Google Scholar block DigitalOcean SFO IP 的方法](https://www.v2ex.com/t/163133)
2 [How To Enable IPv6 for DigitalOcean Droplets](https://www.digitalocean.com/community/tutorials/how-to-enable-ipv6-for-digitalocean-droplets)
