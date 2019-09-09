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

**Randomized Experiment:** counter factual, link 

**Pre-Post Design:** \(not for internet company\) cognitive scores before and after neural operations 

![](../.gitbook/assets/image%20%282%29.png)

**Enrollment:** Eligible for the experiment, eg. if the target population is 'registered users' , etc. Take a representative sample from the full traffic, usually done by hashing the cookie id or random number generator \(take 0.1 for example\). 

**Randomized:** select from the pool of people which group goes to experiment and which goes to the control group. The probability of being selected to any group is the same. It's independent of the users' characteristics and independent of the treatment.  Done by coin flip or random number generator \(take 0.5\).

**Novelty Effect:** Version A and B in experiment should not differ too much, to avoid novelty effect. Aside from the difference itself, 新鲜感 wears off or 陌生感 brings bias to the metrics and thus influences the experiment.  

