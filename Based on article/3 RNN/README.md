# 使用Python实现神经网络

RNN(Recurrent Neural Networks)循环神经网络要相对复杂，它引入了循环，能够处理数据有前后关系的问题，常用在自然语言处理上。  

RNN介绍：

[Wiki：Recurrent neural network](https://en.wikipedia.org/wiki/Recurrent_neural_network "悬停显示")  
[Understanding-LSTMs](http://colah.github.io/posts/2015-08-Understanding-LSTMs/ "悬停显示")  
[循环神经网络(RNN, Recurrent Neural Networks)介绍](https://blog.csdn.net/heyongluoyao8/article/details/48636251  "悬停显示")  
唇语识别论文：https://arxiv.org/pdf/1611.05358v1.pdf  
[自己动手做聊天机器人教程（入门级)](https://github.com/warmheartli/ChatBotCourse "悬停显示")   
NN的目的使用来处理序列数据。在传统的神经网络模型中，是从输入层到隐含层再到输出层，层与层之间是全连接的，每层之间的节点是无连接的。但是这种普通的神经网络对于很多问题却无能无力。例如，你要预测句子的下一个单词是什么，一般需要用到前面的单词，因为一个句子中前后单词并不是独立的。RNNs之所以称为循环神经网路，即一个序列当前的输出与前面的输出也有关。具体的表现形式为网络会对前面的信息进行记忆并应用于当前输出的计算中，即隐藏层之间的节点不再无连接而是有连接的，并且隐藏层的输入不仅包括输入层的输出还包括上一时刻隐藏层的输出。  

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
 ![](https://github.com/duhuazhen/Tensorflow_practice/blob/master/Based%20on%20article/3%20RNN/picture/3.png)<br>
 上图网络的构造方法：  
 ```python
 net = NeuralNet([3, 4, 2])
print('权重: ', net.w_)
print('biases: ', net.b_)
```
 ```python
 权重:  [array([[-0.03149996,  3.24885342,  0.89417842],
              [-0.53460464, -1.5079955 ,  1.82663781],
              [-1.65116615,  0.38629484, -0.41583065],
              [-0.01554273,  0.07004582,  0.21980528]]),
       array([[ 0.14899583,  0.51091601,  1.49353662, -0.14707524],
              [ 0.64196923,  1.37387519,  0.92405086,  0.68889039]])]
biases:  [array([[ 0.06612406],
                 [-0.5104788 ],
                 [ 0.62980541],
                 [-0.9225445 ]]),
          array([[-0.26442039],
                 [-0.91214809]])]
```
定义Sigmoid函数：  
 ```python
# Sigmoid函数，S型曲线，
    def sigmoid(self, z):
        return 1.0/(1.0+np.exp(-z))
    # Sigmoid函数的导函数
    def sigmoid_prime(self, z):
        return self.sigmoid(z)*(1-self.sigmoid(z))
 ```
 画出这个函数图像：  
```python
 import numpy as np
from matplotlib import pyplot
 
def sigmoid(z):
    return 1.0/(1.0+np.exp(-z))
 
x  = np.linspace(-8.0,8.0, 2000)
y = sigmoid(x)
pyplot.plot(x,y)
pyplot.show()
 ```
  ![](https://github.com/duhuazhen/Tensorflow_practice/blob/master/Based%20on%20article/3%20RNN/picture/4.png)<br>
  上面使用Sigmoid函数做为神经网络中的激活函数，其作用就是引入非线性。它的优点在于输出范围有限(0, 1)，所以数据在传递的过程中不容易发散。可选择的激活函数有很多。  
  定义feedforward函数：  
```python
# 给神经网络的输入x，输出对应的值
    def feedforward(self, x):
        for b, w in zip(self.b_, self.w_):
            x = self.sigmoid(np.dot(w, x)+b)
        return x
 ```
 定义随机梯度下降函数，赋予神经网络学习的能力：    
 反向传播算法：  
 ```python
 # training_data是训练数据(x, y); epochs是训练次数; mini_batch_size是每次训练样本数; eta是learning rate
    def SGD(self, training_data, epochs, mini_batch_size, eta, test_data=None):
        if test_data:
            n_test = len(test_data)
 
        n = len(training_data)
        for j in range(epochs):
            random.shuffle(training_data)
            mini_batches = [training_data[k:k+mini_batch_size] for k in range(0, n, mini_batch_size)]
            for mini_batch in mini_batches:
                self.update_mini_batch(mini_batch, eta)
            if test_data:
                print("Epoch {0}: {1} / {2}".format(j, self.evaluate(test_data), n_test))
            else:
                print("Epoch {0} complete".format(j)) 
   def backprop(self, x, y):
        nabla_b = [np.zeros(b.shape) for b in self.b_]
        nabla_w = [np.zeros(w.shape) for w in self.w_]
 
        activation = x
        activations = [x]
        zs = []
        for b, w in zip(self.b_, self.w_):
            z = np.dot(w, activation)+b
            zs.append(z)
            activation = self.sigmoid(z)
            activations.append(activation)
 
        delta = self.cost_derivative(activations[-1], y) * \
            self.sigmoid_prime(zs[-1])
        nabla_b[-1] = delta
        nabla_w[-1] = np.dot(delta, activations[-2].transpose())
 
        for l in range(2, self.num_layers_):
            z = zs[-l]
            sp = self.sigmoid_prime(z)
            delta = np.dot(self.w_[-l+1].transpose(), delta) * sp
            nabla_b[-l] = delta
            nabla_w[-l] = np.dot(delta, activations[-l-1].transpose())
        return (nabla_b, nabla_w)
 
    def update_mini_batch(self, mini_batch, eta):
        nabla_b = [np.zeros(b.shape) for b in self.b_]
        nabla_w = [np.zeros(w.shape) for w in self.w_]
        for x, y in mini_batch:
            delta_nabla_b, delta_nabla_w = self.backprop(x, y)
            nabla_b = [nb+dnb for nb, dnb in zip(nabla_b, delta_nabla_b)]
            nabla_w = [nw+dnw for nw, dnw in zip(nabla_w, delta_nabla_w)]
        self.w_ = [w-(eta/len(mini_batch))*nw for w, nw in zip(self.w_, nabla_w)]
        self.b_ = [b-(eta/len(mini_batch))*nb for b, nb in zip(self.b_, nabla_b)]
 
    def evaluate(self, test_data):
        test_results = [(np.argmax(self.feedforward(x)), y) for (x, y) in test_data]
        return sum(int(x == y) for (x, y) in test_results)
 
    def cost_derivative(self, output_activations, y):
        return (output_activations-y)
 ```
 预测：  
 ```python
  def predict(self, data):
        value = self.feedforward(data)
        return value.tolist().index(max(value))
 ```
 加载MNIST数据集：  
 ```python
 # http://g.sweyla.com/blog/2012/mnist-numpy/
import os, struct
from array import array as pyarray
from numpy import append, array, int8, uint8, zeros
 
def load_mnist(dataset="training_data", digits=np.arange(10), path="."):
 
    if dataset == "training_data":
        fname_image = os.path.join(path, 'train-images-idx3-ubyte')
        fname_label = os.path.join(path, 'train-labels-idx1-ubyte')
    elif dataset == "testing_data":
        fname_image = os.path.join(path, 't10k-images-idx3-ubyte')
        fname_label = os.path.join(path, 't10k-labels-idx1-ubyte')
    else:
        raise ValueError("dataset must be 'training_data' or 'testing_data'")
 
    flbl = open(fname_label, 'rb')
    magic_nr, size = struct.unpack(">II", flbl.read(8))
    lbl = pyarray("b", flbl.read())
    flbl.close()
 
    fimg = open(fname_image, 'rb')
    magic_nr, size, rows, cols = struct.unpack(">IIII", fimg.read(16))
    img = pyarray("B", fimg.read())
    fimg.close()
 
    ind = [ k for k in range(size) if lbl[k] in digits ]
    N = len(ind)
 
    images = zeros((N, rows, cols), dtype=uint8)
    labels = zeros((N, 1), dtype=int8)
    for i in range(len(ind)):
        images[i] = array(img[ ind[i]*rows*cols : (ind[i]+1)*rows*cols ]).reshape((rows, cols))
        labels[i] = lbl[ind[i]]
 
    return images, labels
 
def load_samples(dataset="training_data"):
    image,label = load_mnist(dataset)
    #print(image[0].shape, image.shape)   # (28, 28) (60000, 28, 28)
    #print(label[0].shape, label.shape)   # (1,) (60000, 1)
    #print(label[0])   # 5
 
    # 把28*28二维数据转为一维数据
    X = [np.reshape(x,(28*28, 1)) for x in image]
    X = [x/255.0 for x in X]   # 灰度值范围(0-255)，转换为(0-1)
    #print(X.shape)
 
    # 5 -> [0,0,0,0,0,1.0,0,0,0]      1 -> [0,1.0,0,0,0,0,0,0,0]
    def vectorized_Y(y):
        e = np.zeros((10, 1))
        e[y] = 1.0
        return e
    # 把Y值转换为神经网络的输出格式
    if dataset == "training_data":
        Y = [vectorized_Y(y) for y in label]
        pair = list(zip(X, Y))
        return pair
    elif dataset == 'testing_data':
        pair = list(zip(X, label))
        return pair
    else:
        print('Something wrong')
 ```
