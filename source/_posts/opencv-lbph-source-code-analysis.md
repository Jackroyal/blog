title: opencv中LBPH的人脸识别代码分析
date: 2015-05-06 22:32:42
tags:
- 图像处理
- opencv
- lbph
categories:
- 图像处理
---
我开始用opencv的时候，用的是opencv最新的3.0版本。不过我死活找不到LBPH的代码，网上的教程也都是opencv 2.4的，so  我也去下一份2.4的源码来读读。
opencv目前支持一下三种方法来实现人脸识别：
+ **Eigenfaces特征脸createEigenFaceRecognizer()**
+ **Fisherfaces createFisherFaceRecognizer()**
+ **Local Binary Patterns Histograms局部二值直方图 createLBPHFaceRecognizer()**

我今天读了一下LBPH的代码。
自动人脸识别就是如何从一幅图像中提取有意义的特征，把它们放入一种有用的表示方式，然后对他们进行一些分类。<br>特征脸方法描述了一个全面的方法来识别人脸：面部图像是一个点，这个点是从高维图像空间找到它在低维空间的表示，这样分类变得很简单。低维子空间低维是使用主元分析(Principal Component Analysis,PCA)找到的，它可以找拥有最大方差的那个轴。虽然这样的转换是从最佳重建角度考虑的，但是他没有把标签问题考虑进去。想象一个情况，如果变化是基于外部来源，比如光照。轴的最大方差不一定包含任何有鉴别性的信息，因此此时的分类是不可能的。因此，一个使用线性鉴别(Linear Discriminant Analysis,LDA)的特定类投影方法被提出来解决人脸识别问题。其中一个基本的想法就是，使类内方差最小的同时，使类外方差最大。<br>近年来，各种局部特征提取方法出现。为了避免输入的图像的高维数据，仅仅使用的局部特征描述图像的方法被提出，提取的特征(很有希望的)对于局部遮挡、光照变化、小样本等情况更强健。有关局部特征提取的方法有盖伯小波(Gabor Waelets)，离散傅立叶变换(Discrete Cosinus Transform,DCT)，局部二值模式(Local Binary Patterns,LBP)。使用什么方法来提取时域空间的局部特征依旧是一个开放性的研究问题，因为空间信息是潜在有用的信息。
