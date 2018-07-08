---
title: 将hexo部署到自己的个人服务器上
date: 2017-04-08 19:22:22
tags:
- Nginx
- ubuntu
- Linux
- hexo
categories:
- Hexo
---
> 本文假设你已经配置好了hexo了,这是前提

前面两篇文章主要是用来配置自己的服务器.服务器部署完成后,后面就可以开始写博客了.但是每次`hexo d`推送到gihub的服务器上了,还需要手动将文件复制到自己的服务器对应网站的根目录,对的,要手动,当然不能忍.
# 1 ugly的解决办法
于是我写了一个脚本,在服务器上定时去访问我的github,如果检测到文件有变动,就把github的内容clone到服务器上,然后再布置到网站根目录.
代码很简单,就这几行.主要是检测`/archives/index.html`这个文件的大小是否变化了,选择它,是由于这个文件较小,而且每次发布新文章大小都会发生变化.
```bash
#!/bin/sh
cd ~/blogsync
length=`ls -l github.json| awk '{print $5}'`
curl -o github.json  https://raw.githubusercontent.com/huirong/huirong.github.io/master/archives/index.html
newlength=`ls -l github.json| awk '{print $5}'`
test $length -eq $newlength && exit 0 #if same so quit
git clone https://github.com/huirong/huirong.github.io.git huirongis_me
rm -fr ~/www/huirongis_me
mv huirongis_me ~/www/
```
这样搞法,是不需要手动了,但是做不到实时,而且,如果你没有新增或删减文章,其他的改动,可能不会反应在`/archives/index.html`,那他就不会自动去更新了.

# 2 完美的搞法
既然有ugly的解决方法,自然有完美的解决方法.本文主要是参考 [使用 Git Hook 自动部署 Hexo 到个人 VPS](http://www.swiftyper.com/2016/04/17/deploy-hexo-with-git-hook/).
这个方法的原理,用一句话总结就是:**在自己的服务器上搭建一个git服务器,然后依靠`git hook`程序,将git仓库代码更新到自己的网站根目录**.

## 2.1 在vps上安装git
我的服务器是ubuntu 16.04 lts,安装git服务非常简单
```bash
sudo apt-get install git
```

## 2.2 创建git用户
这一步的原因是,我们需要一个小权限的账号来操作git.比如我们后面会限制这个git账号,不让他登录shell等等.
```bash
sudo adduser git
```
我们在**服务器**的ubuntu执行这条命令就好了,非常简单,而且还帮你在`home`下建立了对应的目录.
接下来要配置`git`用户的认证,有两种办法:
+ 给`git`用户设置一个密码,以后每次执行`hexo d`的时候,会要求你输入密码,才能继续操作
+ 给`git`用户配置ssh证书,当把公钥传到服务器后,每次`hexo d`免密,直接操作完成

## 2.3 设置密码登录
设置密码使用Linux命令`passwd`即可
```bash
sudo passwd git
```
上述命令执行完成后,按照提示输入两次密码,即配置完成

## 2.4 配置ssh证书
配置ssh证书,设置的时候比较麻烦,但是后面每次使用比较简单.后面再说原因.

### 2.4.1 生成ssh证书
首先,我们需要生成证书,这一步我们在自己本地电脑进行.**注意**:生成证书不在服务器上操作,在自己**本地电脑**上进行.

**Mac或者Linux**
对于Mac或者LInux用户,直接使用系统自带的终端就好
**windows**:
对于windows用户,hexo配置完成后,你应该有一个执行shell命令的软件,比如我的是github desktop自带的shell程序,或者你自己安装的git shell,也是可以的,大概长这样
![git shell程序界面](https://ws1.sinaimg.cn/large/692869a3gy1fefka44bdmj20gj094glp.jpg)

首先,我们打开shell程序,输入以下命令
```bash
ssh-keygen -t rsa -b 4096 -C "你的email地址"
```
接着提示让我们输入文件保存的路径,如果你不输入就按照默认的保存,输入完成,回车
下一步,提示我们输入密码,这个密码只是证书的密码,即,当使用这个证书登录的时候要输入的密码,它不是服务器用户的密码,而是单纯给ssh证书设置的密码.
我这里选择不设置,直接回车,提示确认起码,再回车
![生成证书](https://ws1.sinaimg.cn/large/692869a3gy1fefkzl7f04j20eq07wq34.jpg)
证书生成完毕.
我们进入刚才证书保存的目录,可以看到`id_rsa`和`id_rsa.pub`两个文件(也有可能是其他名字,反正一个有pub一个没pub),分别就是私钥和公钥.

**PS**:其实生成证书这一步,在服务器上,或者任何一个机器上都可以,只要你最后把这个公钥传到服务器,用这个私钥链接你的服务器就行,这里为求简单,在本地的git shell中生成

### 2.4.2 上传公钥
接下来,我们还是在**本地终端**操作.将上一步生成的`id_rsa.pub`文件内容追加到服务器的`authorized_keys`中.
OpenSSH提供了一个命令`ssh-copy-id`,直接将文件添加到服务器,简直不能更方便
用法如下:
```bash
ssh-copy-id git@yourserver
```
比如我输入`ssh-copy-id -i ~/.ssh/id_rsa.pub git@bblove.me`,这里的yourserver可以输入域名或者ip,`-i`用来指定具体的公钥文件,适用于存在多个公钥或者找不到公钥的情况,最好手动指定它.输入该命令后,会要求你输入服务器上`git`用户的密码.

![authorized_keys文件内容](https://ws1.sinaimg.cn/large/692869a3gy1feflmcy70yj20g5070ab1.jpg)

如果此法不通,你可以直接在服务器上编辑`authorized_keys`文件,把`id_rsa.pub`内容追加到文件后面即可
请务必确认`authorized_keys`文件的权限是`rw-------`,如果不是,使用`chmod 600 authorized_keys`来修改.

### 2.4.3 测试ssh连接
接下来,我们还是在**本地终端**操作.我们测试一下,看是否能成功连接到服务器.主要使用`ssh -T git@yourserver`命令来测试,此处`git`是指用户名,`yourserver`填你的服务器域名或者ip即可.测试结果如下:
![个人vps的ssh测试结果](https://ws1.sinaimg.cn/large/692869a3gy1fek9m0bm0wj20fs0573yk.jpg)

如果测试不成功,他会返回给你错误提示.如果像我上图这样,那就是测试成功了,需要手动`ctr+c`,让git终端回复到正常输入命令的状态.下面附上github的测试结果,与我的vps略有不同,反正都是成功的.
![github的测试结果](https://ws1.sinaimg.cn/large/692869a3gy1fek9nadydfj20g102adfr.jpg)
### 2.4.4 ssh测试失败
如果你测试失败,可能有多方面的原因,客户端或者服务器都有可能.

#### 2.4.4.1 客户端ssh配置
接下来,还是在**本地终端**操作.我们首先检查一下客户端ssh的配置,文件的位置在`/etc/ssh/ssh_config`,我们使用`vi /etc/ssh/ssh_config`命令打开和编辑该文件.本文服务器域名是`bblove.me`,请检查你的`ssh_config`文件,是否存在一条`Host *`记录,并且该记录的`IdentityFile`值,是否是你刚才生成的那个私钥的地址.
这个配置文件的意思是,针对某个`Host`,程序使用`IdentityFile`指定的私钥文件去进行ssh连接.
如果我们上一步测试ssh连接失败,有可能就是针对我们自己的域名,我们未指定正确的私钥文件.附上我的`ssh_config`文件的内容,
![修改ssh_config文件](https://ws1.sinaimg.cn/large/692869a3gy1fefmq9g0tsj20gj094aad.jpg)

这也就意味着,我们可以针对多个网站各自配置不同的ssh证书,比如我github一套,自己服务器一套.当然偷懒的话,也可以所有网站使用一套ssh证书.只需要添加配置`Host *`的配置即可.你的配置文件内容可能和我的不同,关注下`host`和`IdentityFile`的内容就好.
![ubuntu中ssh_config文件](https://ws1.sinaimg.cn/large/692869a3gy1fefmtf7x24j20ie0kxjsn.jpg)
**PS**:如果host配置了`*`,请务必保证它在其他域名配置的后面,因为系统是从前到后匹配,所以`*`必须在具体域名的后面.

#### 2.4.4.2 服务器ssh配置
接下来,我们在**服务器**上操作.我们检查一下服务器端ssh配置是否有误.文件的位置在`/etc/ssh/sshd_config`,我们使用`sudo vi /etc/ssh/sshd_config`打开和编辑该文件.例如我的ubuntu虚拟机的内容经过修改后,就是这样的:
```bash
# Package generated configuration file
# See the sshd_config(5) manpage for details

# What ports, IPs and protocols we listen for
#这个是用来指定ssh端口的,不用改
Port 22
# Use these options to restrict which interfaces/protocols sshd will bind to
#ListenAddress 10.105.224.192
#ListenAddress 10.105.224.192
Protocol 2
# HostKeys for protocol version 2
HostKey /etc/ssh/ssh_host_rsa_key
HostKey /etc/ssh/ssh_host_dsa_key
HostKey /etc/ssh/ssh_host_ecdsa_key
HostKey /etc/ssh/ssh_host_ed25519_key
#Privilege Separation is turned on for security
UsePrivilegeSeparation yes

# Lifetime and size of ephemeral version 1 server key
KeyRegenerationInterval 3600
ServerKeyBits 1024

# Logging
SyslogFacility AUTH
LogLevel INFO

# Authentication:
LoginGraceTime 120
PermitRootLogin no
StrictModes yes

RSAAuthentication yes
PubkeyAuthentication yes
AuthorizedKeysFile	%h/.ssh/authorized_keys

# Don't read the user's ~/.rhosts and ~/.shosts files
IgnoreRhosts yes
# For this to work you will also need host keys in /etc/ssh_known_hosts
RhostsRSAAuthentication no
# similar for protocol version 2
HostbasedAuthentication no
# Uncomment if you don't trust ~/.ssh/known_hosts for RhostsRSAAuthentication
#IgnoreUserKnownHosts yes

# To enable empty passwords, change to yes (NOT RECOMMENDED)
PermitEmptyPasswords no

# Change to yes to enable challenge-response passwords (beware issues with
# some PAM modules and threads)
ChallengeResponseAuthentication no

# Change to no to disable tunnelled clear text passwords
PasswordAuthentication yes

# Kerberos options
#KerberosAuthentication no
#KerberosGetAFSToken no
#KerberosOrLocalPasswd yes
#KerberosTicketCleanup yes

# GSSAPI options
#GSSAPIAuthentication no
#GSSAPICleanupCredentials yes

X11Forwarding yes
X11DisplayOffset 10
PrintMotd no
PrintLastLog yes
TCPKeepAlive yes
#UseLogin no

#MaxStartups 10:30:60
#Banner /etc/issue.net

# Allow client to pass locale environment variables
AcceptEnv LANG LC_*
AllowUsers chen git zhou
Subsystem sftp /usr/lib/openssh/sftp-server

# Set this to 'yes' to enable PAM authentication, account processing,
# and session processing. If this is enabled, PAM authentication will
# be allowed through the ChallengeResponseAuthentication and
# PasswordAuthentication.  Depending on your PAM configuration,
# PAM authentication via ChallengeResponseAuthentication may bypass
# the setting of "PermitRootLogin without-password".
# If you just want the PAM account and session checks to run without
# PAM authentication, then enable this but set PasswordAuthentication
# and ChallengeResponseAuthentication to 'no'.
UsePAM yes
Ciphers aes128-cbc,aes192-cbc,aes256-cbc,aes128-ctr,aes192-ctr,aes256-ctr,3des-cbc,arcfour128,arcfour256,arcfour,blowfish-cbc,cast128-cbc
MACs hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-ripemd160,hmac-sha1-96,hmac-md5-96
KexAlgorithms diffie-hellman-group1-sha1,diffie-hellman-group14-sha1,diffie-hellman-group-exchange-sha1,diffie-hellman-group-exchange-sha256,ecdh-sha2-nistp256,ecdh-sha2-nistp384,ecdh-sha2-nistp521,diffie-hellman-group1-sha1,curve25519-sha256@libssh.org
UseDNS no
```

我们只需要重点关注下这几个值,
+ `PermitRootLogin no`,也就是不允许使用root登录,这是为了安全起见
+ `AuthorizedKeysFile	%h/.ssh/authorized_keys`,指定公钥存放的文件,这个要重点关注下,注意去掉前面的注释

其他的配置,基本不用修改.以上配置修改完成后,我们重启一下服务器端的ssh服务,使用命令`sudo service ssh restart`即可.最后,我们再回到**客户端**,测试一下ssh连接是否成功.`ssh -T git@yourserver`.还不行的话,你就去google查一查错误

## 2.5 配置服务器git仓库
上一步,我们配置好了客户端ssh连服务器,这一步我们回到**服务器**上去操作.
```bash
#在服务器新建一个文件夹,用来存放git仓库
sudo mkdir /home/chen/bblove
cd /home/chen/bblove
#进入文件夹,对git仓库进行初始化
sudo git init --bare blog.git
#初始化完成后,进入hooks文件夹
cd /home/chen/bblove/blog.git/hooks
#新建一个钩子程序,这个文件名可以自定义,我取名为post-receive
vi post-receive
```
我们在post-receive中,写入如下代码
```bash
#!/bin/sh
git --work-tree=/home/chen/www/bblove_me --git-dir=/home/chen/bblove/blog.git checkout -f
```
此处,`--work-tree`指的是你的网站根目录,`--git-dir`指的是你的git仓库的目录.
编辑完城后,`wq`退出并保存`post-receive`文件.下面给钩子程序加上可执行权限
```bash
#给钩子程序加上可执行权限
sudo chmod +x /home/chen/bblove/blog.git/hooks/post-receive
```
最后,修改git仓库权限,否则客户端无法提交代码到这个仓库
```bash
#接下来修改git仓库的权限
sudo chown -R git:git blog.git
```
此处还要注意一点,我们还要修改网站根目录的权限,否则钩子程序执行的时候,无法修改网站根目录的内容.
```bash
sudo chown -R git:git /home/chen/www/bblove_me
```
至此,服务器端配置完成

## 2.6 修改hexo的配置文件
终于,走到了这一步.
我们打开我们自己hexo博客根目录的`_config.yml`文件.
```bash
deploy:
- type: git
  repo: 'https://github.com/Jackroyal/Jackroyal.github.io.git'
  branch: master
- type: git
  repo: git@bblove.me:/home/git/bblove/blog.git
  branch: master
```
因为,hexo本身是支持多点发布的,所以我们每次deploy的时候,让他同时推到github和自己的服务器上去.
你只需要改`  repo: git@bblove.me:/home/git/bblove/blog.git`这一行,`git@yourserver:/your-git-dir`.
ok,大功告成啦,赶快`hexo d -g`看看效果吧

# 参考文献
+ [使用 Git Hook 自动部署 Hexo 到个人 VPS](http://www.swiftyper.com/2016/04/17/deploy-hexo-with-git-hook/)
+ [Linux启动或禁止SSH用户及IP的登录](http://blog.csdn.net/linghe301/article/details/8211305#)