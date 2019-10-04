# Stats Interview Review

面试问题：Uber有一个新的matching algorithm, 如何比较新的algorithm比原来的更好？

Objective define→Metrics and effect define→Experiment setup→Conduct hypothesis→Result Interpretation

## 第一步：Clarify the metric

和面试官讨论，把定性“好”转换为“定量”的问题。于是变成了“用什么metrics来描述「好」？”

* User Growth
* * Registrations
  * * Analyze new user trends
    * How to acquire new users?
  * Engagement
  * * How to measure user engagement?
    * * MAU, WAU, DAU
      * Time Spent
      * Actions
    * Analyze product usage trends
    * Retention curve
* Revenue Growth
* * Conversions
  * * How to convert more users to pay?
    * Price strategy, discounts, etc.
  * Subscription renewal
  * * Recurring payment vs. non-recurring payment
    * User cancel vs. payment issues
  * Lifetime Values \(LTV\)
  * * Does LTV justify the cost of 

> 注意在这一步不要张口就“等待时间”，还是找到一个系统和条理。比如，

* Driver side: match到近的user，canceling rate降低
* User side: waiting time, user evaluation result \(评星\)
* Company side: revenue increase, user growth

能看出系统性的思路。

> 在这里也不要激动，不要张口就是p value，2 group test。

## 第二步：Experiment design

How shall we do randomization? What are we going to randomize?   
1. There may be correlation between days.  
2. There may be day effects  
3. Allowing to measure long term effect.

- 如果是想追踪long-term的变化，或者user-level metric，用用户的rating来作为metric，那么从长期来看，effect在某个人上的差距。可以按照人来分，比如hash得到一批用户ID，control和treatment group这些人，持续追踪他们1周的活动。也就是“长期expose在treatment group中的effect”。3星-5星-4星的变化，叫做blindness effect。短期来看，可能对revenue带来了效果，但是因为blind，又变回了原来的pattern。  
或者，按照order来分，为了maximize sample size，就可以hash得到随机的订单ID，追踪订单的状况，randomize request。

在这里，可能会让算一下sample size。好好用公式。

再或者，如果算出来发现需要用订单量里5%的traffic但是也不够，那该怎么办？—— Run longer test. 

同理，7% traffic by 1 day or 1% traffic by 7 days? —— 前者如果还是建立在不同人情况下的，所以sample size肯定就很大；相比之下1%by 7days 收集到的数据之间并没有correlation，比如A用户和B用户打分style的不同并不会影响我们的结果。

## 第三步：Data Collection 

用SQL query数据，有可能会问：不同Join的执行效率，执行顺序。

FROM→WHERE→GROUP BY→HAVING→SELECT→ORDER BY→LIMIT

面试时（1）要注意时间的限制 （2）说到哪写到哪，把思路讲出来。

## 第四步：Hypothesis Testing

终于... 背公式，注意公式的使用范围，连续的还是离散的

## 第五步：Result interpretation 

面试问题： What if the hypothesis cannot be rejected?

* Increase the observation time
* Increase the sample size
* Segment Analysis, from universal factor to regional factor \(e.g. Red means bull market, whereas it means bear market in US. Therefore might cause different interpretations\)

面试问题：What if we have conflicting metrics result? 

eg. waiting time down; but revenue also down; weigh in the factors and see which one is more important. 再或者我们也可以构造一个utility function, 比如waiting time降低1%带来xx，revenue增加xx带来xx...  

面试问题：What if the hypothesis is rejected?

1% ramp up, 10%, 50%, ... 还要说明为什么要这么做。

* Time Effect: 长期来看可能... 或者有可能和时效有关，比如experiment的时间是xx，但是不知道xx如何
* Difference between treatment and control.
* Difference between launch and before-launch
* Pay attention to ramp up plan and use the hold-out group \(比如留下1%的不上线\)

