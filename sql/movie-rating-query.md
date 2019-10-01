# Movie Rating Query

movie

| mid | title | year | director |
| :--- | :--- | :--- | :--- |
|  |  |  |  |

reviewer

| rid | name |
| :--- | :--- |
|  |  |

rating

| rid | mid | stars | ratingdate |
| :--- | :--- | :--- | :--- |
|  |  |  |  |

```sql
#Q1: Find the titles of all movies directed by steven spieberg
SELECT DISTINCT title
FROM movie
WHERE director='Steven Spielberg'
# 注意 like有时候可能是case sensitive 
# 如果是两个数值，可以用 IN(,)

#Q2 Find all rating years that have a movie that received a rating of 4/5, and sort them in increasing order
SELECT a.year, b.stars
FROM 

#Q3 titles of all movies that have no ratings
SELECT titles
FROM movie
LEFT JOIN rating 
ON movie.id=rating.id
WHERE star is null

#Q4 Find the names of all reviewers who have ratings with a null
SELECT a.name
FROM reviewer a
INNER JOIN rating b
USING rid
WHERE b.stars is null

#Q5 For all cases where the same reviewer rated the same movie twice ONLY
# and gave it a higher rating the second time, return the reviewers name and the title of the movie. 
# 先找到rate2次的人，再在这个table里找到第二次多于第一次的

#Q6 movie at least 1 rating, highest number of stars it received, sort by movie table
SELECT a.title, max(b.stars)
FROM movie a
JOIN rating b
ON a.mid=b.mid
WHERE b.stars is not null
GROUP BY 1
ORDER BY 1

#Q7 find range of rating 

#Q8 难得的需要笛卡尔乘积的

FROM (SELECT a.movie,
```



