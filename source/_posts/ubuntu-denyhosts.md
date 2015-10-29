title: ubuntu 14.04 安装denyhosts
date: 2015-04-28 20:19:23
tags:
- Linux
- ubuntu
categories:
- Linux
---
# 起因
因为之前服务器两次被黑，妈蛋。今天在上google+的时候，无意中看到一个维护vps的帖子，就顺手把我的服务器维护下。
之前被黑经历也很简单，因为方便管理的缘故，我给root用户设置了密码，没错，是弱口令，一扫不要一分钟就可以出来的那种。
我在那篇帖子中看到denyhosts这货。东西很简单，分析你的日志，如果有人连续登陆几次密码都错误，那么把他的ip添加到denyhost当中，禁止它继续扫描。这货还很多功能，可以设置禁止时间以后，自动解禁；可以配置自动解禁的次数;还可以配置用户密码尝试的次数，给管理员发邮件等。
<!-- more -->
# 安装
## 1、下载源码包,解压源码包
```python
wget http://sourceforge.net/projects/denyhosts/files/denyhosts/2.6/DenyHosts-2.6.tar.gz
tar zxvf DenyHosts-2.6.tar.gz
cd DenyHosts-2.6
```
## 2、安装部署
```python
python setup.py install
cd /usr/share/denyhosts/
cp denyhosts.cfg-dist denyhosts.cfg
cp daemon-control-dist daemon-control
```
## 3、编辑配置文件denyhosts.cfg
```python
sudo vi denyhosts.cfg
```
几个常见的参数如下所示：
```
PURGE_DENY：当一个IP被阻止以后，过多长时间被自动解禁。可选如3m（三分钟）、5h（5小时）、2d（两天）、8w（8周）、1y（一年）；
PURGE_THRESHOLD：定义了某一IP最多被解封多少次。即某一IP由于暴力破解SSH密码被阻止/解封达到了PURGE_THRESHOLD次，则会被永久禁止；
BLOCK_SERVICE：需要阻止的服务名；
DENY_THRESHOLD_INVALID：某一无效用户名（不存在的用户）尝试多少次登录后被阻止；
DENY_THRESHOLD_VALID：某一有效用户名尝试多少次登陆后被阻止（比如账号正确但密码错误），root除外；
DENY_THRESHOLD_ROOT：root用户尝试登录多少次后被阻止；
HOSTNAME_LOOKUP：是否尝试解析源IP的域名；```

一般我们就用默认就好，我们只需要改两个地方：
### 第一个，我们注释掉第12行，启用第15行，修改以后结果如下(原因是ubuntu中的log不在/var/log/secure中，而是在/var/log/auth.log中)
![修改后的denyhosts.cfg](http://ww3.sinaimg.cn/large/692869a3gw1erll3zp3xpj20id0b5q63.jpg)
### 第二个，我们启用第64行，也就是设置，ip被禁止后，禁止5天，这个时间你可以自行设置
去掉前面的`# `,就行了，修改后结果如下
```bash
 63 # purge entries older than 5 days
 64 PURGE_DENY = 5d
 65 ###################################################################### #
 66 
```
## 4、编辑配置文件daemon-control
因为在ubuntu系统中，有些文件不在预设的位置，所以我们需要编辑这个文件
我们只需要改第14行就好了，修改`/usr/bin/denyhosts.py`为`/usr/local/bin/denyhosts.py`，修改后结果如下
```bash
 13 
 14 DENYHOSTS_BIN   = "/usr/local/bin/denyhosts.py"
 15 DENYHOSTS_LOCK  = "/var/lock/subsys/denyhosts"
 16 DENYHOSTS_CFG   = "/usr/share/denyhosts/denyhosts.cfg"
 17 
 18 PYTHON_BIN      = "/usr/bin/env python"
 19 

```
## 5、运行
运行，额，错误一堆啊，我们执行`sudo ./daemon-control start`,然后得到如下错误
```bash
starting DenyHosts:    /usr/bin/env python /usr/local/bin/denyhosts.py --daemon --config=/usr/share/denyhosts/denyhosts.cfg
DenyHosts could not obtain lock (pid: )
[Errno 2] No such file or directory: '/var/lock/subsys/denyhosts'

```
我们可以去`/var/lock`下面的确看不到subsys这个文件夹，那我们就手动创建他
```bash
mkdir -p /var/lock/subsys
```
继续执行`sudo ./daemon-control start`，应该就会成功
ps：如果还不成功，他缺什么文件我们就用`touch`新建这个文件，如果说`file exists`,我们就删除那个文件
## 6、添加到开机启动
第一种是将DenyHosts作为类似apache、mysql一样的服务，这种方法可以通过 /etc/init.d/denyhosts 命令来控制其状态。方法如下：
```
cd /etc/init.d
ln -s /usr/share/denyhosts/daemon-control denyhosts
# 上面的操作就把他变成了一个服务 我们可以使用service denyhosts start来启动服务
# 下面把他添加到开机自启动，我们这里需要安装一个软件来实现
# ubuntu中没有chkconfig命令，网上的教程是用的chkconfig，具体操作查看后面的参考文献1，ubuntu中对应的是update-rc.d，但是不好用，所以我用sysv-rc-conf 
sudo apt-get install sysv-rc-conf
sudo sysv-rc-conf
```
我们设置运行级别2345，编辑以后如下所示
![设置denyhosts开机自启动](http://ww2.sinaimg.cn/large/692869a3gw1erlmrt5ht5j20j80m40zg.jpg)
按`q`，退出并保存

第二种是将Denyhosts直接加入rc.local中自动启动（类似于Windows中的“启动文件夹”）：
```bash
echo '/usr/share/denyhosts/daemon-control start' >> /etc/rc.local
```

# 参考文献
1 [通过DenyHosts阻止SSH暴力攻击教程](http://www.bootf.com/571.html)

2 [/var/log/secure not present in 14.04 ,is there any alternative?](http://askubuntu.com/questions/534324/var-log-secure-not-present-in-14-04-is-there-any-alternative)
