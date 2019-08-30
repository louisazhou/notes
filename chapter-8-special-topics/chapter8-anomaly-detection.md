---
description: A.K.A. Outlier Analysis or fraud detection
---

# Anomaly Detection

## 定义和适用领域

识别不符合预期的行为，包括 识别网络攻击、服务器故障、流量异常、信用卡欺诈、刷单、骗购、刷评论、质量检测、药品测试、IOT sensor event（心脏不规律的报警）...  

总之就是不平衡数据集的分类，不拘泥于之前的那些classification problem，所以要基于统计的手段做分类。

 

## 不同类型的异常检测

### 点异常 Point Anomalies

大多数异常都是这个类型

### 上下文异常 Contextual Anomalies

比如，1元店里的100元商品

有个面试题目，用SQL写，是上下文异常中的百分位模型

### 集合异常 Collective Anomalies

比如大量购买高价

### 难点

难点在于1. 正常和异常状态的边界模糊 2. 异常本身会伪装 3. 缺乏训练数据 4. noise像异常值

### 异常检测方法1： Threshold Model or Rule Based Model

包括

固定阈值\(fixed threshold\) 缺陷是 如果数据模式有周期性、趋势性、季节性，无法用固定阈值（比如波士顿的低温）

![](https://cdn.mathpix.com/snip/images/nP7PYQ4BCnzYBnWJ8r4BmVpaHJ3VDOqN71WPf0AvdqU.original.fullsize.png)

移动阈值\(moving threshold\)、时间序列\(time series\)      

一般都是supervised的阈值或者按要求做的预设，经过判断的  线性的或者周期的

![](https://cdn.mathpix.com/snip/images/KnV1xYejcMPomQO28PieEd5OpOQyG3v2D8c_VtqXkMc.original.fullsize.png)



### 异常检测方法2：半监督模型（基于统计）

1. 基于统计的思路，算出mu和sigma，找3sigma值。因为离中心点越远异常可能性越大。不管是一维还是多维数据都可以采用这样的思路。

一维数据$$\begin{array}{c}{\mu=\sum_{i=1}^{n} x_{i} / n} \\ {\sigma^{2}=\sum_{i=1}^{n}\left(x_{i}-\mu\right)^{2} / n}\end{array}$$ 接着算3sigma

多维数据 $$\begin{aligned} \mu_{j} &=\frac{1}{m} \sum_{i=1}^{m} x_{j}^{(i)} \\ \sigma_{j}^{2} &=\frac{1}{m} \sum_{i=1}^{m}\left(x_{j}^{(i)}-\mu_{j}\right)^{2} \end{aligned}$$ 



2. 也可以画boxplot分析， $$\begin{array}{l}{Q_{1}-k\left(Q 3-Q_{1}\right)} \\ {Q 3+k\left(Q 3-Q_{1}\right)}\end{array}$$ 比如规定k=1.5属于中度异常，k=3属于极度异常



3. Mahalanobis Distance 马氏距离

距离的相对性，到底是更接近正常值还是异常值？如果正常和异常有不同的分布密度，需要借助...  

{% embed url="https://zhuanlan.zhihu.com/p/46626607" %}

{% embed url="https://zhuanlan.zhihu.com/p/30367622" %}

{% embed url="https://www.zhihu.com/question/35211238" %}

### 异常检测方法3：监督模型（基于统计）

把被label过是否是outlier的训练集放进来，取一部分放进有着标记正常的训练集样本中，找到一个threshold，用它来检验剩下的那部分（测试集和验证集），看这个threshold是否有效。

这对训练集有一个“正态分布“的假设。但假如不遵循，可以经历变换让它服从gaussian distribution。最后评估threshold时，其实不是在评估100%，而是考虑precision、recall、F1。

潜在问题是 在所选阈值附近的错误率可能会比较高。

### 异常检测方法4：完全无监督模型 K Means（基于距离）

基于距离或临近度的检测

如果数据集没标记、也不follow gaussian，那么第一步就可以试一下K means，看看数据是否在某一些点附近扎堆。 

### 异常检测方法5：Isolation Forest 

对簇做切割，让每个超平面只有一个点，看需要切几次 outlier切的次数少，簇切的次数多

### 异常检测方法6：局部异常因子 Local Outlier Factor （基于密度或聚类）

![](https://cdn.mathpix.com/snip/images/iHY8EN6OJKOG9KOVwm5__KuW3b2EB7UCPDntOHG-rtk.original.fullsize.png)

比如图中的o2。



总之, 

Statistical-based, distance based, density based, and cluster based outlier detection 

## 面的公司

* Payment processing companies: banks, PayPal, Stripe, Square 
* E-Commerce companies: Amazon, Walmart, Wish, Shopify 
* Two-sided Platform companies that involve processing payments: Uber, Airbnb, Lyft 
* Any companies that need to processing credit card payment 
* Fintech company: Lendingclub, affirm loan application fraud



## 需要看的书

