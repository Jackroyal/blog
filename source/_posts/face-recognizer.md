title: 使用opencv实现人脸识别
date: 2015-04-26 10:50:00
tags:
- opencv
- 图像处理
- python
categories:
- 图像处理
---
最近搞了两个星期的opencv，人脸识别方向，感觉没有什么前途，看不到论文在哪里啊。
<!-- more -->
```python
# !/usr/bin/env python
# -*- coding:utf-8-*-
import os
import sys
import cv2
import numpy as np

z = {} # 存储关于每张图片对应的lable
for_pre = []  # 存储用来进行测试的图片，规则是每个人10张图，5张用来训练，5张用来测试
def normalize(X, low, high, dtype=None):
    """对数据进行正常化处理，让其处于最高和最低值之间."""
    X = np.asarray(X)
    minX, maxX = np.min(X), np.max(X)
    # normalize to [0...1].
    X = X - float(minX)
    X = X / float((maxX - minX))
    # scale to [low...high].
    X = X * (high-low)
    X = X + low
    if dtype is None:
        return np.asarray(X)
    return np.asarray(X, dtype=dtype)


def read_images(path, sz=None):
    """从文件夹中读取图像，并且将其大小限制在一定范围之内

    参数:
        path: 图片的路径
        sz: 设定图像的大小以元组的形式，例如(92,112)

    返回值:
        返回一个list的数据[X,y]

            X: 一个numpy的数组，里面存储的是所有的图片的矩阵.
            y:一个list存储的，都是与X中图片对应的lable
    """
    c = 0
    X,y = [], []
    for dirname, dirnames, filenames in os.walk(path):
        for subdirname in dirnames:
            subject_path = os.path.join(dirname, subdirname)
            for filename in os.listdir(subject_path):
                try:
                    im = cv2.imread(os.path.join(subject_path, filename), cv2.IMREAD_GRAYSCALE)
                    # resize to given size (if given)
                    if (sz is not None):
                        im = cv2.resize(im, sz)
                    if y.count(c) > 4:
                        for_pre.append({'no':c,'src':np.asarray(im, dtype=np.uint8)})
                    else:
                        X.append(np.asarray(im, dtype=np.uint8))
                        y.append(c)
                    global z
                    z[os.path.join(subject_path, filename)] = c
                except IOError, (errno, strerror):
                    print "I/O error({0}): {1}".format(errno, strerror)
                except:
                    print "Unexpected error:", sys.exc_info()[0]
                    raise
            c = c+1
    return [X,y]

def prediction(model):
    """图像预测

    参数:
        model: 就是图片训练的那个model

    数据集中每个人存储了10张图片，我把其中的5张存储到for_pre，作为训练数据。用已知的lable和预测的lable作比较，得出图片识别正确的概率
    """
    tn = 0 # 识别正确的图片数
    for item in for_pre:
        [p_label, p_confidence] = model.predict(cv2.resize(item['src'],(92,112)))
        if p_label == item['no']:
            tn = tn+1
        else:
            print 'the answer is %d,' % item['no'],
            print "Predicted label = %d (confidence=%.2f)" % (p_label, p_confidence)

    print "总共有%d次预测，其中正确次数为%d" %(len(for_pre),tn)

if __name__ == "__main__":
    # This is where we write the images, if an output_dir is given
    # in command line:
    out_dir = None
    # You'll need at least a path to your image data, please see
    # the tutorial coming with this source code on how to prepare
    # your image data:
    if len(sys.argv) < 2:
        print "USAGE: face_rec.py </path/to/images> [</path/to/store/images/at>]"
        sys.exit()
    # Now read in the image data. This must be a valid path!
    [X,y] = read_images(sys.argv[1], (92, 112))
 
    # Convert labels to 32bit integers. This is a workaround for 64bit machines,
    # because the labels will truncated else. This will be fixed in code as
    # soon as possible, so Python users don't need to know about this.
    # Thanks to Leo Dirac for reporting:
    y = np.asarray(y, dtype=np.int32)
    # If a out_dir is given, set it:
    if len(sys.argv) == 3:
        out_dir = sys.argv[2]
    # Create the Eigenfaces model. We are going to use the default
    # parameters for this simple example, please read the documentation
    # for thresholding:
    model = cv2.createEigenFaceRecognizer()
    # Read
    # Learn the model. Remember our function returns Python lists,
    # so we use np.asarray to turn them into NumPy lists to make
    # the OpenCV wrapper happy:
    model.train(np.asarray(X), np.asarray(y))
    prediction(model) # 图片预测
    #
    #
    # You can see the available parameters with getParams():
    print model.getParams()
    # Now let's get some data:
    mean = model.getMat("mean")
    print out_dir + '/out.xml'
    f = open(out_dir + '/out.xml','w')
    model.save(out_dir + '/out.xml')
    eigenvectors = model.getMat("eigenvectors")
    # We'll save the mean, by first normalizing it:
    mean_norm = normalize(mean, 0, 255, dtype=np.uint8)
    mean_resized = mean_norm.reshape(X[0].shape)
    if out_dir is None:
        cv2.imshow("mean", mean_resized)
    else:
        cv2.imwrite("%s/mean.png" % (out_dir), mean_resized)
    # Turn the first (at most) 16 eigenvectors into grayscale
    # images. You could also use cv::normalize here, but sticking
    # to NumPy is much easier for now.
    # Note: eigenvectors are stored by column:
    # for i in xrange(min(len(X), 16)):
    #     eigenvector_i = eigenvectors[:,i].reshape(X[0].shape)
    #     eigenvector_i_norm = normalize(eigenvector_i, 0, 255, dtype=np.uint8)
    #     # Show or save the images:
    #     if out_dir is None:
    #         cv2.imshow("%s/eigenface_%d" % (out_dir,i), eigenvector_i_norm)
    #     else:
    #         cv2.imwrite("%s/eigenface_%d.png" % (out_dir,i), eigenvector_i_norm)
    # Show the images:
    print z
    if out_dir is None:
        cv2.waitKey(0)
```

代码执行效果如下
![人脸识别执行效果](https://ww1.sinaimg.cn/large/692869a3gw1eriuc3wsckj20nr08qn2q.jpg)
我在这里输出的是那些预测错误的。`总共有200次预测，其中正确次数为186。`这预测率有点低啊，我用的数据都是来自于[ AT&T Facedatabase](http://www.cl.cam.ac.uk/research/dtg/attarchive/facedatabase.html)。一共40个人，每个人10张图，图片宽高是92*112像素，全部是灰度图像。
至于如何提高图片识别的效率，我也不知道。

# 参考文献
1 <http://docs.opencv.org/modules/contrib/doc/facerec/facerec_tutorial.html>
2 [python调用opencv实现人脸识别](https://code.google.com/p/pythonxy/source/browse/src/python/OpenCV/DOC/samples/python2/facerec_demo.py?repo=xy-27&r=a2e41c7a3cb6db536b948747872cab71c696b44e)
