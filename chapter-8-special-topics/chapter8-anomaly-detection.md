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
* Fin-tech company: Lendingclub, affirm loan application fraud



## Imbalanced Dataset 处理方法

### Resampling 重新采样

更多的数据永远好于算法提高，因为更多的数据就有更多的数据分布信息，更好做出分布估计

### Downsampling / Upsampling 过采样小样本，欠采样大样本

Down-sampling：randomly remove instances in the majority class

缺点是丢数据，可以利用的信息减少了

Up-sampling:randomly replicate instances in the minority class

缺点是可能会污染数据，最后造成overfitting

![](https://cdn.mathpix.com/snip/images/uAQqguOGpU0jlZ6Gf23CTmHw7s_N2s_QB_Aw0hRdRbk.original.fullsize.png)



#### 面试问题：upsample/downsample是否影响AUC？

AUC的物理意义：随机抽取的正样本排在随机抽取的负样本前面的概率

AUC is insensitive to class distribution! 有两个理解方式

1. 正负样本本身是随机抽取的，所以resampling不会改变概率
2. 举个例子来看，比如downsample 0

|  | Prediction 0 | Prediction 1 |
| :--- | :--- | :--- |
| True 0 | TN | FP |
| True 1 | FP | TP |

ROC Curve的x、y轴分别是TPR和FPR

TPR = TP/\(TP+FN\) 

FPR = FP/\(FP+TN\)

downsample时是stratified，TN和FP会等比降低，所以FPR不变；TPR的内容一直没变过，所以也不会变



### 随机采样和非随机采样

### 增加Class Weight/cost-sensitive learning

Class weights：impose a heavier cost when errors are made in the minority class

分类任务是识别那些小类，对分类器的小类样本数据增加权值，降低大类样本的权值从而使得 分类器集中在小类样本身上。在训练分类器时，若分类器将小类样本分错时额外增加分类器一 个小类样本分错代价，这个额外的代价可以使得分类器更加“关心"小类样本。 ·

penalized-SVM ．

penalized-LDA

比如对于fraud detection的情景，FN就应该给更高的惩罚W1，而FP带来用户满意度的下降，也给一定惩罚，稍微低一点W2. 这里的W1和W2可以先empirically根据实际业务损失定数值，然后再cross validation调参。

在哪里用？ 对于classification problem, 常用的cost function \(purity of node\)是Gini Index或者Cross Entropy. 我们希望每个节点分得尽可能纯，也就是entropy或者gini index减小的最快，最后的数值最少。

![](https://cdn.mathpix.com/snip/images/ThvQHrT_yquTPwYyW6UMXQ0x3KLA3Ih3Wrt5mcrqA5g.original.fullsize.png)

在图中就是p取两端的值时对应的y轴数值。

$$
\begin{array}{|c|c|c|}\hline & {\text { Original }} & {\text { Weighted }} \\ \hline \text { Gini } & {\text { p1(1-p)+ p2(1-p2)}}  & {\text {w0*p1(1-p1) }+ w1*p 2(1-p2)} \\ \hline \text { Entropy } & {-p1+\log \left(p1\right)-p2 \text { log }(p2)} & {-w 1* p2* \log (p2)} \\ \hline\end{array}
$$

```python
from skleam.utils.class_weight import compute_class_weight 

class_weights=compute_class_weight('balanced', np.unique(y), y)
```

### 人工数据样本 SMOTE

Synthetic minority sampling technique\(SMOTE\): downsamples the majority class and synthesizes new minority instances by **interpolating** between existing ones 

另外还可以在上面增加一些随机扰动

![](https://cdn.mathpix.com/snip/images/SuF81nqXkjAMcp5_XQB3y_zVoym5uIWc7tz7GS4Oqpg.original.fullsize.png)

![](https://cdn.mathpix.com/snip/images/YBbE21qu4XV0b8eL2zvQK6CGIoYTqbx-ww8EFnDS_TM.original.fullsize.png)



另外SMOTE可以和RF叠加使用

### 尝试使用Decision Tree

### 尝试变换问题

将问题划分成多个更小的问题，而这些小问题更容易解决。

例如： 将大类压缩成小类； 

使用集成方式，训练多个分类器，然后联合这些分类器进行分类；

### 使用Ensemble Method 

首先通过从多数类中独立随机抽取出若干子集 将每个子集与少数类数据联合起来训练生成多个基分类器 最终将这些基分类器组合形成一个集成学习系统

* 数据集大：划分成多个小数据集，学习多个模型进行组合 
* 数据集小：利用Bootstrap方法进行抽样，得到多个数据集，分别训练多个模型再进行组



## 需要看的书

