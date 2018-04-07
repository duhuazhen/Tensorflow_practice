# 使用Python实现神经网络
<p> 神经网络/人工神经网络的洋文是Neural Network，这个计算模型在上世纪40年代就出现了，但是直到2011、2012年由于大数据和深度学习的兴起，神经网络才得到广泛应用。</p>
参看wiki神经网络：https://en.wikipedia.org/wiki/Artificial_neural_network<br>
## MNIST数据集简介<br>
当我们学习新的编程语言时，通常第一个程序就是打印输出著名的“Hello World!”。在深度学习中，MNIST数据集就相当于Hello World。

MNIST是一个简单的计算机视觉数据集，它包含手写数字的图像集：<br>
 ![](https://github.com/duhuazhen/Tensorflow_practice/blob/master/Based%20on%20article/3%20RNN/picture/1.png)<br>
 
  MNIST数据集下载地址: [Yann LeCun]http://yann.lecun.com/exdb/mnist/<br>
 <p> 数据集：<br>
train-images-idx3-ubyte  训练数据图像  (60,000)<br>
train-labels-idx1-ubyte    训练数据label<br>
t10k-images-idx3-ubyte   测试数据图像  (10,000)<br>
t10k-labels-idx1-ubyte     测试数据label</p><br>
每张图像是28 * 28像素：<br>

我们的任务是使用上面数据训练一个可以准确识别手写数字的神经网络模型。
 ![](https://github.com/duhuazhen/Tensorflow_practice/blob/master/Based%20on%20article/3%20RNN/picture/2.png)<br>
```python
import numpy as np
import random
 
class NeuralNet(object):
 
    # 初始化神经网络，sizes是神经网络的层数和每层神经元个数
    def __init__(self, sizes):
        self.sizes_ = sizes
        self.num_layers_ = len(sizes)  # 层数
        self.w_ = [np.random.randn(y, x) for x, y in zip(sizes[:-1], sizes[1:])]  # w_、b_初始化为正态分布随机数
        self.b_ = [np.random.randn(y, 1) for y in sizes[1:]] 
```
