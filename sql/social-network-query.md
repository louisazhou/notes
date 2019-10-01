# Social-Network Query

highschooler

| id | name | grade |
| :--- | :--- | :--- |
|  |  |  |

friend, two ways

| id1 | id2 |
| :--- | :--- |
|  |  |

likes, one way

| id1 | id2 |
| :--- | :--- |
|  |  |

```sql
#Q1 all names who are friends with someone named gabriel
# 难点在 可能在第一列也可能在第二列 但是表格里不是双向的，不会1.2后还有2.1
# 所以要写一个UNION 把两个结果合并了

#Q2 For every student who likes someone 2 or more grades younger than themselvs. 
# return students' name and grade, and the name and grade of the students they like

SELECT a,id1, b.grade, b.id2, c.grade

#Q3 For every pair of students who both like each other, return the name and grade
# of both students. Include each pair only once, with two names in alphabetical order. 
# 两种方法，force an order或者swap，两个field在subquery里，括号扩一下

#Q4 Find all students who do not appear in likes table and return their names and grades.
#union两个where id not in的subquery

#Q5 for every situation where student A likes student B but we have not info of B likes
#(id2有的id1没有)，所以id1和2取出来，where id2不在这里，subquery
#或者，self join

#Q6 Find names and grades of students who ONLY have friends in the same grade. return the result sorted by grade.
#还挺难的= = 集合的概念，先找到所有有同一grade朋友的人，再找到有不在同一grade的朋友的人，减一下
#或者 加2列。一列是和他同一grade的朋友的数量，另一列，不同grade的数量，所以最后select前者>0,但是后者<0的id

#Q7 Find the name and grade of all students who are liked by more than one other student
# count
```

