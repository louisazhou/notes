# Deep Learning

{% embed url="http://speech.ee.ntu.edu.tw/~tlkagk/courses.html" %}



## Basics

### Perceptron 感知机

input, weight, sum, activation, output 

> 面试有可能让写一个perceptron 复习colab code

### 2层NN 

Universal Approximation Theorem: 2层的perceptron可以无限逼近任意连续函数   
但是实际不这么操作，是因为在hidden layer里的perceptron是一个hyper parameter，对于有些问题可能甚至需要无限多neuron 所以不具有可操作性

### Back Propagation \*\*\*\*

目的：学习网络的weight

BP的实质是Gradient Descent, 复合函数求导（chain rule）, 以及 Dynamic Programming

forward propagation: DAG--chain rule

如果e是一个loss function，那就是通过e求偏导找到weight；如果正常运算，其中有几步的chain是重复计算 

## Activation Function

一般有以下性质:

1. 非线性：因为如果是线性，多层和单层是一样的
2. 可微性：因为需要求gradient
3. 单调性：为保证单层网络是凸函数

sigmoid function（实际用的很少了），因为在x很大或很小时gradient是0，gradient descent时不会有更新

tanh

**ReLU \*\*\***：The Rectified Linear Unit, f\(x\)=max\(0,x\), ReLU的导数都是1. 

优点：ReLU得到的SGD的收敛速度比sigmoid或tanh快，因为它是linear，而且ReLu只需要一个阈值就可以得到激活值。

缺点：梯度死亡的情况。如果Bias是很小的值，所以不管weight和x如何相乘，都无法激活。

**Leaky ReLU：**在input&lt;0时给一点value，所以即使bias很小也会有返回值。

**Maxout：**对x做多个线性变换，然后取其中最大的值。可以把relu之类看做maxout的特殊情况。

优点：用分段线性函数拟合任意凸函数  
缺点：参数变多

### Softmax

输出层转换为（0，1）内，可以理解为概率（将vector转化为概率）。

## Overfitting

### Dropout

在做mini-batch SGD的training时，droupout，不同batch在做back propagation的时候randomly不更新一部分neuron的weight（也就是不同batch dropout的node不一样）； 在testing阶段全部都用

* 因为droupout，所以降低了复杂度；
* 因为dropout，对于每个neuron来说train它的data变少（只用了subset of the training sample\) 
* 去掉了一些correlation，因为强迫每个neuron去学习duplicated data

```python
#dropout导致每个layer的输出比原来正常输出变小了，
#所以为了让predict的时候没有scale difference，这里要乘以dropout rate
```

## Initialization

### All Zero Initialization 

缺点是每个neuron的output都是一样的，所以gradient又都一样，之后的N个Neuron就变成了1个neuron，相当于只有一个



## Auto Encode

AutoEncode, 特点是input output的维度一样。可以unsupervised, 不需要label，通过让output=input，用reconstruct error来train weight。如果hidden layer能最小error的reconstruct original input，那么就是压缩了。比如做图像分割，对于每一个图像的pixel给一个label，用encoder-decoder的结构做图像分割。

Keras Functional API: deal with multiple input 

