# LeetCode SQL题目总结

Sqlzoo,mode[analytics](http://learn.1point3acres.com/courses/ds501-analytics/),hackerank,leetcode. From 1point 3acres bbs  
JOIN  
        1. 标准句式之join+on/where  
SELECT A.NAME  
FROM EMPLOYEE A JOIN EMPLOYEE B-baidu 1point3acres  
ON A.MANAERID=B.ID  
WHERE A.Salary&gt;b.Salary  
On 用来限制join 的key  
Where 用来限制最后呈现出的数据符合的条件  
先开始就要把所有需要的fields在join another table之前都列出来  
        2. 先开始列出多个表格时，用where  
select distinct l1.num as ConsecutiveNums from logs l1,logs l2,logs l3  
where l1.id=l2.id-1 and l2.id=l3.id-1  
and l1.num=l2.num=l3.num. From 1point 3acres bbs  
  
Select dep.name as department,emp.name as employee,emp.salary  
From department dep,employee emp  
Where emp.departmentid=dep.id  
And emp.salary=\(select max\(salary\) from employee e2 where e2.departmentid=dep.id\)  
  
   
JOIN的分类  
如果我们想查找在第一个表里出现的并且在第二个表里也出现的（并且  
Join by default是指inner join的  
因此像上面那道题就可以直接用join 因为我们只关心inner table的内容  
   
Left join会保存所以第一个table的内容 不管第二个table有没有匹配项目 通常可以用来查找第一个表里有但是第二个表里没有的东西. check 1point3acres for more.  
   
Example: 181 Employees Earning More Than their managers  
select a.Name as Customers from Customers a  
 join Orders b  
on a.Id=b.CustomerId  
where b.CustomerId is null  
   
这样inner join return的结果是0 因为inner join的条件下没有customerid是null的情况  
select a.Name as Customers from Customers a  
LEFT join Orders b  
on a.Id=b.CustomerId  
where b.CustomerId is null  
   
This one works. And if we change where b.CustomerId is null to where b.ID is null it also works but with slower performance  
   
Select department highest salary  
  
  
  
Option 1:   
SELECT D.Name AS Department ,E.Name AS Employee ,E.Salary  
FROM  
        Employee E,  
        \(SELECT DepartmentId,max\(Salary\) as max FROM Employee GROUP BY DepartmentId\) T,  
        Department D  
WHERE E.DepartmentId = T.DepartmentId  
  AND E.Salary = T.max  
  AND E.DepartmentId = D.id  
Option 2:  
SELECT D.Name AS Department ,E.Name AS Employee ,E.Salary  
from  
        Employee E,  
        Department D  
WHERE E.DepartmentId = D.id  
  AND \(DepartmentId,Salary\) in . check 1point3acres for more.  
  \(SELECT DepartmentId,max\(Salary\) as max FROM Employee GROUP BY DepartmentId\)  
  
  
  
   
RANK  
SECOND HIGHEST SALARY  
  
  
Option 1:  
select max\(salary\) as SecondHighestSalary from  
Employee  
where Salary&lt;\(Select Max\(Salary\) from Employee\)  
   
Option 2:  
select max\(salary\) as SecondHighestSalary  
from Employee  
where salary not in \(  
select max\(salary\)  
from Employee  
\). check 1point3acres for more.  
   
SQL BASICS  
Find duplicated emails  
  
\# Write your MySQL query statement below  
SELECT Email FROM \(select  
                  email,count\(email\) as num  
                  from person  
                  group by email\) L  
                  where num&gt;1  
Delete duplicate emails  
Option 1:  
DELETE FROM PERSON  
WHERE ID NOT IN\(SELECT MINID FROM \(SELECT EMAIL,MIN\(ID\) AS MINID FROM PERSON  
GROUP BY EMAIL\) AS TMP\)  
Option 2:  
Delete p1 from person p1,  
Person p2  
Where p1.email=p2.email and p1.id&gt;p2.id  
  
  
 无需要join只需要select符合条件的 \(SUBQUERY\)  
The use of subquery  
  
  
To display name, location,phone\_number of the students from database table whose section is A  
  
Answer:  
SELECT NAME, LOCATION, PHONE\_NUMBER FROM DATABASE  
WHERE ROLL\_NO IN  
\(SELECT ROLL\_NO FROM STUDENT WHERE SECTION='A'\)  
  
  
  
  
  
  
  
SELECT REQUEST\_AT AS DAY,  
ROUND\(SUM\(CASE WHEN STATUS LIKE 'cancelled%' THEN 1 ELSE 0 END\)/COUNT\(\*\),2\) AS 'Cancellation Rate' from \(  
SELECT \* FROM TRIPS T  
WHERE -baidu 1point3acres  
T.CLIENT\_ID NOT IN \(SELECT USER\_ID FROM USERS WHERE BANED='Yes'\) and  
T.DRIVER\_ID NOT IN \(SELECT USERS\_ID FROM USERS WHERE BANNED='Yes'\) and  
T.REQUEST\_AT BETWEEN '2013-10-01' AND '2013-10-03'\) AS NEWT  
GROUP BY RESUEST\_AT  
  
  
  
Answer 1:  
. check 1point3acres for more.  
select customers.name as 'Customers'  
from customers  
where customers.id not in  
\(  
    select customerid from orders  
\);  
  
Answer 2:  
Select c.name as customers. From 1point 3acres bbs  
From customers as c  
Left join orders o  
On c.id=o.customerid  
Where o.customerid is null  
  
注意这里不能用where 因为where 会only select 符合条件的（inner join相当于）会return nothing  
select customers.name as customers from customers,orders  
where customers.id=orders.customerid  
and orders.customerid is null （错误答案）  
  
下面一个例子同样不能用where语句要用join并选择合适的join的  
  
  
Select d.dept\_name, count\(s.student\_id\) as student\_number  
From student s right join department d on s.dept\_id=d.dept\_id  
Group by d.dept\_name  
Order by student\_number desc, d.dept\_name  
  
  
  
  
How to join tables with itself \(where /on两种写法都可以  
  
  
Answer 1:  
select e1.name as employee from employee e1  
join employee e2  
on e1.managerid=e2.id  
where e1.salary&gt;e2.salary  
. check 1point3acres for more.  
Answer 2:  
SELECT  
    \*  
FROM  
    Employee AS a,  
    Employee AS b  
WHERE  
    a.ManagerId = b.Id  
        AND a.Salary &gt; b.Salary  
  
   
Given a Weather table, write a SQL query to find all dates' Ids with higher temperature compared to its previous \(yesterday's\) dates.  
   
select p1.Id from Weather p1 join Weather p2  
on datediff\(p1.recorddate,p2.recorddate\)=1 and p1.Temperature&gt;p2.temperature  
   
select p1.Id from Weather p1, Weather p2  
where datediff\(p1.recorddate,p2.recorddate\)=1 and p1.Temperature&gt;p2.temperature  
  
Consecutive available seats   
当设计到连续的座位数（连续的天数）我们可以用table自己join自己 on 相差数为1 的方式，去得到我们想要的结果  
因为只有两个consecutive就满足条件，所以只能是join 两个table  
  
Question:  
  
Answer:  
 select distinct a.seat\_id  
From cinema a  
Join cinema b  
On abs\(a.seat\_id-b.seat\_id\)=1  
And a.free=true and b.free=true  
Order by a.seat\_id  
   
Question:  
  
Notice the difference between this one and the above one is that this one doesn't require you to join on abs value as this one is asking for the num instead of the id.  
  
  
 Answer 1:  
select distinct l1.num as ConsecutiveNums from logs l1,logs l2,logs l3  
where l1.id=l2.id-1 and l2.id=l3.id-1  
and l1.num=l2.num=l3.num  
  
Answer 2:  
Select t.num as consecutivenums  
From  
\(select distinct a.num from  
Logs a  
Left join logs b on a.id=b.id-1  
Left join logs c on a.id=c.id-2  
Where a.num=b.num and a.num=c.num\) t  
  
  
Side knowledge:  
The TO\_DAYS\(\) function returns the number of days between a date and year 0 \(date "0000-00-00"\). The TO\_DAYS\(\) function can be used only with dates within the Gregorian calendar  
   
Answer 1:  
SELECT WT1.ID  
FROM WEATHER WT1 JOIN WEATHER WT2  
ON TO\_DAYS\(WmT1.RECORDDATE\)-TO\_DAYS\(WT2.RECORDDATE\)=1  
AND WT1.TEMPERATURE&gt;WT2.TEMPERATURE  
  
Answer 2:  
SELECT WT1.ID  
FROM WEATHER WT1 JOIN WEATHER WT2  
ON TO\_DAYS\(WT1.RECORDDATE\)-TO\_DAYS\(WT2.RECORDDATE\)=1  
WHERE WT1.TEMPERATURE&gt;WT2.TEMPERATURE  
  
Answer 3:  
SELECT WT1.ID. check 1point3acres for more.  
FROM WEATHER WT1,WEATHER WT2  
WHERE WT1.TEMPERATURE&gt;WT2.TEMPERATURE AND  
TO\_DAYS\(WT1.RECORDDATE\)-TO\_DAYS\(WT2.RECORDDATE\)=1  
  
  
How to choose add number  
Side Knowledge:  
MySQL MOD\(\) returns the remainder of a number divided by another number. This function also works on fractional values and returns the exact remainder. The function returns NULL when the value of divisor is 0  
  
Question:  
Please write a SQL query to output movies with an odd numbered ID and a description that is not 'boring'. Order the result by rating.  
select \* from cinema  
where mod\(id,2\)=1 and description !='boring'  
order by rating DESC  
   
    
Select from temp table  
Answer  
Select class from \(select count\(distinct student\) as num,class from courses  
Group by class\) as temp where num&gt;=5  \(notice, the parenthesis is before table name\)  
  
  
Difference between Where and On  
Where is a part of the SELECT query as a whole, on is a part of each individual join  
On can only refer to the fields of previously used tables  
  
The on clause defines the relationship between the tables  
The where clause describes which rows you are interested in  
Many times you can swap them and still get the same result  
However this is not always the case with a left outer join  
  
As a rule of thumb, you should use columns that join your tables in On clauses and columns that are used for filtering in where clauses. This provides the best readability  
  
Different types of the JOINs in SQL  
Inner join: returns records that have matching values in both tables  
Left\(outer\) join:return all records from the left table and the matched records from the right table  
Right\(outer\) join: return all records from the right table and the matched records from the left table  
Full\(outer\) join: return all records when there is a match in either left or right table  
  
Managers with at least 5 direct reports  
  
  
  
  
Option 1:  
思路：可以通过inner join来确保另一个field满足条件 . check 1point3acres for more.  
Select name from employee as t1 join  
\(select managerid from employee  
Group by managerid  
Having count\(managerid\)&gt;=5\) as t2  
On t1.id=tj2.managerid  
  
Option 2:  
思路：可以不join而通过where限制另一个field的条件 \(subquery\)  
Select name from employee  
Where id in \(select managerid from employee  
Group by managerid  
Having count\(\*\)&gt;=5\)  
  
  
The use of offset  
Get nth salary  
Answer:  
CREATE FUNCTION getNthHighestSalary\(N INT\) RETURNS INT  
BEGIN  
DECLARE off\_var INT;  
SET off\_var = N-1;  
RETURN \(-baidu 1point3acres  
\# Write your MySQL query statement below.  
SELECT DISTINCT  
salary. 1point3acres  
FROM  
Employee  
ORDER BY Salary DESC  
LIMIT 1 OFFSET off\_var  
\);  
END  
  
Offset will offset number of rows and start reading the table.  
  
Ranking  
  
  
  
SELECT  
    Department.name AS 'Department',  
    Employee.name AS 'Employee',  
    Salary  
FROM  
    Employee  
        JOIN. 1point3acres  
    Department ON Employee.DepartmentId = Department.Id. From 1point 3acres bbs  
WHERE  
    \(Employee.DepartmentId , Salary\) IN  
    \(   SELECT  
            DepartmentId, MAX\(Salary\)  
        FROM  
            Employee  
        GROUP BY DepartmentId  
        \)  
  
It looks like we can't use e as a table name after from clause  
And we need parenthesis when have two fields in where conditions  
Where \(employee.departmentid,salary\)  
  
Department Top 3 Salaries  
在subquery里用自己和自己比较 （比较salary）  
  
  
Option 1:  
Select dep.Name as Department, emp.Name as Employee, emp.Salary from Department dep,  
Employee emp where emp.DepartmentId=dep.Id and  
\(Select count\(distinct Salary\) From Employee where DepartmentId=dep.Id and Salary&gt;emp.Salary\)&lt;3  
  
Option 2:  
select d.Name Department, e1.Name Employee, e1.Salary  
from Employee e1  
join Department d. check 1point3acres for more.  
on e1.DepartmentId = d.Id  
where  \(select count\(distinct\(e2.Salary\)\) . 1point3acres  
                  from Employee e2  
                  where e2.Salary &gt; e1.Salary  
                  and e1.DepartmentId = e2.DepartmentId  
                  \)&lt;3;. check 1point3acres for more.  
  
Option 3: Oracle  
SELECT Department, Employee, Salary FROM \(  
SELECT DENSE\_RANK\(\) OVER \(PARTITION BY d.Name ORDER BY e.Salary DESC\) AS Rank, d.Name AS Department, e.Name AS Employee, e.Salary, d.Id FROM Department d. 1point3acres  
JOIN \(SELECT \* FROM Employee\) e ON d.Id = e.DepartmentId  
\)a  
WHERE Rank &lt;= 3  
ORDER BY Id  
  
Rank scores  
  
  
  
  
Side knowledge:  
DENSE\_RANK computes the rank of a row in an ordered group of rows and returns the rank as a NUMBER . The ranks are consecutive integers beginning with 1. The largest rank value is the number of unique values returned by the query. ... Rows with equal values for the ranking criteria receive the same rank.  
  
Answer:  
SELECT SCORE, \(SELECT COUNT\(DISTTINCT SCORE\) FROM SCORES WHERE SCORE&gt;=S.SCORE\) RANK  
FROM SCORES S  
ORDER BY SCORE DESC  
  
Metric Calculation  
  
注意在计算一个metric涉及到除法的时候，用ifnull用0代替null  
注意在calculate题中，只是提取两个table里的内容不需要join两个table的话，可以直接select from 两个table而不需要让两个table join起来  
Select ifnull\(round\(count\(distinct requester\_id,acceper\_id\)/count\(distinct sender\_i,send\_to\_id\),2,\),0\) as accept\_Rate  
From rerquest\_accepted,friend\_request  
  
  
  
思路：当遇到这种复杂问题时候，先考虑select 呈现最后想要的table，然后逐渐join 提取不同的fields  
Answer:. 1point3acres  
Solve this problem by 3 steps as below  
        1. Calculate the company's average salary in every month.  
        Select avg\(amount\) as company\_avg, date\_format\(pay\_date,'%Y-%m'\) as pay\_month from salary  
        Group by date\_format\(pay\_date,'%Y-%m'\)  
        2. Calculate the each department's average salary in every month  
        Select department\_id,avg\(amount\) as department\_avg, date\_format\(pay\_date,'%Y-%m'\) as pay\_month from salary  
        Join employee on salary.employee\_id=employee.employee\_id  
        Group by department\_id,pay\_month  
        3. At last, combine the above two query and join them on department\_salary.pay\_month=company\_salary.pay\_month  
  
        select department\_salary.pay\_month, department\_id,  
case  
  when department\_avg&gt;company\_avg then 'higher'  
  when department\_avg&lt;company\_avg then 'lower'  
  else 'same'  
end as comparison  
from  
\(  
  select department\_id, avg\(amount\) as department\_avg, date\_format\(pay\_date, '%Y-%m'\) as pay\_month  
  from salary join employee on salary.employee\_id = employee.employee\_id  
  group by department\_id, pay\_month  
\) as department\_salary  
join  
\(  
  select avg\(amount\) as company\_avg,  date\_format\(pay\_date, '%Y-%m'\) as pay\_month from salary group by date\_format\(pay\_date, '%Y-%m'\)  
\) as company\_salary  
on department\_salary.pay\_month = company\_salary.pay\_month  
         
        Winning candidate  
         
  
思路：可以通过join的方式去找符合条件的id，也可以通过where语句去限制另一个table 找到符合条件的id  
  
Option 1:  
SELECT  
    name AS 'Name'  
FROM  
    Candidate,,,  
        JOIN  
    \(SELECT  
        Candidateid  
    FROM  
        Vote  
    GROUP BY Candidateid  
    ORDER BY COUNT\(\*\) DESC  
    LIMIT 1\) AS winner  
WHERE  
    Candidate.id = winner.Candidateid  
;  
  
Option 2:  
Select name  
From candidate  
Where id=\(select candidateid  
From vote  
Group by candidateid  
Order by count\(\*\) desc  
Limit 1\)  
  
Option 3:  
Select name From candidate join vote on  
Candidate.id=vote.candidateid  
Group by candidateid  
Order by count\(\*\) desc  
Limit 1  \(not efficient\)  
  
Find cumulative salary of an employee  
  
  
  
  
select a.id, a.month, sum\(b.salary\) from employee a, employee b  
where a.id = b.id and a.month &gt;= b.month  
and a.month &lt; \(select max\(month\) from employee c  
where a.id = c.id\)  
group by a.id, a.month  
  
One more way of doing it using ROW\_NUMBER:  
WITH s AS  
\(SELECT Id,Month,Salary,  
Sum\(Salary\) OVER \(PARTITION BY Id ORDER BY Month\) as SumSal,  
ROW\_NUMBER\(\) OVER \(PARTITION BY id ORDER BY id ASC, month DESC\) rn  
FROM emp1\)  
SELECT Id,Month,SumSal as Salary  
FROM s  
WHERE rn &gt; 1  
  
Ifnull, if statement  
  
  
Answer:  
Select T.id,  
If\(isnull\(T.p\_id\),'Root',if\(T.id in \(select p\_id from tree\),'Inner','Leaf'\)\) Type  
From tree T  
. From 1point 3acres bbs  
  
Answer:  
Select x,y,z  
Case when x+y&lt;=z OR  
x+z&lt;=y or. check 1point3acres for more.  
y+z&lt;=x  
Then 'no'  
Else 'yes'  
End as 'triangle'  
From traingle  
  
Get highest answer rate question  
利用subquery自己制作一个表格来select  
. check 1point3acres for more.  
  
  
Answer:  
Select question\_id as survey\_log  
From  
\(select question\_id, sum\(case when action='show' then 1 else 0 end\) as num\_show,  
Sum\(case when action='answer' then 1 else 0 end\) as num\_answer  
From survey\_log  
Group by question\_id\) as tbl  
Order by \(num\_answer/num\_show\) DESC LIMIT1  
  
How to deal with null value: 注意要考虑到null的情况也是&lt;1000 ，但是系统不会记入  
  
  
Answer:  
SELECT NAME,BONUS  
FROM EMPLOYEE LEFT JOIN BONUS  
ON EMPLOYEE.EMPID=BONUS.EMPID  
WHERE BONUS&lt;1000 OR BONUS IS NULL  
  
不可以用where as where is inner join  
Select name,bonus from employee.bonus  
Where …  
  
  
  
Answer:  
Select name from customer  
Where referr\_id&lt;&gt;2 or referee\_id is null  
  
Investments in 2016  
  
  
  
Answer:  
利用subquery  
SELECT SUM\(INSURANCE.TIV\_2016\) AS TIV\_2016  
FROM INSURANCE  
WHERE INSURANCE.TIV\_2015 IN   \#注意语法 要先写这个field然后in 而不是where （select）  
\(SELECT  
TIV\_2015  
FROM INSURANCE  
GROUP BY TIV\_2015  
HAVING COUNT\(\*\)&gt;1  
\)  
AND CONCAT\(LAT,LON\) IN  
\(SELECT  
CONCAT\(LAT,LON\)  
FROM INSURANCE  
GROUP BY LAT,LON  
HAVING COUNT\(\*\)=1\)  
  
UDPATE Sentence in SQL  
  
Answer:  
UPDATE SALARY  
SET  
SEX=CASE SEX  
WHEN 'M' THEN 'F'  
ELSE 'M'  
END;  
  
hints：  
当我们要选取second biggest number或者只出现了一次的biggest nb的时候，注意可以先select max 然后通过subquery的形式进一步限制条件  
  
  
  
Answer:  
SELECT  
    MAX\(num\) AS num  
FROM  
    \(SELECT. check 1point3acres for more.  
        num  
    FROM  
        number  
    GROUP BY num  
    HAVING COUNT\(num\) = 1\) AS t  
  
  
  
  
Answer:  
SELECT  
    America, Asia, Europe  
FROM  
    \(SELECT @as:=0, @am:=0, @eu:=0\) t,  
    \(SELECT  
        @as:=@as + 1 AS asid, name AS Asia  
    FROM  
        student  
    WHERE. From 1point 3acres bbs  
        continent = 'Asia'  
    ORDER BY Asia\) AS t1  
        RIGHT JOIN  
    \(SELECT  
        @am:=@am + 1 AS amid, name AS America  
    FROM  
        student  
    WHERE  
        continent = 'America'  
    ORDER BY America\) AS t2 ON asid = amid  
        LEFT JOIN  
    \(SELECT  
        @eu:=@eu + 1 AS euid, name AS Europe  
    FROM  
        student  
    WHERE  
        continent = 'Europe'  
    ORDER BY Europe\) AS t3 ON amid = euid  
  
Second degree follower  
  
Basically is to count follower's follower  
Option 1:  
Select distinct follower, num from follow,  
\(select followee, count\(distinct follower\) as num from follow  
Group by followee\) t  
Where follower=t.followee  
Order by follower  
  
Option 2:  
Select f1.follower, count\(distinct f2.follower\) as num  
From follow f1  
Join follow f2 on f1.follower=f2.followee  
Group by f1.follower  
Order by f1.follower  
  
  
  
  
Select min\(abs\(p1.x-p2.x\)\) as shortest from point as P1  
JOIN POINT AS P2 ON P1.X &lt;&gt;P2.X  
  
  
  
SELECT ROUND\(SQRT\(MIN\(POW\(A.X-B.X,2\)+POW\(A.Y-B.Y,2\)\)\),2\) SHORTEST  
FROM POINT\_2D A,POINT\_2D B  
WHERE \(A.X,A.Y\)!=\(B.X,B.Y\)  
  
MULTIPLE TABLES JOIN  
  
两种做法：  
三个表格的关系可能是，第一个table和第三个有common field 然后第二个和第三个有common field。这是可以先把第一个和第三个连接起来，然后用连接后的表和第二个join  
  
找另一个表的info不再这个表上的可以考虑用subquery  
  
  
  
  
Option 1:  
Select s.name from salesperson s  
Where s.sales\_id not in \(select o.sales\_id from orders o left join company c on o.com\_id=c.com\_id  
Where c.name='RED'\)  
  
Option 2:  
SELET NAME FROM SALESPERSON WHERE NAME NOT IN\(  
SELECT S.NAME FROM SALESPERSON S,COMPANY C, ORDERS O  
WHERE S.SALES\_ID=O.SALES\_ID AND C.COM\_ID=O.COM\_ID AND C.NAME='RED'\)  
  
Friend requests: who has the most friends  
  
  
Friend request example. No difference between requester and accepter then we can use union  
Select id1 as id, count\(id2\) as num from  
\(select requester\_id as id1, acccpter\_id as id2 from request\_accepted  
Union  
Select accepter\_id as id1, requester\_id as id2 from  
Request\_accepted\) temp1  
Group by id1  
Order by num desc limit 1  
  
  
  
Answer:  
Select customer\_number from orders  
Group by custoemr\_number  
Order by count\(\*\) desc limit 1  
  
How to join Consecutive numbers on id  
  
  
注意：  
join的关键不在于每个表格都要在不同的位置出现 在下题其实s2，s3并不重要，最重要的是s1的位置，s1一定要在第一位，第二位，第三位都出现，就完成了  
Option  
SELECT DISTINCT S1.\*  
FROM stadium S1  
JOIN stadium S2     \#注意join语句是可以直接join一个以上的表然后再一起写on条件  
JOIN stadium S3  
ON \(\(S1.id = S2.id - 1 AND S1.id = S3.id -2\)  \#s1&lt;s2&lt;s3    s1在末尾  
OR \(S3.id = S1.id - 1 AND S3.id = S2.id -2\)    \#s3&lt;s1&lt;s2    s1在中间  
OR \(S3.id = S2.id - 1 AND S3.id = S1.id -2\)\)   \#s3&lt;s2&lt;s1    s1在头顶  
WHERE S1.people &gt;= 100  
AND S2.people &gt;= 100  
AND S3.people &gt;= 100. 1point3acres  
ORDER BY S1.id;-baidu 1point3acres  
. check 1point3acres for more.  
Option 2  
Select s1.\* from stadium as s2, stadium as s2, stadium as s3. From 1point 3acres bbs  
Where \(\(s1.id+1=s2.id  
And s1.id+2=s3.id\)  
Or  
\(s1.id-1=s2.id  
And s1.id+1=s3.id\)  
Or  
\(s1.id-2=s2.id  
And s1.id-1=s3.id\)\)  
And s1.people&gt;=100  
And s2.people&gt;=100  
And s3.people&gt;=100  
Group by s1.id

