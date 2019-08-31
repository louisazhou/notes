---
description: 'SEO, SEM, Marketing Vehicles'
---

# E Commerce

## SEO - Search Engine Optimization

Search Engine Ranking Algorithm

### On-Page Factors \(Code & Content\)

1. Title
2. Header
3. Content
4. Hyperlink
5. Keyword Frequency & Density \(similar to the idea of TF-IDF\)

### Off-Page Factors

1. Anchor Text
2. Link Popularity 

## SEM - Search Engine Marketing

A form of internet marketing that involves the promotion of websites by increasing their visibility in search engine result pages \(SERPs\) primarily through paid advertising.

Cost Per Click \(CPC\) Cost Per Impression \(CPI\) Pay Per Acquisition \(PPA\) Pay Per Order \(PPO\)

### Google AdRank

Ad Rank \(an overall score\)= Max CPC Bid $$\times$$ Quality Score Score越高，rank越靠前, Ad Position越好

Quality Score 根据 On-Page Factors/ Off-Page Factors 计算，这里是一个ML problem.

### Bid for Max CPC

Actual CPC = Add rank of Ad below you/ your quality score +$0.01 最后的0.01是为了防止恶意竞拍冷门关键词

> 使用原因 Vickrey Auction 信息不透明，不知道其他买家的竞拍价的情况下，上一个人出后一个人的价格 结果是，大家为了对自己最有利，都会将竞价集中在value上下。

### Measure of Effectiveness

面试题目1： 方案ABC哪个更好 要注意CPA CPC不一定是主要指标，也有可能公司更在乎的是impression或者click再或者conversion。比如在growth stage，重要的不是CPA/CPC，而是conversion和impression。所以 即使在问题比较清晰时也需要问clarification question。

面试题目2：AB Testing，A3000，CTR高一点，B30000，CTR低一点，A更好？ A 3000 CTR10% variance 0.5% variance 0.05% B 30000 CTR9.9% C 30000 CTR9.8% 假设variance是0.5%，那么A就没有比B好 Variance = P\(1-p\)/n

## Customer Shopping Journey

Trigger 种草 Initial Consideration Set Zero Moment of Truth \(ZMOT\) 2011，Google提出，网络的渠道接触商品，比如搜到了、微信看到了评价 First Moment of Truth \(FMOT\) 可以是offline也可以是online，去实际看到这个产品 Moment of Truth 顾客与产品接触的机会 Purchase Decision 购买 Second Moment of Truth \(SMOT\) 网络上的评价、点赞、分享，比如拼多多就是利用它

## Marketing Vehicles

1. Organic 原生态
2. SEO
3. SEM
4. Email    面试题目：classification 5 groups，其实可以用任何一个model，因为可以用1vsall，1vs1
5. Display Ads （app开屏、网页开屏）    面试题目：如何知道用户安装了ad block    \(1\)search engine-ad blocker    \(2\)点击量的对比、conversion的对比    最后广告位其实也是Vickery Bidding 可以认为只要有广告位就有bidding    现在更多的是mobile ad spending，Facebook和Twitter33%，Google80%
6. Social Media    内置在ins等的推荐
7. Affiliates    cashback的网站（eBate），通过点击卖家信息，拿到cashback，原理是把卖家的广告费的一部分返给买家
8. Comparison Shopping Engine    比如Google shopping，什么值得买
9. Content Marketing    鼓励用户提供logo之类

这些都有diminishing effect 不是说double了cost之后就一定能double return

## Marketing Attribution Models

### Single Touch Attribution Model \(single source attribution\)

Assign the last click to the outcome 比如3 days last touch 只算看到ad之后三天的，超过的都不算 早的MOT比较吃亏

### Multiple Touch Attribution Model \(rule based model\), fractional Attribution

Eg 90 days equal distribution Eg each action is assigned a certain weight at position 所以关键在于如何assign weight

### Algorithm or probabilistic Attribution

基于ML model 1. Define the metric \(Y, binary classification, buy or not\) 2. 1 3. Run a model 4. Analyze output and generate report

Customer. action1. action2. Y 这里需要给action1和action2顺序，因为先后和Y有关 所以需要这么定Feature： X1：Action1先出现 X2：Action1后出现 X3：Action2先出现 X4：Action2后出现 接下来，logistic regression，feature importance

如果有5个feature，会有5times5个feature

