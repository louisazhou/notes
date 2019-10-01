---
description: Sequential Query Language
---

# Chapter5 SQL

## 基本解题思路

* Metrics, what does the result look like
* 需要什么数据
* 怎么获取数据
* 有了数据怎么计算（avg，count）

## 面试

1. 电话面试：“2张表格” 借助草稿纸，口头说
2. share doc，copy paste问题，规定时间内写SELECT FROM 实时看写语句的顺序、停顿的位置... 
3. On-Site： 白板

强推postgre SQL （相比较Oracle SQL/MySQL...  ）

非关系型数据库 HIVE Query也和SQL差不多

## Cheatsheet

执行顺序 ROM--&gt;WHERE--&gt;GROUP BY--&gt;HAVING--&gt;SELECT--&gt;ORDER BY

1. Group By 1 可以当做一种简写，指的group by select语句中的第一列  同理，ORDER BY 2 也是一种简写
2. HAVING是在select之后才操作的，所以Having里有Select里用到的aggregation function
3. UNION，连接的是两个SELECT语句，需要SELECT的column名字一样，数量也一样，默认的是unique values，所以假如有两个row一模一样，它会合并掉；如果想保留，用UNION ALL

```sql
SELECT FROM 
    CASE WHEN THEN
        WHEN THEN
        ELSE END
        AS 
FROM AS
INNER JOIN AS
ON
WHERE IN       / WHERE BETWEEN AND
GROUP BY
ORDER BY
```

UNION, INTERSECT, EXCEPT

```sql
SELECT AS
FROM
UNION
SELECT
FROM
ORDER BY
```

subqueries: 

```sql
SELECT
FROM
WHERE LIKE
    (SELECT
    FROM
    WHERE )
```

```sql
SELECT
FROM
WHERE 
    AND (SELECT AVG()
    FROM )
```

```sql
SELECT DISTINCT
    (SELECT COUNT (*)
    FROM
    WHERE) AS 
FROM
```

Window Function,   
row-number\(\) over \(partition by X order by Y\)  
这里容易犯的错误是partition by后面写很多column

```sql
SELECT category, product, sales
FROM (
    SELECT category, product, 
    row_number() OVER (PARTITION BY category, order by sales DESC) as row_num
    )
WHERE row_num<=2
```

Window Function,  
rank\(\) over \(partition by X oder by Y\)  
row\_number, rank, dense\_rank的意义都是排序，但是区别是row\_number给出单纯的行号，两种rank都给出排序后的号，两个相同数值给一样的rank；区别是rank的5、6名是同一个数字，则第七名标7，可是dense rank的第七名会标6，顺着之前的数字来。

Window Function,  
sum\(\) over \(\)  
得到的是cumulative sum

Case When除了最上面的基本用法之外，更常用的是结合aggregation function使用。如果多个Case是同样的条件，那么只执行第一个条件。  
+aggregation\_function 比如：

```sql
SUM(CASE WHEN ... THEN ... ELSE ... END) AS ...
COUNT(DISTINCT(CASE WHEN ... THEN... ELSE ... END)) AS ...

# Eg. count total impressions and how many 'top' clicks for all campaigns, all impressions

SELECT ads_campaign, 
        COUNT(impression_time) as impressions, 
        COUNT(CASE WHEN click_time is not null then click_time END) as top_clicks
FROM table
Group by 1
```

另外window function还可以用来算一堆数据中的median，因为median具有一个特性：它的row\_number的编号处在正中间，那么就可以通过从小到大排、从大到小排。如果是奇数，那么这两种排序后相等的那个就是中间的了，如果是偶数，会有两个index，分别是对方的+-1；不管如何，都可以对这部分匹配的。

row\_number over \(partition by group by\) as row\_num WHERE row\_num=INT\(COUNT\(\*\)/2\)



最后SQL也可以用来generate distribution或者bucketization。

t\_pages

| page\_id | product\_id |
| :--- | :--- |
|  |  |

t\_users

| user\_id | page\_id | timestamp |
| :--- | :--- | :--- |
|  |  |  |

```sql
# Q1: count unique users for product view for a given time
SELECT a.page_id, COUNT(DISTINCT b.user_id) as unique_users
FROM t_pages a
LEFT JOIN t_users b 
ON a.page_id=b.page_id
WHERE cast(timestamp as date) between AND 
#cast用来做数值类型的转换

# Q2：Product view distribution. eg. for product A, 10000 viewers saw it once, 22 twice

```



