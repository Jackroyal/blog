title: kali安装后设置
date: 2015-03-04 14:14:57
tags:
- kali
- Linux
categories: Linux
---
kali安装好了，还有几件事要做
我们用root的身份登进去系统
<!-- more -->
#1 更新软件源
官方自带的软件源速度相对比较慢，资源也少一些，我们添加一些国内的源进去
vi /etc/apt/sources.list
（可自由选择，不一定要全部）： 
```
#官方源
deb http://http.kali.org/kali kali main non-free contrib
deb-src http://http.kali.org/kali kali main non-free contrib
deb http://security.kali.org/kali-security kali/updates main contrib non-free

#激进源，新手不推荐使用这个软件源
deb http://repo.kali.org/kali kali-bleeding-edge main
deb-src http://repo.kali.org/kali kali-bleeding-edge main

#中科大kali源
deb http://mirrors.ustc.edu.cn/kali kali main non-free contrib
deb-src http://mirrors.ustc.edu.cn/kali kali main non-free contrib
deb http://mirrors.ustc.edu.cn/kali-security kali/updates main contrib non-free

#阿里云kali源
deb http://mirrors.aliyun.com/kali kali main non-free contrib
deb-src http://mirrors.aliyun.com/kali kali main non-free contrib
deb http://mirrors.aliyun.com/kali-security kali/updates main contrib non-free
```
保存之后运行：
`apt-get update `     #刷新系统
`apt-get dist-upgrade `        #安装更新

#2 安装中文输入法和字体
安装字体
`apt-get install ttf-wqy-microhei ttf-wqy-zenhei`
 执行以下命令
 `apt-get install fcitx fcitx-googlepinyin`

#3 安装vpn
kali默认情况下vpn是无法使用的，需要安装相关组件
```
apt-get install network-manager-openvpn
apt-get install network-manager-openvpn-gnome
apt-get install network-manager-pptp
apt-get install network-manager-pptp-gnome
apt-get install network-manager-strongswan
apt-get install network-manager-vpnc
apt-get install network-manager-vpnc-gnome
/etc/init.d/network-manager restart
```

#4 网卡管理显示“device not managed”
`vi  /etc/NetworkManager/NetworkManager.conf`
修改`managed=false`为`managed=true`
然后重启网络管理
`service network-manager restart`
#5 安装chrome浏览器
`apt-get install google-chrome-unstable`
