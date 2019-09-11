# A/B Testing

## A/B Testing 

**经典考法：** 

1. Phone Screening, 概念解释 eg. What is P Value 
2. 应用题 公司的应用场景描述问题，开放型，A/B Testing  - 实验设计 - 假设检验
3. \*证明题 Target PhD的

### **A/B Testing**

A decision making procedure during product iteration. Experiment Design \(collect customer log\), Data Analysis and Hypothesis Testing. 

![](https://cdn.mathpix.com/snip/images/qCrwzndk-aa__zyS2VSDKgBZ2Q5J6mz_fUABXf2AcbI.original.fullsize.png)

Review可能会回到数据分析（engineering）或者重新A/B Testing。Ramp up：逐步上线，用户逐步从1到100%，launch：100%用户接触。

> 面试：最终的问题其实是，作为产品的负责人，怎么知道新的xx会不会比原来的xx更好？  
>   
> 在回答这种性质的面试问题时，  
> 第一步：定义 什么是好 define the metrics to describe it. eg. revenue, CTR , the latter one reflects users' increase in their interests.   
> 第二步：对比实验，比较前后的指标   
> 第三步：实验设计，控制实验成本、风险，确定实验的有效性  
> 第四步：假设检验，从实验数据中得到正确的结论

### **Metrics**

 ****quantitative measurements that reflect the product quality. 

> 面试：

### **Experiment and Randomized Experiments**

**Experiment:** \(not for internet company\) 

**Randomized Experiment:** counter factual, link 无法用上面的这样实验，因为在互联网公司，没办法让每个人看到广告后被洗脑、忘记过去的再看一个新的，即使是同一个人，今天心情好明天心情不好也会有一个随机性。

**Pre-Post Design:** \(not for internet company\) cognitive scores before and after neural operations 

![](../.gitbook/assets/image%20%282%29.png)

**Enrollment:** Eligible for the experiment, eg. if the target population is 'registered users' , etc. Take a representative sample from the full traffic, usually done by hashing the cookie id or random number generator \(take 0.1 for example\). 

**Randomized:** select from the pool of people which group goes to experiment and which goes to the control group. The probability of being selected to any group is the same. It's independent of the users' characteristics and independent of the treatment.  Done by coin flip or random number generator \(take 0.5\).

**Novelty Effect:** Version A and B in experiment should not differ too much, to avoid novelty effect. Aside from the difference itself, 新鲜感 wears off or 陌生感 brings bias to the metrics and thus influences the experiment.  

以下是 如何根据AB Testing所定义的metric的不同，利用HT得出结论。

## Hypothesis Testing

  

##  



### Key Concepts

**Null Hypothesis** $$H_{0}:$$ A specific claim about the population, often described using an expression of the parameter\(s\).

**Alternative Hypothesis** $$H_{\alpha}$$ : The claim about the population when the null hypothesis is false \(parameter of interest: $$\not=, > <0$$ \).

_**P-value:**_ The probability of obtaining **as or more extreme results** than the current observation, under the null hypothesis. 

**Significance Level** $$\alpha$$ : Type 1 Error Rate, the probability of rejecting $$H_{0}$$ given $$H_{0}$$ is true. $$P(rej H_{0}|H_{0} \text{is true})$$ 



$$H_{0}$$ 是想要推翻的假设， $$H_{\alpha}$$是想要证明的假设。绝大多数的A/B Testing中的HT：

$$H_{0}$$ 是 $$\delta metric=0$$ , $$H_{\alpha}$$可以是双尾（=），单尾（&gt; 或&lt;0）。

"Is A better than B? " $$H_{\alpha}: metricA-metricB>0$$ 

"Can you tell me if there is a difference?" $$H_{\alpha}: metricA-metricB\not=0$$ 

"Which one is better?" "Let's assume we can run a two-tail hypothesis testing"



**Type 1 Error:** This happens when $$H_{0}$$ is true, but is rejected. 无中生有

**Significance Level** = Type 1 Error Rate = $$P(rej H_{0}|H_{0} \text{is true})$$ 

**Type 2 Error:** This happens when  $$H_{\alpha}$$ is true, but $$H_{0}$$ is not rejected. 有眼无珠

**Type 2 Error Rate:** $$\beta=\operatorname{Pr}\left(\text { not rej } H_{0} | \mathrm{H}_{1} \text { is true }\right)=1-\operatorname{Pr}\left(\text { rej } \mathrm{H}_{0} | \mathrm{H}_{1} \text { is true) }\right.$$ 

Power: The probability of rejecting  $$H_{0}$$ , when  $$H_{\alpha}$$ is true. $$power = Pr (\text{rej}\mathrm{H}_{0}| \mathrm{H}_{1}\text { is true) }=1-\beta$$ 



### Why Significance Level = Type 1 Error Rate ?

P-value is a random variable because it's a function of a random variable. $$\left\{p \text { -value } | \mathrm{H}_{2} \mathrm{~ i s ~ t r u e \} ~} -\text { Uniform }(0,1)\right. ，\operatorname{Pr}(\text { uniform variable }<x)=x, \text { for } x \text { in }(0,1)$$ 

$$\begin{aligned} \text { Type 1} \text { error rate } &=\operatorname{Pr}(\text { reject } H_{0} | H_{0}  \text { is true) }\\ &=\operatorname{Pr}(\text { p-value }<\text { significance level } | \mathrm{H_{0} } \text { is true) }\\ &=\text { significance level } \end{aligned}$$ 

### Trade off between type 1 and type 2 error

可以默认Type1 Error \(convicting an innocent man\) 比Type 2 Error \(releasing a criminal\)更严重，所以在实际操作中，优先降低Type1 Error，其次尽可能降低Type 2 Error。为此，设置一个比较小的 $$\alpha=0.05$$ , 然后尽可能收集更多的数据，来获得更多的power $$\beta$$  （或者也可以设计其他的 less variable metric）。





eg. If we want to analyze the H0: students at xx have an average height of 7 feet \(2.13m\). 

Let's say  $$H_{0}: \mu=7 feet$$ , then $$H_{\alpha}: \mu\not=7 feet$$ . We first assume the null hypothesis to be true. Then take a random sampling from the population \(the sample represents well the population\), know of the average height of the sample is $$\overline{X}=5'9''$$ . If the null is true, then if we enter any random classroom and draw the histogram of this parameter, then the histogram \(hypothetically\) will look like a mean of 7 and a bell-shape distribution. Also, in that histogram,  $$\overline{X}=5'9''$$ will fall to the left end. 

So the key-point in here is 'if the null hypothesis is true, what's the histogram gonna look like'

social-network中，不能单纯用P value，因为nodes之间不再是independent。所以原来的“如果p&lt;0.05, reject the null hypothesis H0“就不对了。

