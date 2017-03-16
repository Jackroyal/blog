title: git使用笔记(2)
date: 2015-11-04 16:52:25
tags:
- Linux
- git
- github
categories:
- Linux
---
上一篇笔记,只写了些git的基础操作,这篇写一下稍微高阶一点的操作,实际项目的时候可能经常会用到.
<!-- more -->

# 撤销操作
撤销操作,我经常会用到,有时候写的代码没有考虑周全,或者有疏漏,需要重新提交什么的.

# 1 撤销工作空间最近的修改 
应用场景:比如上次push以后的完整干净的目录,工作空间没有任何修改,暂存区也没有任何修改,如下图:
![上一次提交后干净的工作目录](https://ww2.sinaimg.cn/large/692869a3gw1exp9lmtz38j20cl0383yn.jpg)
这时候,你手贱,改了一下其中的一个文件,但是这次更改是没有意义的.我们继续使用`git status`来查看
![修改一个文件后执行git status的结果](https://ww4.sinaimg.cn/large/692869a3gw1exp9ngk82jj20it07i75f.jpg)
我们现在想撤销这次修改.
解决方法:现在这个修改只是工作区的修改,我们要撤销很简单执行
```bash
git checkout a.txt
```
搞定

# 2 撤销暂存区最近的修改
应用场景:假如在上面说的那种情况下,你手贱改错了文件,而且你没有撤销,你还执行`git add a.txt`,将修改提交到暂存区了.具体情况如下图
![错误的修改被提交到了暂存区](https://ww2.sinaimg.cn/large/692869a3gw1expa1cbny2j20ge0790td.jpg)
现在我们要从暂存区撤回那个修改
解决办法:我们使用`git reset HEAD a.txt`,将文件从暂存区撤出来,又回到上面的工作空间被修改的状态,如图
![从暂存区撤回](https://ww2.sinaimg.cn/large/692869a3gw1expa3azt15j20hg09twft.jpg)
ps:上面的命令也可以用下面的命令,语法格式是`git reset <path>`,括号中填路径
```bash
git reset a.txt
```
总的来说,`git reset <path>`就是`git add <path>`的反向操作

# 3 撤销提交到本地仓库的修改
应用场景:假如你在`1.2`还没有回头,一路手贱下去,将暂存区的修改`commit`到本地仓库了
![错误的修改被提交到本地仓库](https://ww4.sinaimg.cn/large/692869a3gw1expa8o47yvj20fi087gmv.jpg)
我们现在要从仓库撤销这次没用的提交
解决方法:
#### 1.3.1 方案一  再新建一次提交
我们可以新建一次提交,然后让所修改的文件回到之前的状态,执行
```bash
git revert HEAD
```
含义很简单,逆转,这样你的`log`里面会多一次提交,然后文件也回到的之前的状态
![执行git revert 后的log](https://ww4.sinaimg.cn/large/692869a3gw1expb97iondj20it0catb7.jpg)

#3.2 方案二  干净的撤销上一次commit
上面的方案一对于一个强迫症来说,不能忍啊,本来就是一次错误的提交,为了弥补这个错误,我却还要多提交一次,完全不能忍受.
方案二,我们使用`git reset`命令.我们执行
```
git reset HEAD^
```
这个命令表示重置HEAD指针,我们要指向上一次提交(也就是取消最近的一次,HEAD表示当前指针,HEAD^是上一次,HEAD^^是上上次,HEAD^^^是上三次),`git reset`命令可以添加`--mixed`,`--soft`,`--hard`三种参数,`--soft`程度最轻,只会撤销提交,对当前暂存区和当前工作空间不会有任何更改,默认的参数是`--mixed`,他会将暂存区的文件撤销
```
git reset --soft :取消了commit  

git reset --mixed（默认） :取消了commit ，取消了add

git reset --hard :取消了commit ，取消了add，取消源文件修改
```


# 3.3 方案三  修改最后一次提交
**首先强调,下面说的实在本地仓库的操作.**
应用场景:对于上面提到的错误的提交,我们还可以修改他的内容,删除我们错误的修改,将它恢复到原来的样子,我们可以使用
```bash
git commit --amend
```
这个方法是将当前暂存区快照提交,也就是你后面的修改合在一起,然后再提交.最后查看`log`的时候,会只有一次提交,前面的一次提交就被删除,在`log`中只看到这次提交.
用具体例子来说.
```bash
git commit -m "这是第一次提交"
#commit完,我们修改工作空间的内容,然后再执行
git add b.txt
git commit --amend
#这条命令会弹出一个编辑窗口,你可以修改提交信息
#假设我们将提交信息修改为"这是第二次提交"
```
接着当我们使用`git log`来查看的时候,你只会看到提交信息是"这是第二次提交".
解决方法:
我们先将文件内容恢复到上一次(当前是HEAD,上一次就是HEAD^)提交时候的样子,然后执行`git commit --amend`,看到如下错误提示,就是说这次文件没有任何修改(废话,我已经恢复正确的样子了,当然一模一样了),默认是不允许空提交的,你只有强制使用`--allow-empty`才行,或者执行方案二
![执行git commit --amend内容没有变化的错误提示](https://ww1.sinaimg.cn/large/692869a3gw1expt5luwo4j20io06cq3w.jpg)

# 4 总结
盗图一张,总结上面说的内容
![工作空间,暂存区,本地仓库之间的关机和转换](https://ww2.sinaimg.cn/large/692869a3gw1exptkjlw1fj20jg09fq3k.jpg)
打完收工

### 附言
**PS**:现在再来解释开头说的,这是本地仓库的操作.
因为,我们本地仓库执行了`git commit`后,所做的更改只会在本地,如果接着执行了`git push`那么,所做的更改就会被提交到`git`,在上面的例子中那么首先提交到github的就是`"这是第一次提交"`.这时候,当你执行完`git commit --amend`的时候,本地仓库的上次提交会被删除,也就是不存在提示信息为`"这是第一次提交"`,此时本地仓库完成了修改,你想提交到github,就会报错了.如图所示
![本地执行--amend操作后直接提交到github报错](https://ww1.sinaimg.cn/large/692869a3gw1exp93i04qtj20it0cajua.jpg)
解决方法就是,**Force**
没错,强制提交`git push -f`,这样他就会像本地一样,删除上一次提交,添加这一次提交


# 友情链接
1 [修改提交信息](https://help.github.com/articles/changing-a-commit-message/)
2 [如何撤销在git上的各种修改,好文](https://github.com/blog/2019-how-to-undo-almost-anything-with-git)
3 [git revert和git reset的区别](http://my.oschina.net/MinGKai/blog/144932)
4 [git常用命令介绍,带效果图的](https://marklodato.github.io/visual-git-guide/index-zh-cn.html)



