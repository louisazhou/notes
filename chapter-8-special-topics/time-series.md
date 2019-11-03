# Time Series

### 考法

concept, procedure, **logic**, data challenge

eg. 什么是平稳过程，为什么

## Type of Analysis

### Exploratory Analysis

* 画图看数据长什么样
* Autocorrelation analysis, examine serial dependence
* Spectral Analysis
* Decomposition of time series

### Curve Fitting

* Interpolation \(smoothing, regression analysis\)
* Extrapolation

### Function Approximation

* Known Target Function 知道function长什么样 做近似
* Curve Fitting 

### Prediction, Forecasting and Classification

Spark-TS as a third party package for large scale data

### Signal Detection

Fourier Transformation 对声音降噪

### Segmentation Analysis

区分对话中不同人的对话声 

## Definition 

### Time Series 

A time series model for observed data $$\left\{x_{t}\right\}$$ is a specification of the joint distribution of a sequence of random variables $$\left\{X_{t}\right\}$$ of which $$\left\{x_{t}\right\}$$ is postulated to be a realization. 

1. Time series研究的是joint distribution，联合概率 
2. Xt是random variable, 一般不知道，可以认为是一种distribution；xt是一次实现，是一个数据

> 比如观测股价一年的变化，我们有每天的股价数据，那么这就是365个变量，我们在试着求这365个变量的联合分布，但是手上却只有365个data point，每个变量只有一个。
>
> 如果是联合概率，完全没有任何限制，有各种情况组合，而我们观测时只看到这些X的一次实现，那这个问题非常复杂。要试着把问题简化，比如，平稳过程，我们认为X1...X5的分布一样，于是就可以用X2的实现推断X1的distribution。不研究X1...X5的联合分布，而是找到一阶expectation value，二阶expectation... 也就是不需要知道distribution，而是通过找到mean和variation分析问题。有时，知道first and second moments就够用。

### Mean Function 

Let $$\left\{X_{t}\right\}$$ be a time series with $$E\left(X_{t}^{2}\right)<\text { inf }$$. The mean function of  $$\left\{X_{t}\right\}$$ is $$\mu_{X}(t)=E\left(X_{t}\right)$$ . 

### Covariance Function

The covariance function of $$\left\{X_{t}\right\}$$ is \(for all integers r and s\)

$$
\gamma_{X}(r, s)=\operatorname{Cov}\left(X_{r}, X_{s}\right)=E\left[\left(X_{r}-\mu_{X}(t)\right)\left(X_{s}-\mu_{X}(s)\right)\right]
$$

### Stationary Process （weakly stationary）\*\*\*\*\*

$$\left\{X_{t}\right\}$$is weakly stationary if 

\(1\) $$\mu_{X}(t)$$ is independent of t \(2\) $$\gamma_{X}(t+h, t)$$ is independent of t for each h

Remark 1: strictly stationary is if the joint distribution is independent of t

Remark 2: $$\gamma_{X}(h)=\gamma_{X}(t+h, t)$$ 

mean function is independent of t, and covariance function is independent of t for each h \(和起点终点无关，和距离有关）所以x1和x3的covariance和x2与x4的covariance相同，以及variance function和时间也无关。只在乎first and second moment.

Why do we care about 'stationary'?: make a prediction, assume something does not change with time

### Examples

1. √  i.i.d. noise: with 0 mean and finite variance  
2. √ white noise: uncorrelated random variables, each with zero mean and finite variance  
   因为uncorrelated意味着只要i≠j，它们的correlation就=0，所以满足了weakly stationary的定义。 

  
   要注意 uncorrelated不意味着independent，就像 $$x, x^2$$ 的例子，或者normal & chi square。因为uncorrelated只是线性无关。所以从iid noise可以推出white noise，但是反过来不行。

3. × Random Walk: $$S_{t}=\sum_{k=0}^{t} X_{i}$$ 其中 $$X_{i}$$ 是i.i.d. noise, 0 mean, finite variance.  其中E\( $$S_{t}$$ \)=0 但是！variance var\($$S_{t}$$\)= \(t+1\) $$\sigma^2$$ 

### AutoCovariance Function \(ACVF\) 

$$
\mathrm{ACVF}: \gamma_{X}(h)
$$

### Auto Correlation Function ACF

$$
\rho_{X}(h)=\gamma_{X}(h) / \gamma_{X}(0)
$$

covariance除以variance, 做standardization 

### Sample Mean

因为数据 $$X_{t}$$ share同一个 $$\mu_{X}(t)$$ , 所以在平稳过程的条件下，Let $$x_{1}, \ldots ., x_{n}$$ be observations of a time series. The sample mean of  $$x_{1}, \ldots ., x_{n}$$ is $$\overline{\mathbf{X}}=\frac{1}{n} \sum_{t=1}^{n} \mathbf{X}_{t}$$ 

### Sample ACVF

Sample auto covariance function is $$\hat{\gamma}(h)=n^{-1} \sum_{t=1}^{n-h}\left(x_{t+|h|}-\bar{x}\right)\left(x_{t}-\bar{x}\right)$$ 

### Sample ACF

The sample auto correlation function $$\hat{\rho}(h)=\hat{\gamma}(h) / \hat{\gamma}(0)$$ 

 

## General Approach to Time Series Modeling（procedure）

1. Plot the series and examine the main features of the graph. Check if there is: 也就是Exploratory  - Trend - Seasonal Component  - Sharp Change in Behavior  - Outlying Observation
2. Remove the trend and seasonal components to get stationary residuals. 获得后先放下，看residual是否还有信息 -  Stationary residual: in between iid and seasonal 
3. Choose a model to fit the residuals to make use of various sample statistics including the sample autocorrelation function.
4. Forecasting is achieved by forecasting the residuals and then inverting the transformation. 

### De-trend

#### Spline Regression 

1. Run a linear regression with time index 
2. Run a polynomial regression with time index  弱点1: non-local, 所以如果在某一个点附近y值有了变化，整个poly就变了 弱点2: 多项式的fit 可能更高项的fit更好，但是却是overfitting 所以针对这样的弱点，弥补方式就是分段 构造k个阶梯函数，找这些阶梯函数的线性组合。 
3. Fit a spline regression 用光滑的曲线，比较没有棱角。

{% embed url="https://www.analyticsvidhya.com/blog/2018/03/introduction-regression-splines-python-codes/" %}

{% embed url="https://docs.scipy.org/doc/scipy/reference/generated/scipy.interpolate.BSpline.html" %}

#### Smoothing 

都是nonparametric methods， 是trend estimation的，不是用于model  building。

* Moving Average

$$
\hat{m}_{t}=(2 q+1)^{-1} \sum_{j=-q}^{q} X_{t-j}
$$

Assume Mt是linear 大多数情况下，只要每个segment足够小，都可以认为是linear的。

* Exponential Smoothing

$$
\hat{m}_{t}=\alpha X_{t}+(1-\alpha) \hat{m}_{t-1}
$$

这个只依赖于过去的值，所以可以用于forecasting；另外最佳的alpha的值是需要subjective judgement然后试的。

* Differencing

$$\begin{array}{l}{\nabla X_{t}=X_{t}-X_{t-1}=X_{t}-B X_{t}=(1-B) X_{t}} \\ {\nabla^{2} X_{t}=(1-B)^{2} X_{t}=X_{t}-2 X_{t-1}+X_{t-2}}\end{array}$$ 

其中B是一个operation，‘back’，delta=1-B。这样简写的时候比较方便。

不研究xt，而是研究delta，因为做差之后就消掉了linear trend；如果说有nonlinear trend，但是在足够小的时候可能可以忽略，再或者如果把差值再减一次，二次方也消了；再多次做差，多次项都可以消。

## First-order moving average or MA\(1\) Process

$$
{X_{t}=Z_{t}+\theta_{1} Z_{t-1}} \\ {\rho_{1}=\frac{\theta_{1}}{1+\theta_{1}^{2}}, \text { and } \rho_{h}=0 \text { for } h \geq 2}
$$

其中 $$Z_{t}$$ 是white noise，theta是一个常数 。

$$\begin{aligned} & Var(a \cdot x)=a^{2} \operatorname{Var}(x) \end{aligned}$$ ， $$\begin{aligned} & Cov(a \cdot x，a\cdot y)=a {Cov}(x,y) \end{aligned}$$ 

Mean Function是0, variance function是 $$\left(1+\theta_{1}^{2}\right) \sigma^{2}$$ ，当距离相差1时，covariance是 $$\theta_{1}\sigma^{2}$$ ，当距离相差大的时候，covariance是0. 所以这些都和时间无关，只和距离有关， 所以这也是stationary process。

correlation function，距离相差1时covariance= $$\theta_{1}\sigma^{2}$$ ，再除以variance，做standardization，得到 $$\rho$$ . 距离大于1时是0.

如果把correlation function画成图，横轴的lag是上面公示的h，纵轴是correlation。

![](https://cdn.mathpix.com/snip/images/sqCBfnl-s6zZ-5oEJV5g91y0unssDNBKmyR3hZXvOqs.original.fullsize.png)

## First-order Autoregression \( AR\(1\) Process\)

当phi的绝对值小于1时构造出来的AR1 Process是stationary process（但在某些其他情况下也有可能stationary）

$$
X_{t}=\varphi X_{t-1}+Z_{t}, \text { where }\left\{Z_{t}\right\} \text { is WN with variance } \sigma^{2} \text { and }|\varphi|<1
$$

起始时x0=z0 

![](../.gitbook/assets/image%20%2830%29.png)



自学列表：

* [ ] 自回归模型 AR
* [ ] 移动平均模型 MA
* [ ] ARIMA
* [ ] LSTM 

## ARIMA

Moving Average 

{% embed url="https://www.datacamp.com/community/tutorials/moving-averages-in-pandas" %}

{% embed url="https://www.learndatasci.com/tutorials/python-finance-part-3-moving-average-trading-strategy/" %}



{% embed url="https://zhuanlan.zhihu.com/p/32085405" %}

{% embed url="https://www.jianshu.com/p/9dc9f41f0b29" %}



