# Spark SQL

Window Function

```python
# Load trainsched.txt
df = spark.read.csv("trainsched.txt", header=True)

# Create temporary table called table1
df.createOrReplaceTempView('table1')

# Inspect the columns in the table df
spark.sql("DESCRIBE schedule").show()

# window function 
# LEAD function can query more than 1 row in a table without having to self join
query = """
SELECT train_id, station, time
LEAD(time, 1) OVER (ORDER BY time) AS time_next
FROM sched
WHERE train_id=324 """
spark.sql(query).show()

# Add col running_total that sums diff_min col in each group
query = """
SELECT train_id, station, time, diff_min,
SUM(diff_min) OVER (PARTITION BY train_id ORDER BY time) AS running_total
FROM schedule
"""

query = """
SELECT *
ROW_NUMBER() OVER (PARTITION BY train_id ORDER BY time) AS id
FROM schedule
"""

#using dot notation
from pyspark.sql import Window
from pyspark.sql.functions import row_number
df.withColumn(
"id", row_number()
.over(Window.partitionBy('train_id').orderBy('time'))
)

#using a WindowSpec
```

Dot notation and SQL

```python
# Three ways to select 2 columns
df.select('train_id', 'station')
df.select(df.train_d, df.station)
from pyspark.sql.functions import col
df.select(col('train_id'), col('station'))

# 2 ways to rename a column
df.select('train_id', 'station')
    .withColumnRenamed('train_id','train')
    .show(5)
df.select(col('train_id').alias('train'), 'station')

# SQL queries using dot notation
spark.sql('SELECT train_id AS train, station FROM schedule LIMIT 5')
.show()
df.select(col('train_id').alias('train'), 'station')
.limit(5)
.show()

# dot notation
from pyspark.sql.functions import min, max, col
expr = [min(col("time")).alias('start'), max(col("time")).alias('end')]
dot_df = df.groupBy("train_id").agg(*expr)
dot_df.show()

# SQL query 
query = "SELECT train_id, min(time) as start, max(time) as end from schedule GROUP BY train_id"
sql_df = spark.sql(query)
sql_df.show()

# SparkSQL的agg一次只能接1个argument
spark.sql('SELECT train_id, MIN(time) AS start FROM schedule GROUP BY train_id').show()
df.groupBy('train_id').agg({'time':'min'}).withColumnRenamed('min(time)', 'start').show()

# UNIX_TIMESTAMP dot notation
window = Window.partitionBy('train_id').orderBy('time')
dot_df = df.withColumn('diff_min', 
                    (unix_timestamp(lead('time', 1).over(window),'H:m') 
                     - unix_timestamp('time', 'H:m'))/60)
                     
# sparkSQL notation
query = """
SELECT *, 
(unix_timestamp(lead(time, 1) over (PARTITION BY train_id ORDER BY time),'H:m') 
 - unix_timestamp(time, 'H:m'))/60 AS diff_min 
FROM schedule 
"""
sql_df = spark.sql(query)
sql_df.show()
```

Load natural language text

```python
# load text
df = spark.read.text("sherlock.txt")
print(df.first())
print(df.count())

# load parquet
df1 = spark.read.load('sherlock.parquet')  #parquet is hadoop file to store data structures
df1.show(15, truncate=False)

# Lower case operation
df = df1.select(lower(col('value'))) 
print(df.first())

# show column names
df.columns
# Alias operation
df = df1.select(lower(col('value')).alias('v'))

# Replacing text
df = df1.select(regexp_replace('value', 'Mr\.', 'Mr').alias('v')) #"Mr. Holmes." ==> "Mr Holmes."
df = df1.select(regexp_replace('value', 'don\'t', 'do not').alias('v')) #"don't know." ==> "do not know."

# Tokenizing text
df = df2.select(split('v', '[ ]').alias('words'))

# Split characters are discarded
punctuation = "_|.\?\!\",\'\[\]\*()"
df3 = df2.select(split('v', '[ %s]' % punctuation).alias('words'))

# Exploding an array
df4 = df3.select(explode('words').alias('word')) 

```

