# Student Scores Query

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



