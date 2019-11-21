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

## MapReduce

* 好处：有迹可循，存了中间结果
* 坏处：每次读入写入磁盘很慢

## Spark

1. One Computation Engine to fit all
2. From disk to memory
3. From single job to DAG
4. From 1 pass to multiple pass

* 有向无环图DAG方便优化步骤，中间没有读写

如果有100GB data on disk，20G in memory，Spark会memory and disk，把一部分放在memory，一部分放在disk；通过来回swap的方式，在数据放不下memory的时候写一些到disk（面试不太会考了）。

Spark session：配置一些基本信息，比如application name，memory和CPU sparksession下有一些function，比如spark.read.format\("csv"\)，load进来就是spark **dataframe**

Spark context是spark RDD操作的接口；类比就像是SparkContext是宝库的大门，里面给**RDD**开了一些小门；所以通过data=sc.textFile\(\)之类的function 

Spark SQL is data warehouse   
Spark ML support deep learning   
Spark Streaming is able to process stream data  
Spark SQL can query Hive Table

### Spark DAG and stage

Organization data flow of RDD   
Optimize network communication cost   
One job is divided into different stages based on data shuffle

### Spark RDD

* RDD是分布式内存环境的抽象（或者说，分布式内存的一个对象） 通过RDD访问每一个机器内存里的数据 但是在用户使用时是感受不到它有多个分块的 RDD is an immutable collection of **objects** that can be operated on in parallel **\(面试常考\)**
* Spark中实现了内存级别的备份（use lineage information, user不能访问的），和硬盘级别的备份（checkpoint）
* **Resilient:** RDD keeps its **lineage** information; it can be recreated from parent RDDs
* **Distributed:** partitions can be distributed across multiple nodes in the cluster; each RDD is composed of 1 or more partitions. 
* Big data object abstraction 
* Contains multiple data partitions
* Can persist into disk

Spark RDD caching 

* save the iterative computation cost 
* caching data into memory function is : RDD.cache\(\)
* Store data into memory 
* Reusable for different stages 
* Reduce overhead to read data from disk

### Spark physical plan

国内某公司面试问到：groupbykey和reducebykey有什么区别？



![](../.gitbook/assets/image%20%2810%29.png)

![](../.gitbook/assets/image%20%2817%29.png)

reducebykey存了一个中间结果，中间有一个C2，这就减少了中间数据的shuffle。reducebykey相当于把aggregation的操作放在了前面来做。

### Fault Tolerance机制

Spark data lineage可以fault tolerance，track back to the data source

### Spark Data Partitioner \(Data Engineer的OA考过\)

输入是key-value，输出是 the partition id

### Spark Dataframe

先只有RDD，没有dataframe的概念；RDD is a key-value pairs object, 所以数据没有schema。而用dataframe的时候，能直接assess某一列，因为数据有了schema。

至于SQL Query，其实可以和dataframe走一样的逻辑引擎；这就是为什么它们之间可以互相转化；dataframe的结果可以转换成query，反之亦然。

![](../.gitbook/assets/image%20%2838%29.png)

### ML-lib Spark ML

* ML-lib API: 针对的是RDD的操作
* Spark-ML: 针对的是data frame的操作

### Spark Vs Hive 

Spark is memory based   
Hive is based on MapReduce   
Hive Spark can execute Hive table

### 面试必问：K Means手写、分布式的K Means

### 面试必问：SGD、为什么-alpha、alpha设置不当会怎么样

### 面试必问：Linear Regression的分布式实现

老的做法：把数据做成分块，然后每一块学w，最后汇总平均；但问题在于这不一定是最好的结果，比如对于skewed data就不行；

现在的做法：parameter server，数据分成多个存储，参数也分块存储

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



