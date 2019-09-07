---
description: Machine Learning Models
---

# Chapter3 Model

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

到现在为止，我们把“设计一个超售系统”转化成了一个“预测show的概率”。这个逻辑思维能力非常重要。因为“如何使用Logistic Regression”是一个很简单的事，但是这个思维方式是很难训练的。面试如果能按照这个思路说，就算在model的部分出了一点小的知识点问题，也可以得到一个“yes”。很容易能够弥补domain knowledge的漏洞，用这种方式，面试官就可以给一些提示，自己也很容易上下迂回。

倒数第二个步骤，其实还有一个表述， E\(show\_num\) = num\_sold\_ticket \* avg\_show\_rate。这两个等式其实都是正确的，第一个等式，是因为logistic regression is well calibrated, 成立。 第二个等式也成立。那我们为什么不用一个平均情况？而是用一个machine learning model？

答案是 more stable、看方差。因为第一种方式是多次结果的P，降低了方差。使用第二种方式表达后，方差会非常大。

接下来就是为这个classification problem的ml model找feature... 

比如，性别、年龄、购票时间和起飞时间之间的时间差、

**Notation**

P: Profit       T: ticket\_value    C: cost\_per\_person   S: \# of seats in the flight 

 





