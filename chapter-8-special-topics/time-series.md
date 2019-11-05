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

### PACF Partial Autocorrelation Function

$$
\alpha_{n}=\operatorname{cor}\left(X_{n}-P\left(X_{n} | X_{n-1}, \ldots, X_{1}\right), X_{0}-P\left(X_{0} | X_{1}, \ldots, X_{n-1}\right)\right)
$$

residual 之间的correlation。eg. 看10.1和10.10之间的correlation，但是不直接比较这两个，因为这中间隔了很多中间信息。所以partial correlation就是把中间的信息挖了。

拿regression类比， $$y=\beta_{0}+\beta_{1} x^{2}$$ 和 $$y=\beta_{0}+\beta_{1} x+\beta_{2} x^{2}$$ ，后者的最后一项的系数beta2就是挖了beta1的结果。



## General Approach to Time Series Modeling（procedure）

1. Plot the series and examine the main features of the graph. Check if there is: 也就是Exploratory  - Trend - Seasonal Component  - Sharp Change in Behavior  - Outlying Observation
2. Remove the trend and seasonal components to get stationary residuals. 获得后先放下，看residual是否还有信息 -  Stationary residual: in between iid and seasonal 
3. Choose a model to fit the residuals to make use of various sample statistics including the sample autocorrelation function.
4. Forecasting is achieved by forecasting the residuals and then inverting the transformation. 

### De-trend

#### 1.Spline Regression    \(Parametric\) 

1. Run a linear regression with time index 
2. Run a polynomial regression with time index  弱点1: non-local, 所以如果在某一个点附近y值有了变化，整个poly就变了 弱点2: 多项式的fit 可能更高项的fit更好，但是却是overfitting 所以针对这样的弱点，弥补方式就是分段 构造k个阶梯函数，找这些阶梯函数的线性组合。 
3. Fit a spline regression 用光滑的曲线，比较没有棱角。（最后还可以用penalized spline regression）

{% embed url="https://www.analyticsvidhya.com/blog/2018/03/introduction-regression-splines-python-codes/" %}

{% embed url="https://docs.scipy.org/doc/scipy/reference/generated/scipy.interpolate.BSpline.html" %}

#### 2.Smoothing \(Non-Parametric\) 

都是nonparametric methods， 是trend estimation的，不是用于model  building。

对于这样的一个time series $$X_{t}=m_{t}+Y_{t}$$ ，假设mt就是trend term

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

#### 3. Differencing

Less parametric, no assumption that the same trend among observation period 

$$\begin{array}{l}{\nabla X_{t}=X_{t}-X_{t-1}=X_{t}-B X_{t}=(1-B) X_{t}} \\ {\nabla^{2} X_{t}=(1-B)^{2} X_{t}=X_{t}-2 X_{t-1}+X_{t-2}}\end{array}$$ 

其中B是一个operation，‘back’，delta=1-B。这样简写的时候比较方便。

不研究xt，而是研究delta，因为做差之后就消掉了linear trend；如果说有nonlinear trend，但是在足够小的时候可能可以忽略，再或者如果把差值再减一次，二次方也消了；再多次做差，多次项都可以消。

### De-Seasonality 

也是做differencing，但是不是和相邻的做，而是根据周期项，峰值减峰值，谷值减谷值。

对于这样的time series $$X_{t}=m_{t}+s_{t}+Y_{t}$$ ，s就是周期项 $$s_{t}=s_{t-d}$$ d就是周期

做differencing， $$\nabla_{d} X_{t}=\left(1-B^{d}\right) X_{t}=m_{t}-m_{t-d}+\nabla_{d} Y_{t}$$ 

除此之外，也可以试着去和sin或者cosine去拟合，或者把季节直接拿出来。

### Beyond Trend and Seasonality 

除了时间序列上的分析之外，也可以去联合其他feature去做regression，因为我们有一项Y，可以是隔壁竞品的价格，可以是股市，可以是温度... 

也可以用RNN先把有时间的部分先解释了，然后剩下的stationary的部分再用时间序列模型继续分析。如何知道Time Series是Stationary的

### Method 1：visualization

### Method 2：split，calculation

把time series的数据分成几份，每份上都算mean, variance, auto correlation. 接着对比. 虽然并不是充分条件，但却是快速的检验方法。

### Method 3：Hypothesis Testing

#### AD Test \(Augmented Dickey Fuller Test\)

H0: 需要验证的是non-stationary;   
所以p很小的时候reject null hypothesis, 然后认为它是stationary。

#### KPSS Test 

H0: 需要验证的是stationary;   
所以p很小的时候reject null hypothesis, 认为它是non-stationary

如果不仅发现是stationary，还发现了是white noise \(uncorrelated\)，那应该就是到此为止，没有其他可以做的了。

## Role of ACF

Auto-correlation function 到底有怎样的作用？

假设一些time series data，它的mean是c，那么这个时候best predictor of X 就是c

假设有n个data，已知其中某一个数据的值（比如x3=4\)，那么最好的predictor是 $$E\left[X_{n+h} | X_{n}=x_{n}\right]$$ 。

如果是一个gaussian分布，这个条件概率就是 $$X_{n+h} | X_{n}=x_{n} \sim N\left(\mu+\rho(h)\left(x_{n}-\mu\right), \sigma^{2}\left(1-\rho(h)^{2}\right)\right)$$ 。这个公式可以make sense, 因为如果我们知道它是100%correlated，那么variance=0；如果我们知道它是uncorrelated，那么今天发生的事和过去没有关系，因为expectation是xn。那么这个时候的best predictor是 $$E\left[X_{n+h} | X_{n}=x_{n}\right]=\mu+\rho(h)\left(x_{n}-\mu\right)$$ 。从公式来看，它和之前的平均、今天的数据、过去和今天之间的关系有关。

但是如果不是高斯分布，我们就试着找best linear predictor. $$a X_{n}+b$$ 形式上和Gaussian的best predictor一样。

总之，ACF很显式的对于prediction起了很重要的作用。

> 面试考公式，PhD考推导

如果获得的数据不只是一天的，还是一系列之前的，那么best linear predictor 就是 $$P_{n} X_{n+h}=a_{0}+a_{1} X_{n}+a_{2} X_{n-1}+\ldots+a_{n} X_{1}$$ ，这里的系数a都是ACF

> 现在的做法是non-paramatric，但是如果有model了，就能more stable，因为多次结果降低了方差。  
>   
> 原来的思路是 数据算ACF，然后算best predictor，这可能算了n多个parameter；  
>   
> 如果有了（正确的）model，那么就是先从数据算model的系数，比如MA里面的theta1，然后有了Xt之后算rho，因为rho是theta1的function，再回来算ACF。  
>   
> 这里也有个tradeoff 因为model会有bias，但是它会降低variance；  
> 而不使用model，没有bias，但是variance更大。

## Classical Time Series Model

### First-order Autoregression \( AR\(1\) Process\)

当phi的绝对值小于1时构造出来的AR1 Process是stationary process（但在某些其他情况下也有可能stationary）

$$
X_{t}=\varphi X_{t-1}+Z_{t}, \text { where }\left\{Z_{t}\right\} \text { is WN with variance } \sigma^{2} \text { and }|\varphi|<1
$$

起始时x0=z0 

![](../.gitbook/assets/image%20%2831%29.png)

### AR\(p\) AutoRegressive process of order P

$$
\varphi(B) X_{t}=Z_{t} \text { and } \varphi(B)=1-\varphi_{1} B-\varphi_{2} B^{2}-\ldots-\varphi_{p} B^{p}
$$

![&#x4ECE;PACF&#x77E5;&#x9053;AR&#x662F;&#x51E0;&#x9636;&#x7684;](https://cdn.mathpix.com/snip/images/WmAHWjCr5ZMFu3XslkFeJilpF0t-x9WL6BdXzOnwsY0.original.fullsize.png)

Note: the bounds are +/-  1.96/sqrt\(n\)

### First-order moving average or MA\(1\) Process

$$
{X_{t}=Z_{t}+\theta_{1} Z_{t-1}} \\ {\rho_{1}=\frac{\theta_{1}}{1+\theta_{1}^{2}}, \text { and } \rho_{h}=0 \text { for } h \geq 2}
$$

其中 $$Z_{t}$$ 是white noise，theta是一个常数 。

$$\begin{aligned} & Var(a \cdot x)=a^{2} \operatorname{Var}(x) \end{aligned}$$ ， $$\begin{aligned} & Cov(a \cdot x，a\cdot y)=a {Cov}(x,y) \end{aligned}$$ 

Mean Function是0, variance function是 $$\left(1+\theta_{1}^{2}\right) \sigma^{2}$$ ，当距离相差1时，covariance是 $$\theta_{1}\sigma^{2}$$ ，当距离相差大的时候，covariance是0. 所以这些都和时间无关，只和距离有关， 所以这也是stationary process。

correlation function，距离相差1时covariance= $$\theta_{1}\sigma^{2}$$ ，再除以variance，做standardization，得到 $$\rho$$ . 距离大于1时是0.

如果把correlation function画成图，横轴的lag是上面公示的h，纵轴是correlation。

![](https://cdn.mathpix.com/snip/images/sqCBfnl-s6zZ-5oEJV5g91y0unssDNBKmyR3hZXvOqs.original.fullsize.png)

### MA\(q\) Moving average with order q

$$
X_{t}=\theta(B) Z_{t} \text { and } \theta(B)=1+\theta_{1} B+\theta_{2} B^{2}+\ldots+\theta_{q} B^{q}
$$

下图是一个 $$X_{t}=Z_{t}+\theta_{1} Z_{t-1}$$ 

![&#x4ECE;ACF&#x80FD;&#x77E5;&#x9053;&#x662F;&#x51E0;&#x9636;&#x7684;&#xFF0C;&#x4F46;&#x662F;PACF&#x5C31;&#x6CA1;&#x7528;](https://cdn.mathpix.com/snip/images/FSJii5ASsSX5GLDJiJoxuOy0i3fYUN-vwSXavGYYU6o.original.fullsize.png)

这里需要注意PACF里lag=2时不是0了。intuition是

$$
\begin{array}{l}{\text { Independent but conditionally dependent }} \\ {\text { Let's say you flip two fair coins }} \\ {\text { A-Your first coin flip is heads }} \\ {\text { B- Your second coin flip is heads }} \\ {\text { C- Your first two flips were the same }} \\ {\text { A and B here are independent. However, A and B are conditionally dependent given C, }} \\ {\text { since if you know C then your first coin flip will inform the other one. }}\end{array}
$$

### ARMA\(p,q\) Process

$$\varphi(B) X_{t}=\theta(B) Z_{t}$$ 

比如一个ARMA\(1,1\) $$X_{t}=\varphi_{1} X_{t-1}+Z_{t}+\theta_{1} Z_{t-1}$$ 

![](https://cdn.mathpix.com/snip/images/G7PElwQn-G3aoVNVXO_jmZIZkTwM84Vi-6NN0NCPUwI.original.fullsize.png)

这里不能从图直接知道几阶了

### ARIMA\(p,d,q\) process

$$\varphi(B)(1-B)^{d} X_{t}=\theta(B) Z_{t}$$ 

Rule 1: if a series has positive autocorrelations out to a high number of lags, then it probably needs a higher order of differencing \(如果一大堆positive 再differencing）

Rule 2：if the lag-1 autocorrelation is zero or negative, or the autocorrelations are all small and patternless, then the series does not need a higher order of differencing. If the lag-1 autocorrelation is -0.5 or more negative, the series may be over-differenced.

以上都是rule of thumb，但是实际操作时 看情况。 

## Model Estimation

简直太多了... 

* Preliminary Estimation
* Yule-Walker Estimation
* Burg's Algorithm
* The Innovation Algorithm
* The Hannan-Rissanen Algorithm
* Maximum Likelihood Estimation

Q: 能用OLS计算AR\(1\)的系数吗？

Least Square/Regression在这里不满足，因为不满足assumption：如果要regression，需要观测之间互相independent 

## Model Diagnostics

* Check the significance of the coefficients 
* Check the ACF of residuals. \(should be non-significant\)
* \*Check the Box-Pierce \([Ljung](https://en.wikipedia.org/wiki/Ljung–Box_test)\) tests for possible residual autocorrelation at various lags
* Check if the variance is non-constant \(use ARCH or GARCH model\)

## More than 1 Model looks OK

* Choose the one with fewest parameters
* Pick lowest standard error
* AIC, BIC, MSE to compare models 

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



