title: Linux下使用github
date: 2015-01-19 00:53:48
tags:
- Linux
- github
categories:
- Linux
---
>我的惰性真是病入膏肓了，已经整整3周没有在github上提交东西了，今天看了《模拟游戏》，索性又捡起来了。下面来总结一下linux下github的使用，也算是给自己一个备份，因为我自己也是老忘记。
<!-- more -->
使用环境：Ubuntu 14.04
##1 安装git相关软件
我的Ubuntu里面没有自带git相关软件，所以我们首先需要安装它，很简单。
```
sudo apt-get install git 
```
##2 初始化git的设置
接下来，进行初始化设置，也就是设置你的github账号和密码
```
git config --global user.name "zhangsan"#其中的“zhangsan”输入的就是你注册时候的用户名，这步是设置你提交时候默认的用户名
#之后设置提交时候默认的邮箱，在命令行输入：
git config --global user.email "haha@qq.com"
#其中的“haha@qq.com”就是你注册时候用的邮箱，当然也可以用别的邮箱，用别的邮箱的时候你必须在github的主页上设置里面把用的邮箱添加进去
```
##3 开始使用github
1 首先你的github上应该有一个库，如果没有的话，就去github网站上新建一个库，或者fork一个别人的项目，以下的操作都是建立在这个基础上，`假设存在一个库https://github.com/Jackroyal/test.git`.
如果你的github上已经有库了，可以忽略第1步直接进入第2步
2 我们在本地新建一个文件夹(命名随你便，我取名叫做test_git)
```
mkdir test_git
```
3 将远程的库复制下来，我们使用git clone命令来完成
```
#如果你的环境不在test_git目录
#cd test_git
#如果在test_git中
git clone https://github.com/Jackroyal/test.git#他会在你的本地新建一个test文件夹
cd test
#接下来新建一个测试文件
touch test.md
#修改测试文件的内容
vi test.md
#提交刚才所做的更改
git add .
git commit -m "首次提交"
```
3 push提交到github
```
git remote add origin https://github.com/Jackroyal/test.git
git push
```
之后会提示你输入用户名密码，你输入你github账号和密码就行了
打完收工，睡觉
后面会陆续介绍hexo在Linux下的使用
#参考文献
1 [ubuntu 下 github 简单的使用教程](http://blog.chinaunix.net/uid-29040159-id-3799719.html)
2 [Github入门级使用攻略](http://blog.csdn.net/pony_maggie/article/details/23207847)
