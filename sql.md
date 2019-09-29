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

执行顺序 FROM--&gt;WHERE--&gt;GROUP BY--&gt;HAVING--&gt;SELECT--&gt;ORDER BY

1. Group By 1 可以当做一种简写，指的group by select语句中的第一列  同理，ORDER BY 2 也是一种简写
2. HAVING是在select之后才操作的，所以Having里有Select里用到的aggregation function
3. row

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

## Homework/Practice

练习平台 

{% embed url="https://www.w3schools.com/sql/" %}

student

| Sno | Sname | Ssex | Sage | Sdept |
| :--- | :--- | :--- | :--- | :--- |
| 9512101 | 李三 | 男 | 19 | 计算机系 |

course

| Cno | Cname | HOURS |
| :--- | :--- | :--- |
| C01 | 计算机网络 | 90 |

SC

| Sno | Cno | Grade |
| :--- | :--- | :--- |
| 9512101 | C01 | 90 |



```sql
#2
SELECT Sno, Cno, Grade
FROM SC 
WHERE Grade BETWEEN 70 AND 80

#3
SELECT Grade
FROM SC
WHERE Cno='C01'
ORDER BY Grade DESC
LIMIT 1

#4
SELECT Cname, Cno
FROM course
WHERE Cno in (SELECT DISTINCT(Cno) FROM SC)

#5
SELECT AVG(Grade), MAX(Grade), MIN(Grade)
FROM SC
WHERE SC.Cno='C01'

#6
SELECT Sdept, COUNT(Sno)
FROM student
GROUP BY Sdept

#7
SELECT Cname, COUNT(Sno), MAX(Grade)
FROM SC, course
WHERE SC.Cno in (SELECT DISTINCT(Cno) FROM SC) AND SC.Cno=SC.Cno
GROUP BY SC.Cno

SELECT Cname, COUNT(Sno), MAX(Grade)
FROM course
INNER JOIN SC
USING Cno
GROUP BY SC.Cno

#8
SELECT student.Sname, student.Sno, COUNT(Cno)
FROM student
INNER JOIN SC ON SC.Sno=student.Sno
GROUP BY student.Sname, student.Sno
ORDER BY COUNT(Cno)

#9 
SELECT COUNT (DISTINCT(Cno)), AVG(Grade)
FROM SC

#10
SELECT Sno, Sname, COUNT(Cno), AVG(Grade)
FROM student
INNER JOIN SC on Sno
GROUP BY Student.Sname
HAVING COUNT(Cno)>2

#11
SELECT Sno, SUM(Grade) AS TOTAL
FROM student
INNER JOIN SC
USING Sno
GROUP BY student.Sno
HAVING TOTAL>200

#12 
SELECT Sname, Sdept
FROM student
INNER JOIN SC
USING Sno
WHERE SC.Cno='C02'


SELECT Sname, Sdept
FROM student
WHERE student.Sno==SC.Sno AND Cno='C02'

#13
SELECT Sname, Cno, Grade
FROM student
INNER JOIN SC
USING Sno
WHERE Grade>80
ORDER BY Grade DESC 

#14
SELECT Sname, Ssex, Grade
FROM student
INNER JOIN SC
USING Cno
IN (SELECT Cno FROM course WHERE Cname='数据库基础') 
AND student.Sno=SC.Sno
WHERE Ssex='男' AND Sdept='计算机系'

#15
SELECT A.Sname, A.Sage
FROM student as A
INNER JOIN student B
on A.Sage=B.Sage IN (SELECT )
```

Customers

| customerid | country |
| :--- | :--- |
|  |  |

Orders

| orderid | customerid |
| :--- | :--- |
|  |  |

Orderdetails

| orderid | productid | quantity |
| :--- | :--- | :--- |
|  |  |  |

Products

| productid | categoryid | price |
| :--- | :--- | :--- |
|  |  |  |

Categories

| categoryid | categoryname |
| :--- | :--- |
|  |  |

```sql
# Top sales(#) by country
SELECT a.country, COUNT(orderid)
FROM Customers
INNER JOIN Orders
USING customerid
ORDER BY COUNT(orderid)
```

这里，如果sum所有的null就是null，如果count所有的null是0，但average了还是null

如果两个都改成inner join，那么就只有4行，因为0的行不会出现。面试题：你用了inner join，那么如果你用left join会如何？ 

如果只是第二个改成inner join





```sql
# revenue by country
SELECT a.country, COUNT(orderid)
FROM Customers
INNER JOIN Orders
USING customerid
ORDER BY COUNT(orderid)
```

## Online Compiler



