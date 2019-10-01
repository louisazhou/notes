# Order Query

## Online Compiler

{% embed url="https://rextester.com/l/postgresql\_online\_compiler" %}



```sql
CREATE TABLE zyz_customers
    ("customerid" int, "country" varchar(11))
;
    
INSERT INTO zyz_customers
    ("customerid", "country")
VALUES
    (1, 'Germany'),
    (2, 'Mexico'),
    (3, 'Mexico'),
    (4, 'UK'),
    (5, 'Sweden'),
    (6, 'Germany'),
    (7, 'France'),
    (8, 'Spain'),
    (9, 'France'),
    (10, 'Canada'),
    (11, 'UK'),
    (12, 'Argentina'),
    (13, 'Mexico'),
    (14, 'Switzerland'),
    (15, 'Brazil'),
    (16, 'UK'),
    (17, 'Germany'),
    (18, 'France'),
    (19, 'UK'),
    (20, 'Austria')
;



CREATE TABLE zyz_categories
    ("categoryid" int, "categoryname" varchar(14))
;
    
INSERT INTO zyz_categories
    ("categoryid", "categoryname")
VALUES
    (1, 'Beverages'),
    (2, 'Condiments'),
    (3, 'Confections'),
    (4, 'Dairy Products'),
    (5, 'Grains/Cereals'),
    (6, 'Meat/Poultry'),
    (7, 'Produce'),
    (8, 'Seafood')
;

CREATE TABLE zyz_employees
    ("employeeid" int, "birthdate" timestamp)
;
    
INSERT INTO zyz_employees
    ("employeeid", "birthdate")
VALUES
    (1, '1968-12-08'),
    (2, '1952-02-19'),
    (3, '1963-08-30'),
    (4, '1958-09-19'),
    (5, '1955-03-04')
;



CREATE TABLE zyz_orderdetails
    ("orderid" int, "productid" int, "quantity" int)
;
    
INSERT INTO zyz_orderdetails
    ("orderid", "productid", "quantity")
VALUES
    (10248, 11, 12),
    (10248, 42, 10),
    (10248, 72, 5),
    (10249, 14, 9),
    (10249, 51, 40),
    (10250, 41, 10),
    (10250, 51, 35),
    (10250, 65, 15),
    (10251, 22, 6),
    (10251, 57, 15),
    (10251, 65, 20),
    (10252, 20, 40),
    (10252, 33, 25),
    (10252, 60, 40),
    (10253, 31, 20),
    (10253, 39, 42),
    (10253, 49, 40),
    (10254, 24, 15),
    (10254, 55, 21),
    (10254, 74, 21),
    (10255, 2, 20),
    (10255, 16, 35),
    (10255, 36, 25),
    (10255, 59, 30),
    (10256, 53, 15),
    (10256, 77, 12),
    (10257, 27, 25),
    (10257, 39, 6),
    (10257, 77, 15),
    (10258, 2, 50),
    (10258, 5, 65),
    (10258, 32, 6),
    (10259, 21, 10),
    (10259, 37, 1),
    (10260, 41, 16),
    (10260, 57, 50),
    (10260, 62, 15),
    (10260, 70, 21),
    (10261, 21, 20),
    (10261, 35, 20),
    (10262, 5, 12),
    (10262, 7, 15),
    (10262, 56, 2),
    (10263, 16, 60),
    (10263, 24, 28),
    (10263, 30, 60),
    (10263, 74, 36),
    (10264, 2, 35),
    (10264, 41, 25),
    (10265, 17, 30),
    (10265, 70, 20),
    (10266, 12, 12),
    (10267, 40, 50),
    (10267, 59, 70),
    (10267, 76, 15),
    (10268, 29, 10),
    (10268, 72, 4),
    (10269, 33, 60),
    (10269, 72, 20),
    (10270, 36, 30),
    (10270, 43, 25),
    (10271, 33, 24),
    (10272, 20, 6),
    (10272, 31, 40),
    (10272, 72, 24),
    (10273, 10, 24),
    (10273, 31, 15),
    (10273, 33, 20),
    (10273, 40, 60),
    (10273, 76, 33),
    (10274, 71, 20),
    (10274, 72, 7),
    (10275, 24, 12),
    (10275, 59, 6),
    (10276, 10, 15),
    (10276, 13, 10),
    (10277, 28, 20),
    (10277, 62, 12),
    (10278, 44, 16),
    (10278, 59, 15),
    (10278, 63, 8),
    (10278, 73, 25),
    (10279, 17, 15),
    (10280, 24, 12),
    (10280, 55, 20),
    (10280, 75, 30),
    (10281, 19, 1),
    (10281, 24, 6),
    (10281, 35, 4),
    (10282, 30, 6),
    (10282, 57, 2),
    (10283, 15, 20),
    (10283, 19, 18),
    (10283, 60, 35),
    (10283, 72, 3),
    (10284, 27, 15),
    (10284, 44, 21),
    (10284, 60, 20),
    (10284, 67, 5)
;



CREATE TABLE zyz_orders
    ("orderid" int, "customerid" int)
;
    
INSERT INTO zyz_orders
    ("orderid", "customerid")
VALUES
    (10248, 90),
    (10249, 81),
    (10250, 34),
    (10251, 84),
    (10252, 76),
    (10253, 34),
    (10254, 14),
    (10255, 68),
    (10256, 88),
    (10257, 35),
    (10258, 20),
    (10259, 13),
    (10260, 55),
    (10261, 61),
    (10262, 65),
    (10263, 20),
    (10264, 24),
    (10265, 7),
    (10266, 87)
;



CREATE TABLE zyz_products
    ("productid" int, "categoryid" int, "price" int)
;
    
INSERT INTO zyz_products
    ("productid", "categoryid", "price")
VALUES
    (1, 1, 18),
    (2, 1, 19),
    (3, 2, 10),
    (4, 2, 22),
    (5, 2, 21.35),
    (6, 2, 25),
    (7, 7, 30),
    (8, 2, 40),
    (9, 6, 97),
    (10, 8, 31),
    (11, 4, 21),
    (12, 4, 38),
    (13, 8, 6),
    (14, 7, 23.25),
    (15, 2, 15.5),
    (16, 3, 17.45),
    (17, 6, 39),
    (18, 8, 62.5),
    (19, 3, 9.2),
    (20, 3, 81),
    (21, 3, 10),
    (22, 5, 21),
    (23, 5, 9),
    (24, 1, 4.5),
    (25, 3, 14),
    (26, 3, 31.23),
    (27, 3, 43.9),
    (28, 7, 45.6),
    (29, 6, 123.79),
    (30, 8, 25.89),
    (31, 4, 12.5),
    (32, 4, 32),
    (33, 4, 2.5),
    (34, 1, 14),
    (35, 1, 18),
    (36, 8, 19),
    (37, 8, 26),
    (38, 1, 263.5),
    (39, 1, 18),
    (40, 8, 18.4),
    (41, 8, 9.65),
    (42, 5, 14),
    (43, 1, 46),
    (44, 2, 19.45),
    (45, 8, 9.5),
    (46, 8, 12),
    (47, 3, 9.5),
    (48, 3, 12.75),
    (49, 3, 20)
;

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
# total sales (quantity) by country
SELECT a.country, SUM(CASE WHEN c.quantity is not null THEN c.quantity ELSE 0 END) AS total_sales
FROM zyz_customers a
LEFT JOIN zyz_Orders b
ON a.customerid=b.customerid
LEFT JOIN zyz_Orderdetails c
ON b.orderid=c.orderid
GROUP BY 1
ORDER BY 1
```

这里，如果sum所有的null就是null，如果count所有的null是0，但average了还是null

如果两个都改成inner join，那么就只有4行，因为0的行不会出现。面试题：你用了inner join，那么如果你用left join会如何？ 

如果只是第二个改成inner join，效果一样的，4行

```sql
# revenue by country
SELECT a.country, SUM(CASE WHEN c. quantity is not null then c.quantity*d.price ELSE 0 END) as revenue
FROM zyz_Customers a
LEFT JOIN zyz_Orders b
ON a.customerid=b.customerid
LEFT JOIN zyz_Orderdetails c
ON b.orderid=c.orderid
LEFT JOIN zyz_Products d
ON c.productid=d.productid
GROUP BY 1
ORDER BY 1
```

注意groupby和orderby的顺序

```sql
# revenue by category in Austria
SELECT a.country, d.categoryid, SUM(CASE WHEN c.quantity is not null then c.quantity*d.price ELSE 0 END) as revenue
FROM zyz_Customers a
JOIN zyz_Orders b
ON a.customerid=b.customerid
JOIN zyz_Orderdetails c
ON b.orderid=c.orderid
JOIN zyz_Products d
ON c.productid=d.productid
WHERE a.country='Austria'
GROUP BY 1,2
ORDER BY 1,2
```

这里还要注意where的顺序，因为ROM--&gt;WHERE--&gt;GROUP BY--&gt;HAVING--&gt;SELECT--&gt;ORDER BY 所以WHERE要在FROM的都执行完了才会执行

```sql
#average spend per customer (total_spend/total_order)
SELECT a.customerid, CASE WHEN COUNT(DISTINCT b.orderid)>0 then SUM(c.quantity*d.price)/COUNT(DISTINCT b.orderid) ELSE null end as average
FROM zyz_customers as a
JOIN zyz_orders b ON a.customerid=b.customerid
JOIN zyz_orderdetails c ON b.orderid=c.orderid
JOIN zyz_products d ON c.productid=d.productid
GROUP BY 1
ORDER BY 1
```

这里要注意，orderid在每个表格里不是distinct的，因为orders里，同一个customerid会买很多单，而orderdetails是按照单个商品分的，所以不管用这两个的哪个表格，在除的时候都需要distinct. 还要注意，SQL里的除法最好都写成case when的形式，因为不知道什么时候分母就是0或者null或者负数了。

```sql
# top 5 most popular products in each category
# in interview, should ask 'how is popular defined' and in this case, it's the quantity
SELECT categoryid, productid, sales, row_num
FROM (
    SELECT a.categoryid, c.productid, c.sales, row_number() over (partition by a.categoryid order by c.sales desc) as row_num
    FROM zyz_categories a
    JOIN
        (SELECT DISTINCT categoryid, productid 
         FROM zyz_products) AS b on a.categoryid=b.categoryid
    JOIN
        (SELECT productid, sum(quantity) as sales
         FROM zyz_orderdetails
         GROUP BY 1) AS c on b.productid=c.productid
    ) AS fun
WHERE row_num<=5
ORDER BY 1,4

# Option 2
SELECT *
FROM (
    SELECT categoryid, productid, sales, row_number() over (partition by categoryid order by sales desc) as row_num
    FROM (SELECT  a.categoryid, a.productid, SUM(b.quantity) as sales
         FROM zyz_products a
         INNER JOIN zyz_orderdetails b 
         ON a.productid=b.productid
         GROUP by 1,2) as fun
    ) as fun1
WHERE row_num<=5
ORDER BY 1,4
```

这里需要注意的是，尽管最后不需要call function fun，也需要给from后面的这一大块一个alias。

```sql
SELECT a.customerid, 
       SUM(CASE WHEN e.categoryname='Beverages' then c.quantity else 0 END) as bev_sales,
       SUM(CASE WHEN e.categoryname='Condiments' then c.quantity else 0 END) as condi_sales,
       SUM(CASE WHEN e.categoryname not in ('Beverage', 'Condiments') then c.quantity else 0 END) as other_sales
FROM zyz_customers a
INNER JOIN zyz_orders b ON a.customerid=b.customerid
INNER JOIN zyz_orderdetails c ON b.orderid=c.orderid
INNER JOIN zyz_products d ON c.productid=d.productid
INNER JOIN zyz_categories e ON d.categoryid=e.categoryid
GROUP BY 1
ORDER BY 1
```



