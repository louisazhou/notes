---
description: Machine Learning Models
---

# Chapter4 Model

面试必考内容，对于data scientist来说，有一个思想转变很重要：从基于归纳到基于演绎。

基于归纳的思考方式：一种省时省力的思考方式，从众或者基于他人经验。比如Machine Learning本质就是一个归纳。

基于演绎的思考方式：从问题出发，用数据支撑演绎结论。这个过程中有一些条件是需要归纳的方式获得的，最终是需要把问题分割到不可分为止。

比如，设计一个航空公司oversell的系统，如何把问题转换为一个我们可以解决的子问题？也就是，为这个问题设计一个接口（interface）。对它来说，实现的目的是maximize profit, 从演绎的角度来说需要进行转化，把它分解成很多子问题。合理的interface是：在某个时间点，下一张票该不该继续卖。

**What**

Goal：Maximize Profit

Interface：At a given time, should we continue to sell one more ticket? 

**How**

Sell if E\(incremental profit from selling 1 more ticket\)&gt;0, where Expected \(Sell 1 More\)= E\(Revenue -Cost\) = E\(Revenue\)-E\(Cost\). 

E\(Revenue\)=T=tickets\_value

E\(Cost\) = E\(oversell\_cost\) = 

\(1\) \# of sold ticket &lt; capacity, E\(cost\) = 0

\(2\) \# of sold ticket &gt;= capacity, E\(cost\) = E\(total cost when sell one more\) - E\(total cost now\)  or E\(cost\) = E\(cost\_per\_person \* num\_no\_seat\_increased\) = \(假设cost per person是一个常数\) = C \* E\(num\_no\_seat\_increased\) = C\*\[E\(num\_no\_seat\_after\) - E\(num\_no\_seat\_before\) \]

E\(num\_no\_seat\) = E\(show\_num- capacity\) = E\(show\_num\) - S

E\(show\_num\) = sum \(P\(show1\)+P\(show2\)+ ... \)

P\(show\_x\) 

**Notation**

P: Profit       T: ticket\_value    C: cost\_per\_person   S: \# of seats in the flight 

到现在为止，我们把“设计一个超售系统”转化成了一个“预测show的概率”。这个逻辑思维能力非常重要。因为“如何使用Logistic Regression”是一个很简单的事，但是这个思维方式是很难训练的。面试如果能按照这个思路说，就算在model的部分出了一点小的知识点问题，也可以得到一个“yes”。很容易能够弥补domain knowledge的漏洞，用这种方式，面试官就可以给一些提示，自己也很容易上下迂回。

倒数第二个步骤，其实还有一个表述， E\(show\_num\) = num\_sold\_ticket \* avg\_show\_rate。这两个等式其实都是正确的，第一个等式，是因为logistic regression is well calibrated, 成立。 第二个等式也成立。那我们为什么不用一个平均情况？而是用一个machine learning model？

答案是 more stable、看方差。因为第一种方式是多次结果的P，降低了方差。使用第二种方式表达后，方差会非常大。

接下来就是为这个classification problem的ml model找feature... 

比如，性别、年龄、购票时间和起飞时间之间的时间差... 

如果我们在比较自己的ML model和使用平均值预测出来的结果时，发现model的方差还不如使用平均值，那说明这个model做的非常trivial。

**本章**的sub-section就是各种modeling的细节。

\*\*\*\*

### Machine Learning Workflow

Data→Feature Matrix→Models→Evaluation→Business Value



### Handle Missing Value

* General ideas: 
* * Deletion 因为所有补齐的数据都有自己的假设，也就是都是假的，认为引入了噪声，污染了数据
  * Imputation
* Categorical features
* * Make NA as one category
  * Logistic Regression
* Continues features
* * Mean, median, mode, etc. 
  * Linear regression.

> 面试真题：推荐用户购买商品，如果有一个feature是对于他之前x次看到了这个商品的排序，比如刚看过咖啡机就是1，再之前看过咖啡壶是2... 如果有一款咖啡豆，没有被看过，如果想补齐这个continuous variable，补什么？  
> -可以补均值  
> -如果不补，那可以补100（意味着不重要）  
> -不可以补0或者-1 因为数字越小，意味着它越重要

#### 用regression做插值

1. 不用label，就用feature，用剩下的满的feature构造它们和有missing的那个feature之间的关系。
2. 补上值（但是也引入了相关性）
3. 新的training data就有了补上了的值，重新train一遍regression model
4. 此时可能训练结果就有了变化。
5. 再继续train
6. 如此iterative，直到稳定。 （当然也可以只补一次）

最大的局限性：overfitting，在feature之间引入了multicolinarity。

### 面试时注意

Missing Value的处理，可以按照feature matrix的角度去处理，也可以从model的角度，挑选不会受到missing value影响的model。

Categorical Feature的处理，one-hot、DL的方法做embedding、聚类后one-hot。

如何做evaluation，evaluation metrics的定义需要根据具体案例做具体分析。比如携程订房、做房价预估的问题，RMSE还是绝对值还是比例。MSE，惩罚定价特别高的项。 可以根据直觉判断，说绝对值 但是还是要AB Testing。订餐软件迟到的时间，MSE更好，因为迟到是一个难以容忍的use case。

offline evaluation很好，但是online evaluation特别不好，为什么？ 可以提出各种原因。比如时间的变化、long-term effect、experiment的randomization没做好... 

 





