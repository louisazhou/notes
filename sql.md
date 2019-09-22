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

FROM--&gt;WHERE--&gt;GROUP BY--&gt;HAVING--&gt;SELECT--&gt;ORDER BY

Group By 1 可以当做一种简写，指的group by select语句中的第一列

同理，ORDER BY 2 也是一种简写

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

练习平台 [https://www.w3schools.com/sql/](https://www.w3schools.com/sql/)



student

| Sno | Sname | Ssex | Sage | Sdept |
| :--- | :--- | :--- | :--- | :--- |
| 9512101 | 李三 | 男 | 19 | 计算机系 |

course

| Cno | Cname |  |
| :--- | :--- | :--- |
|  | 计算机网络 | 90 |

SC

| Sno | Cno | Grade |
| :--- | :--- | :--- |
| 9512101 |  | 90 |



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

```

