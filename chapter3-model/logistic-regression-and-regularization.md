# Logistic Regression & Regularization

## Logistic Regression

1. 直观理解，Linear Regression和Logistic Regression的区别，或者说，我们为什么要使用logistic?

如果我们在做一个基于肿瘤大小预测恶性肿瘤的预测，在数据组A中使用了我们唯一会的模型linear regression，拟合了这样一条曲线，似乎还不错。此时来了一个新的数据，我们依然用这个linear model，为了让model更make sense，这条线需要斜率变小。 但如果计算它此时的loss function（比如就用least square），就会很糟糕...  因为linear regression对于extreme observations比较敏感（不管它是不是outlier）。

2. 从1的例子中知道，linear regression把每一个数据点以相同的权重来对待，这并不是我们想要的。“区别对待“

