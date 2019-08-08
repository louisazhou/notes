# Model Evaluation

spam email detection: 

disease/ anomaly detection:  

1. 在计算时，其实是人为引入threshold，以这个threshold计算概率，来给出Precision、Recall、F1
2. 如果我们只想直到分类器的水平，只看precision和recall不是很好的metric。

## ROC Curve

思想：直接定义一个metric，直观给出分类器的概率（与threshold无关的metric，来定义classifier performance）。无关，意味着threshold可以取到任意值，都能评价到model的好坏。通过改变threshold画图得到ROC Curve. 来自EE的统计信号处理的思想，Fourier变换。

y: True Positive Rate

x: False Positive Rate = recall 

在不变的testing data上的performance，此时模型确定，testing data确定，所以evaluation的结果也是确定的值。但是之所以会有一条线，是因为threshold变化。

记四个点

1. （0，0），我们定义的threshold是1，eg 只有当肿瘤大小非常大时才认为它有病，所以所有人都被判断为没病。注意这和分类器的表现没关系，只是threshold高。也正因为如此，false positive rate=0，everything is negative. True positive rate也是零
2. （1，1），我们定义的threshold是0，eg 每个人都认为没病
3. （0，1），最理想情况，没有false positive，所有positive都找对了
4. Equal Error Rate means FPR=FNR

既然越凸越好，可以定义AUC来描述它有多凸（AUC的面积有多大），以此定量来表示。

> 潜在面试题：AUC的值域是\[0.5,1\], 可以被理解为概率。那么，AUC的值是0.6的话，它的物理意义是什么？这个概率的物理意义是什么？
>
> The probability that a randomly-chosen positive example is ranked higher than a randomly chosen negative example. 
>
> Example:
>
>                                A     B     C      D     E
>
> Truth                      0     0      1       0     1
>
> Predict                  0.2  0.3  0.6  0.1  0.7
>
> 排队，        0   D A B C E 1 越靠左，越往前排
>
> 来个人，切一刀，eg D A \| B C E 1 如果天赋好一点的人切对了，切在了 D A  B \| C E 1

> 但要记得切错不是model的锅，因为AUC的定义是：任意一个数据把positive排在negative后面的概率 所以此时 AUC=1 所有的negative其实都在positive的前面
>
> 此时如果我们换一个model排队，0.2 0.3 0.1 0.1 0.7 此时 0 C D A B E 1  不管怎么切都不可能对了，因为这是model自己的问题，此时的AUC&lt;1

还有一个理解误区，ROC curve和数据点的个数有关，点少了画不出来。不！ROC curve 一定可以画得出来，和testing dataset有多大没关系，因为它是通过调整threshold，即使只有5个点，但我们依然可以给10000个threshold. 

那么有了它，怎么选threshold？——一般0.5上下随便选，或者根据use case来调整，也有人选在Equal Error Rate，但其实没啥意义，只是单纯在展示自己知识渊博，换句话就是炫技。



