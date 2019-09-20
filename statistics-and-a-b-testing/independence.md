# Inference about independence

## Discrete Variable

* How to test 2 random variables are independent 

$$2\times2$$ contingency table. For instance, z=0 for male, z=1 for female; y=0 for unemployed y=1 employed. Total data is n. 

$$
\begin{array}{|l|l|l|l|}\hline & {Y=0} & {Y=1} & {} \\ \hline z=0 & {X_{00}} & {X_{01}} & {X_{0.}} \\ \hline z=1 & {X_{10}} & {X_{11}} & {X_{1.}} \\ \hline & {X_{.0}} & {X_{.1}} & {X..=n} \\ \hline\end{array}
$$

Question: Sex & employment status， 2 binary variables. Independent? 

H0: Z is Independent of Y  

Test Statistic: Pearson's Chi Square Statistic for Independence 

$$E_{i j}=\frac{X_{i} X_{j}}{n}$$ , 代表under null hypothesis 时每个cell的数量，

 $$U=\sum_{i=0}^{1} \sum_{j=0}^{1} \frac{\left(X_{i j}-E_{i j}\right)^{2}}{E_{i j}}$$ ，物理意义是理想和观测的差距，

Under the Null Hypothesis, $$U \sim \chi_{1}^{2}$$ 

**注：**

1. **Chi-squared Distribution** k个独立标准正态分布随机变量的平方和就是自由度为k的chi-square distribution
2. Covariance（Y，Z）=0 意思是线性无关，但是它不等于independent  但是independent一定covariance=0 比如 $$cov(x, x^{2})=0$$ 
3. “独立” independence 意味着 $$P(Y=y, Z=z)=P(Y=y)\times P(Z=z)$$ 而等式右边就是marginal probability，也就是 $$E_{i,j}$$ 
4. 所以在Null Hypothesis成立的情况下，X00的那个cell应该有 $$\frac{x_{.0} \cdot x_{0.}}{n\times n} \cdot n$$ 这么多的人数。
5. 假如U过大，理想和观测的差距过大\(p值小\)，reject。
6. df，在binary vs binary时是1； 在test non-binary vs non-binary时不是1. t-test是和sample size有关；chi-square test的df=\(nrow-1\)\*\(ncol-1\)
7. “不独立”也不意味着causality
8. 注意confounding factors，避免Simpson‘s Effect，如果做segmentation的内容本身就对结果有作用，segmentation的effect可能还会大于treatment的effect。所以要保证所区分的group在两组中的人数一样。
9. 如果在面试上是observational study，无法控制sample的比例，那就尽可能找到confounding factors，做穷举control（考查的data intuition）。

* How to estimate the strength of dependence b/w 2 random variables

Odds Ratio： $$\psi=\frac{X_{00} X_{11}}{X_{01} X_{10}}$$ 

注意以上的Test都是Independence Test 不是Correlation Test！！！





## Continuous Variable 

如果两个variables是normally distributed，可以通过correlation来看Independence。

或者，把continuous一个个做binning，做成discrete，又能回到上面的方法。



## One Continuous, One Discrete 

如果面对不同的category，continuous data的分布是一样的， 那就等价于它们的CDF（cumulative density function）是一样的。所以假设检验的test statistic就可以用 $$D=\sup \left|\hat{F}_{1}(x)-\hat{F}_{2}(x)\right|$$ 两个function之间距离的最大值。用KS Test作比较，[链接](https://en.wikipedia.org/wiki/Kolmogorov%E2%80%93Smirnov_test)。

不过更直接的还是画图~



## Hypothesis Testing and Model Selection

用Hypothesis Testing的方式做Model Selection，把一些non-significant coefficients扔了。

Zheng-Loh Model Selection Method

存在的问题： 

1. true model其实是bias=0的model，variance很大

2. 如果是cross-validation，最后evaluate的是model performance。但是这个method 的结果是让model更接近true model。

所以相比之下， 还是cross validation更加简单直接，中间不绕弯子直接奔model performance提升的结果。

