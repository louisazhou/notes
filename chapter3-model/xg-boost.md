---
description: 对decision tree做boosting
---

# XG Boost and Light GBD

## Recap supervised learning

![](../.gitbook/assets/image%20%2823%29.png)

Kernels: kernel trick, 在svm、knn、linear regression中都可以用到。空间映射。

混合feature：NN、SVM、KNN对于“数值和categorical mixed”的情况不太行，需要转化，但是Decision Tree不会因此受到影响。

缺失值：NN和SVM受missing value影响比较敏感，尤其SVM，如果missing是在kernel附近的。但如果k选的足够好，就对missing value不敏感。

计算复杂度：NN、SVM、KNN的计算复杂度都很大，而tree都是和数据点的个数有关的。

线性关系：NN和SVM都能捕捉到feature中的线性关系，因为本质是做线性变换。而tree考察的是不同属性，不知道不同属性之间的联系。

可解释性：tree model还有一个feature importance，但是其他model没有什么可解释性。

从表的比较来看，tree除了某些点之外，其他都还不错~ 





## XGBoost

eXtreme Gradient Boosting 其实也是对上一节的Gradient Boosting的一种实现。但是因为Gradient Boosting每一步对所有数据求梯度、每次构建一个新的tree加入原模型序列，比较慢；XG Boost因为这几个原因相比之下会更快：

Parallelization： 训练时可以用所有的 CPU 内核来并行化建树。 

Distributed Computing ： 用分布式计算来训练非常大的模型。 

Out-of-Core Computing： 对于非常大的数据集还可以进行 Out-of-Core Computing。 

Cache Optimization of data structures and algorithms： 更好地利用硬件。



{% embed url="http://wepon.me/files/gbdt.pdf" %}







## Ref Links

1. [Chen Tianqi的原paper](http://delivery.acm.org/10.1145/2940000/2939785/p785-chen.pdf?ip=24.38.219.89&id=2939785&acc=CHORUS&key=4D4702B0C3E38B35%2E4D4702B0C3E38B35%2E4D4702B0C3E38B35%2E6D218144511F3437&__acm__=1567967546_74d92e2d4377b729191fb4188a5b00fe)
2. [YouTube 2016.6.2 Talk](https://www.youtube.com/watch?v=Vly8xGnNiWs&feature=youtu.be) and [Slides](https://speakerdeck.com/datasciencela/tianqi-chen-xgboost-overview-and-latest-news-la-meetup-talk?slide=54)
3. [Code](https://blog.csdn.net/aliceyangxi1987/article/details/72969146)   [详解&参数](https://www.cnblogs.com/webRobot/p/8099314.html)  [调参](https://www.jianshu.com/p/6a706c50afdf)  [调参](https://www.analyticsvidhya.com/blog/2016/03/complete-guide-parameter-tuning-xgboost-with-codes-python/)   [调参](https://www.analyticsvidhya.com/blog/2016/02/complete-guide-parameter-tuning-gradient-boosting-gbm-python/)
4. [Guolin Ke vs Tianqi Chen 讨论](https://www.zhihu.com/question/51644470/answer/130946285)
5. [源码阅读](http://mlnote.com/2016/10/29/xgboost-code-review-with-paper/)
6. [XGBoost Python Feature Walkthrough](https://github.com/dmlc/xgboost/tree/master/demo/guide-python)
7. [LightGBM源码解读和文字介绍](https://www.hrwhisper.me/machine-learning-lightgbm/)



