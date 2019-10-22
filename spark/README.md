# Chapter6 Big Data and Spark

Data Warehousing--OLAP

ACID \(Atomicity, Consistency, Isolation, Durability\)

DataBase--OLTP



> 面试中考过数据库中的常见join：Sort-based Merge Join、Hash-based Join



BigTable：都是key-value pair，因为value的值不一定数目，不能用关系型数据库了。

HBase：column family和timestamp，方便单数据的搜索添加查找，还有时间戳

{% embed url="https://data-flair.training/blogs/hbase-vs-rdbms/" %}

> 面试题：merge n个sorted list的复杂度

> 面试题：对数据量不大的文档做word count，数据量非常大的，单词有范围的、单词没有范围的... 如何做到最快
>
> 可以hashmap或者external sort（merge sort）的思想； 数据量非常大就external sort，单词有范围可以hashmap，单词没有范围只能external sort。  
> 多个machine，counting in parallel，最后reduce汇总结果。总之，map，shuffle，reduce  
>   
> 对terabyte的数据进行排序  
> 1. sample数据 得到histogram  
> 2. 尽量让每个reducer有一样数量的data 同时也是起初有了这么一个小的排序减少reducer的工作  
> 3.   
>   
> followup：如何在一堆bucket里找有没有某个数字？  
> 类似binary search的技巧

> 面试题：微博热搜的top10每一块区域选出top10  
> 每个机器处理一个字母initial 最后汇总  
>   
> 面试题：如果data skew如何优化parallel过程
>
> 画histogram

## Data Stream Analytics

### Volume Reduction

* Sampling 如果是fraud，可能sample的时候完全不会看到fraud cases； sample不能用于做alerting的； 另外 得到的都是approximation 
* Sketching  尽量少用内存，对于数一堆数的时候有用 得到的都是accurate的

### Data Stream Analytics: Data Analysis

* Concept Drift 是其中最重要的，需要关注是否有drifting
* 和aggregate相比，单个data的重要性要大得多，因为这就是分析的对象

![](../.gitbook/assets/image%20%285%29.png)



### Sampling

Approaches：

* Reservoir Sampling： sample first m items, 缺点是不能parallelize
* Min-Wise Sampling： generate a random number uniformly distributed between 0 and 1, 
* "Smart" Sampling: domain knowledge sampling, 比如security的人会知道先关注small packets data



