title: win8中使用vs2010配置opencv
date: 2015-05-15 19:56:06
tags:
- 软件安装与技巧
- vs2010
- opencv
- win8
categories:
- 软件安装与技巧
---
今天在师兄的帮助下,总算配置好了opencv.
# 1 系统环境和软件版本
操作系统:win8 64位
vs版本:vs2010 32位
opencv版本:opencv 2.4.10
<!-- more -->
# 2 配置opencv
安装vs比较简单,这里不再赘述.
现在说下配置opencv.
网上有很多相关的配置,都是设置环境变量,然后添加库目录,包含目录,blablabla.
这样做的坏处是,每次我新建一个项目,都要重复以上操作,这样岂不是很蛋疼.
如果你移植你的程序给别人,别人也还要去配置这些东西才能够运行,是不是很不方便?
所以,师兄教给我简单粗暴的办法,直接把相关文件手动复制到vs的目录下,这样就没这么多事了.
**缺点:每次都需要手动设置你所需要调用的lib文件**

## 解压opencv到某个目录
这个很简单,双击那个exe,选择一个目录,就会把opencv的文件解压到你设定的那个目录.
我将它解压到`D:/opencv`下.
## 复制include文件夹
复制`D:\opencv\build\include`目录下的`opencv`和`opencv2`
到`C:\Program Files (x86)\Microsoft Visual Studio 10.0\VC\include`.
![opencv解压后的include目录](https://ww3.sinaimg.cn/large/692869a3gw1es5atg7oimj20kw081ta4.jpg)
![复制以后的效果截图](https://ww2.sinaimg.cn/large/692869a3gw1es5az6qsa7j20jc099jtf.jpg)
## 复制lib文件夹
同理,复制lib文件夹
从`D:\opencv\build\x86\vc10\lib`复制到`C:\Program Files (x86)\Microsoft Visual Studio 10.0\VC\lib`
如果你的程序是win32就选择x86,如果你写的程序是64位的,就复制x64的程序
## 复制bin文件夹
复制bin目录下的东西,我们只需要系统找得到就行,所以我们这里采用环境变量的搞法

我们修改path变量,在后面添加`;d:\opencv\build\x86\vc10\bin`,效果如图所示<br>
![修改path变量,添加opencv的bin目录路径](https://ww2.sinaimg.cn/large/692869a3gw1es5bew5vcjj20e10g4go8.jpg)
# 3 写代码调试
代码如下:
```c#
# include <opencv2/opencv.hpp>
# include <iostream>
# include <string>
# ifdef _DEBUG
# pragma comment(lib,"opencv_core2410d.lib")
# pragma comment(lib,"opencv_highgui2410d.lib")
# else
# pragma comment(lib,"opencv_core2410.lib")
# pragma comment(lib,"opencv_highgui2410.lib")
# endif
using namespace cv;
using namespace std;
int main()
{
    Mat img = imread("ie8-2.png");
    if(img.empty())
    {
        cout<<"error";
        return -1;
   }
   imshow("xx的靓照",img);
   waitKey();
}
```
注意一下这段宏设置
```
# ifdef _DEBUG
# pragma comment(lib,"opencv_core2410d.lib")
# pragma comment(lib,"opencv_highgui2410d.lib")
# else
# pragma comment(lib,"opencv_core2410.lib")
# pragma comment(lib,"opencv_highgui2410.lib")
# endif
```
debug和release,分别调用对应的lib
如果你当前是debug模式
```
# pragma comment(lib,"opencv_core2410d.lib")
# pragma comment(lib,"opencv_highgui2410d.lib")
```
这段代码是必须要的,以后调用相应的lib文件,我们都要手动在头部加入这句话才行
否则,你会看到下面这堆东西
```
1>  test.cpp
1>ManifestResourceCompile:
1>  所有输出均为最新。
1>test.obj : error LNK2019: 无法解析的外部符号 "int __cdecl cv::waitKey(int)" (?waitKey@cv@@YAHH@Z)，该符号在函数 _main 中被引用
1>test.obj : error LNK2019: 无法解析的外部符号 "void __cdecl cv::imshow(class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > const &,class cv::_InputArray const &)" (?imshow@cv@@YAXABV?$basic_string@DU?$char_traits@D@std@@V?$allocator@D@2@@std@@ABV_InputArray@1@@Z)，该符号在函数 _main 中被引用
1>test.obj : error LNK2019: 无法解析的外部符号 "public: __thiscall cv::_InputArray::_InputArray(class cv::Mat const &)" (??0_InputArray@cv@@QAE@ABVMat@1@@Z)，该符号在函数 _main 中被引用
1>test.obj : error LNK2019: 无法解析的外部符号 "class cv::Mat __cdecl cv::imread(class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > const &,int)" (?imread@cv@@YA?AVMat@1@ABV?$basic_string@DU?$char_traits@D@std@@V?$allocator@D@2@@std@@H@Z)，该符号在函数 _main 中被引用
1>test.obj : error LNK2019: 无法解析的外部符号 "void __cdecl cv::fastFree(void *)" (?fastFree@cv@@YAXPAX@Z)，该符号在函数 "public: __thiscall cv::Mat::~Mat(void)" (??1Mat@cv@@QAE@XZ) 中被引用
1>test.obj : error LNK2019: 无法解析的外部符号 "public: void __thiscall cv::Mat::deallocate(void)" (?deallocate@Mat@cv@@QAEXXZ)，该符号在函数 "public: void __thiscall cv::Mat::release(void)" (?release@Mat@cv@@QAEXXZ) 中被引用
1>test.obj : error LNK2019: 无法解析的外部符号 "int __cdecl cv::_interlockedExchangeAdd(int *,int)" (?_interlockedExchangeAdd@cv@@YAHPAHH@Z)，该符号在函数 "public: void __thiscall cv::Mat::release(void)" (?release@Mat@cv@@QAEXXZ) 中被引用
1>C:\Users\chenhao\documents\visual studio 2010\Projects\b\Debug\b.exe : fatal error LNK1120: 7 个无法解析的外部命令
1>
1>生成失败。
1>
1>已用时间 00:00:03.71
========== 生成: 成功 0 个，失败 1 个，最新 0 个，跳过 0 个 ==========
```

ok,打完收工!


ps:上述操作完,应该就好了.我还遇到一个奇葩问题
编译和生成解决方案没有问题，但是开始执行就出现应用程序无法正常启动 0xc000007b错误窗口
最后,原来是权限问题,使用管理员权限打开vs就解决了,win7下找兼容性设置,以管理员权限运行
我的win8没有找到兼容性设置,如图所示操作,每次都能以管理员权限打开vs
![win8使用管理员权限运行vs2010](https://ww4.sinaimg.cn/large/692869a3gw1es5bpz7mkqj20mm0giq7k.jpg)

# 参考文献
1 [VS2010+Opencv-2.4.0的配置攻略](http://www.cnblogs.com/freedomshe/archive/2012/04/25/2470540.html)


