# Chapter4 Statistics and A/B Testing

## Statistics 

![](../.gitbook/assets/image%20%286%29.png)

### **Descriptive Methods**

**shape:** histogram

**center:**         

* **median：**robust，对outlier友好。      
* **mean：**（1）当sample size很大时，sample mean服从高斯分布。这是一个非常好的性质！（2）更好的性质是，中心极限定理中，sample mean虽然有不稳定性（采样10个，sample mean可能是5可能是别的）但在random sample without replacement的过程中，随着sample size增加，mean的不稳定性是提升的。所以mean比median更efficient。

**spread：**range, quantile, variance, standard deviation

* quantile: robust to outlier 
* sd和variance: 和mean类似，也是有稳定性 随着sample size的增加稳定性增强

**relative position:** relative rates p/1-p， frequency，percentage，proportion of each category  

**correlation：**两个continuous variable，用correlation；如果是discrete variable，可以画一个cross table； chi square test， ks test, fisher exact test, pearsonr ; 如果是discrete variable和continuous variable之间（1）直接画图也能看出来，eg 工资和性别的关系 可以boxplot，左右男生和女生；或者histogram，男生女生分别画distribution （2）logistic regression或linear regression 后者，response valuable是工资，feature就是性别，先做one-hot encoding，最后用性别predict工资 如果predict出来性别的coefficient很大，就说明性别有用 也可以反过来，用前者，工资predict性别，如果工资这个feature对性别判断有帮助，也可以刻画... 

**multivariate：**（1）两两画图 （2）multivariate analysis 



以上 都需要自己熟悉、DIY一些画图的function，sub-package，比如 图怎么画、label怎么放、多变量怎么画、单变量怎么画、correlation怎么画、boxplot怎么画... 方便data challenge或者online assessment 

### Inferential Methods

在descriptive methods的时候，sample都有随机性 如果想要做出一个不取决于样本、没有随机性的论断时，就需要inferential methods。

**Distribution** 

* unimodal 
* bimodal: mixed gaussian distribution 
* symmetric 

**characterize distribution**: frequency table, density function, cumulative frequency, cumulative distribution function \(CDF，是一个概率，单调递增且一定在 0,1之间 F\(x\)\)，probability density function \(PDF，是F\(x\)的导数，upper bound没有限制，斜率的函数，只是单纯大于0\)。

### Normal Distribution

好处一：sample mean 数很大时服从正态分布

好处二：知道了mean和variance，就知道distribution 

比如, 68    95    99. 7Rule ．

68％ of the observations fall within $$\sigma$$ of the mean $$\mu$$ ．

95％ of the observations fall within  $$2\sigma$$ of the mean $$\mu$$ ．

99.7％ of the observations fall within  $$3\sigma$$ of the mean $$\mu$$ 

### standardized normal 标准正态分布

如果mean是$$\mu$$ ，sd是$$\sigma$$，standardized value of x就是 $$z=\frac{x-\mu}{\sigma}$$ ，mean就是0，sd是1.

 $$E\left(\Sigma X_{i}\right)=\sum\left(E X_{i}\right)=n \mu$$ ，如果x之间independent， $$\operatorname{Var}\left(\sum X_{i}\right)=\sum \operatorname{Var}\left(X_{j}\right)=n \sigma^{2}$$ 

性质： $$\overline{x} \sim N(\mu, \sigma / \sqrt{n})$$ 

$$E(\overline{x})=E\left(\frac{\sum x_{i}}{n}\right)=\frac{1}{n} E\left(\sum x_{i}\right)=\frac{1}{n} \cdot n \mu=\mu$$ 

$$Var (\overline{x})=\operatorname{Var}\left(\frac{\sum x_{i}}{n}\right)=\frac{1}{n^{2}} \log \left(\sum X_{i}\right)=\frac{1}{n^{2}} \cdot n \sigma^{2} = \frac{1}{n}\sigma^{2}$$ 

standardized value of the mean $$\overline{x}$$ 就是 $$z=\frac{\overline{x}-\mu}{\sigma / \sqrt{n}}$$ 

### predict ordinal feature

如果想要predict是“为我的服务评分”出来的“满意 一般满意 不满意” 之类，这是有顺序的categorical feature比如可以做multistage regression，就像是one vs all那样，先合并两个，然后用普通的logistic regression，接下来再把bin过了的分开

还有一个ordinal regression

比“单纯用logistic regression或者linear regression，强加一个距离”考虑的更完整。

### Population and Sample

Sample: random sampling, stratified sampling, systematic sampling, cluster sampling, multistage sampling, area sampling 

### 如何检验normality of the data

1. QQ Plot
2. QR Test?



