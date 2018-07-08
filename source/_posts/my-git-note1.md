title: git使用笔记(1)
date: 2015-11-04 12:17:04
tags:
- Linux
- git
- github
categories:
- Linux
---
git也用了一段时间了,还是写点笔记,记录一下.
<!-- more -->
# 准备条件
首先假装你已经安装好了git
+ 在Linux下
    我用的是`ubuntu`,安装非常easy,使用`sudo apt-get install git`
+ 在windows下
    在windows下安装,就使用[git-downloads](https://git-scm.com/downloads),点击下载,安装,就ok.
ps:如果你足够幸运,可以试试[github的windows版](https://desktop.github.com/),这个的好处是,带有图形化界面.

# 1 初始化git设置
新装好git后,我们先配置个人的用户名和email,这两条配置很重要,因为在git的日志中,都会带上这两条信息.
```bash
$ git config --global user.name "John Doe"
$ git config --global user.email johndoe@example.com
```
如果用了`--global`选项，那么更改的配置文件就是位于你用户主目录下的那个，以后你所有的项目都会默认使用这里配置的用户信息。如果要在某个特定的项目中使用其他名字或者电邮，只要去掉`--global`选项重新配置即可，新的设定保存在当前项目的`.git/config`文件里。

# 2 新建第一个仓库
建立第一个仓库有两种方式,一种是完全新建一个仓库,第二种是克隆一个现有仓库.

#### 2.1 完全新建一个仓库
你先切换到你想建项目的地方,比如`f:/blog/`,然后执行
```bash
git init
```
初始化后再在当前目录新建一个名为`.git`的目录,git所有的东西都存在这里面.当然我们不用管.

#### 2.2 克隆一个现有仓库
这个也是很easy,直接一条命令搞定
```bash
git clone https://github.com/Jackroyal/blog.git
```
他会在你的当前目录新建一个`blog`文件夹,在`blog`文件夹里面他会把相关的文件都下载下来,就可以用了.
# 3 提交更新

#### 3.1 提交工作空间文件到暂存区
执行`touch a.txt`,我们在目录中新建一个文件名叫`a.txt`,然后我们修改它的内容.
接下来,我们来查看下状态
```bash
git status
```
执行结果如下图:
![执行git status查看工作区文件状态](https://ww1.sinaimg.cn/large/692869a3gw1exoxiu5jyij20it0933zl.jpg)
就像图片中提示的,我们新加的`a.txt`是未跟踪文件,我们执行
```bash
git add a.txt
```
我们把`a.txt`添加到暂存区,再次执行`git status`,查看状态,执行结果如图所示
![添加到暂存区后再次执行git status](https://ww2.sinaimg.cn/large/692869a3gw1exoxp6duu1j20it07o0th.jpg)

#### 3.2 将暂存区文件保存到本地仓库
假如你要做的修改已经改完,已经保存到暂存区了,我们准备提交到本地仓库.执行
```
git commit -m "这是提交内容"
```
参数`-m`,就是添加说明,方便以后查看,你就可以知道自己做了哪些更改

#### 3.3 将本地仓库内容提交到远程仓库
上面的步骤都弄好以后,我们提交到远程仓库
```
 git push origin master
```
然后就行了,一个基本的流程就是这样.
推荐看看下面友情链接里面的那本书,pro git 
![pro git封面](https://ww1.sinaimg.cn/large/692869a3gw1exp17u1ltjj208r0boq38.jpg)

# 友情链接
1 [pro git中文书籍在线版](http://iissnan.com/progit/)
