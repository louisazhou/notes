# Spark SQL

### Window Function

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

# Words sorted by frequency query
query = """
SELECT word, COUNT(*) AS count FROM df
GROUP BY word
ORDER BY count DESC
"""

#等价的dot
df.groupBy('word') .count() .sort(desc('count')) .explain()
```

### Load natural language text

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

# Removing empty rows
nonblank_df = df.where(length('word') > 0)
# Adding a row id column
df2 = df.select('word', monotonically_increasing_id().alias('id'))

# Partitioning the data
df2 = df.withColumn('title', when(df.id < 25000, 'Preface') .when(df.id < 50000, 'Chapter 1') .when(df.id < 75000, 'Chapter 2')
.otherwise('Chapter 3'))
df2 = df2.withColumn('part', when(df2.id < 25000, 0) .when(df2.id < 50000, 1) .when(df2.id < 75000, 2)
.otherwise(3)) .show()

# Repartitioning on a column
df2 = df.repartition(4, 'part')

# How to know how many chapters in in repartitioning 
df.select('part', 'title').distinct().sort('part').show(truncate=False)
# or the following gives a number 
repart_df.rdd.getNumPartitions()

# Reading pre-partitioned text
df_parts = spark.read.text('sherlock_parts') #每个叫做sherlock的文档

# Word for each row, previous two and subsequent two words
query = """
SELECT
part,
LAG(word, 2) OVER(PARTITION BY part ORDER BY id) AS w1,
LAG(word, 1) OVER(PARTITION BY part ORDER BY id) AS w2,
word AS w3,
LEAD(word, 1) OVER(PARTITION BY part ORDER BY id) AS w4,
LEAD(word, 2) OVER(PARTITION BY part ORDER BY id) AS w5
FROM text
"""
spark.sql(query).where("part = 12").show(10)

# 还可以再加subquery，统计word-tuple的count
# Find the top 10 sequences of five words
query = """
SELECT w1, w2, w3, w4, w5, COUNT(*) AS count FROM (
   SELECT word AS w1,
   LEAD(word, 1) OVER (PARTITION BY part ORDER BY id) AS w2,
   LEAD(word, 2) OVER (PARTITION BY part ORDER BY id)  AS w3,
   LEAD(word, 3) OVER (PARTITION BY part ORDER BY id)  AS w4,
   LEAD(word, 4) OVER (PARTITION BY part ORDER BY id)  AS w5
   FROM text
)
GROUP BY w1, w2, w3, w4, w5
ORDER BY count DESC
LIMIT 10 """
df = spark.sql(query)
df.show()

#   Most frequent 3-tuple per chapter
query = """
SELECT chapter, w1, w2, w3, count FROM
(
  SELECT
  chapter,
  ROW_NUMBER() OVER (PARTITION BY chapter ORDER BY count DESC) AS row,
  w1, w2, w3, count
  FROM ( %s )
)
WHERE row = 1
ORDER BY chapter ASC
""" % subquery
spark.sql(query).show()

# length
query3agg = """
SELECT w1, w2, w3, length(w1)+length(w2)+length(w3) as length FROM (
SELECT
word AS w1,
LEAD(word,1) OVER(PARTITION BY part ORDER BY id ) AS w2, LEAD(word,2) OVER(PARTITION BY part ORDER BY id ) AS w3 FROM df
WHERE part <> 0 and part <> 13
)
GROUP BY w1, w2, w3 ORDER BY length DESC """
spark.sql(query3agg).show(truncate=False)
```

### Caching: 

Eviction Policy  Least Recently Used \(LRU\)   
Caching is a lazy operation. It requires an action to trigger it. 

eg.   
- spark.sql\("select count\(\*\) from text"\).show\(\)  
- partitioned\_df.count\(\)  


```python
df.cache()    #df.persist() df.persist(storageLevel=pyspark.StorageLevel.MEMORY_AND_DISK)
df.unpersist()

#Determining whether a dataframe is cached
df.is_cached

#storage level useDisk useMemory useOffHeap deserialized replication
df.storageLevel

#Caching a table
df.createOrReplaceTempView('df') 
spark.catalog.cacheTable('df')
spark.catalog.isCached(tableName='df')
spark.catalog.dropTempView('table1')
# List the tables
print("Tables:\n", spark.catalog.listTables())

# Uncaching a table
spark.catalog.uncacheTable('df')
spark.catalog.clearCache()
```

### Spark UI

- Spark Task is a unit of execution that runs on a single cpu  
- Spark Stage a group of tasks that perform the same computation in parallel, each task typically running on a different subset of the data  
- Spark Job is a computation triggered by an action, sliced into one or more stages.  
- Jobs, Stages, Storage, Environment, Executors, SQL  
-Storage: in memory, or on disk, across the cluster, at a snapshot in time.

如果是local，从[http://\[DRIVER\_HOST\]:404](http://[DRIVER_HOST]:4040)0开始依次往下。

### Logging primer

```python
import logging
logging.basicConfig(stream=sys.stdout, level=logging.INFO,
format='%(asctime)s - %(levelname)s - %(message)s') 
logging.info("Hello %s", "world")
logging.debug("Hello, take %d", 2)

#OUTPUT
# 2019-03-14 15:92:65,359 - INFO - Hello world
# 因为level=infor，debug不执行

#Logging with DEBUG level
import logging
logging.basicConfig(stream=sys.stdout, level=logging.DEBUG,
format='%(asctime)s - %(levelname)s - %(message)s') 
logging.info("Hello %s", "world")
logging.debug("Hello, take %d", 2)
#OUTPUT
#2018-03-14 12:00:00,000 - INFO - Hello world
#2018-03-14 12:00:00,001 - DEBUG - Hello, take 2

# Log columns of text_df as debug message
logging.debug("text_df columns: %s", text_df.columns)

# Log whether table1 is cached as info message
logging.info("table1 is cached: %s", spark.catalog.isCached(tableName="table1"))

# Log first row of text_df as warning message
logging.warning("The first row of text_df:\n %s", text_df.first())

# Log selected columns of text_df as error message
logging.error("Selected columns: %s", text_df.select("id", "word"))
```

如果用一个timer来对logging计时，

```python
class timer:
    start_time = time.time() 
    step = 0
    
    def elapsed(self, reset=True): 
        self.step += 1
        print("%d. elapsed: %.1f sec %s"
        % (self.step, time.time() - self.start_time))
        if reset: 
            self.reset()

    def reset(self):
        self.start_time = time.time()
```

要注意的是即使是在info的level，debug里的操作其实还是执行了，比如

```python
import logging logging.basicConfig(level=logging.INFO,
format='%(asctime)s - %(levelname)s - %(message)s') 

# < create dataframe df here >

t = timer()
logging.info("No action here.")
t.elapsed()
logging.debug("df has %d rows.", df.count()) 
t.elapsed()

#2018-12-23 22:24:20,472 - INFO - No action here. 
#1. elapsed: 0.0 sec
#2. elapsed: 2.0 sec
```

比较好的做法是，disable action

```python
ENABLED = False

t = timer()
logger.info("No action here.") t.elapsed()
if ENABLED:
    logger.info("df has %d rows.", df.count()) t.elapsed()
```

\(行吧，这章没学懂\)

### Query Plans

```python
# SQL 中的EXPLAIN SELECT * FROM table1解释执行顺序

#Load dataframe and register
df = sqlContext.read.load('/temp/df.parquet')
df.registerTempTable('df')
#Running an EXPLAIN query
spark.sql('EXPLAIN SELECT * FROM df').first()
#等价于下面的
df.explain()
spark.sql("SELECT * FROM df").explain()
#但如果cache()了，df.cache()
#要倒着看执行顺序
```

### Extract, Transform, Select

```python
# Importing the udf function
from pyspark.sql.functions import udf
from pyspark.sql.types import BooleanType
from pyspark.sql.types import StringType, IntegerType, FloatType, ArrayType
# Creating a boolean UDF
short_udf = udf(lambda x: 
True if not x or len(x) < 10 else False, BooleanType())

df.select(short_udf('textdata')\
 .alias("is short"))\
 .show(3)
 
 #Creating an array UDF
 df3.select('word array', in_udf('word array').alias('without endword'))\
  .show(5, truncate=30)
  
  from pyspark.sql.types import StringType, ArrayType
  # Removes last item in array
in_udf = udf(lambda x:
x[0:len(x)-1] if x and len(x) > 1 else [],
ArrayType(StringType()))

# Sparse vector format Indices Values
# Array: [1.0, 0.0, 0.0, 3.0]
# Sparse vector: (4, [0, 3], [1.0, 3.0])

hasattr(x, "toArray") #ensures it's an array by checking it has an attribute 'toArray'
x.numNonzeros()

# Show the rows where doc contains the item '5'
df_before.where(array_contains('doc', '5')).show()

# UDF removes items in TRIVIAL_TOKENS from array
rm_trivial_udf = udf(lambda x:
                     list(set(x) - TRIVIAL_TOKENS) if x
                     else x,
                     ArrayType(StringType()))

# Remove trivial tokens from 'in' and 'out' columns of df2
df_after = df_before.withColumn('in', rm_trivial_udf('in'))\
                    .withColumn('out', rm_trivial_udf('out'))

# Show the rows of df_after where doc contains the item '5'
df_after.where(array_contains('doc','5')).show()
```

### Creating feature data for classification 

```python
try: df.select(bad_udf('outvec').alias('label')).first()
except Exception as e: print(e.__class__) print(e.errmsg)
#debug 用的

#UDF return type must be properly cast
first_udf = udf(lambda x: int(x.indices[0])
if (x and hasattr(x, "toArray") and x.numNonzeros()) else 0,
IntegerType())

df.withColumn('label', first_udf('outvec')).drop('outvec').show(3)
# Add label by applying the first_udf to output column
df_new = df.withColumn('label', first_udf('output'))
```

CountVectorizer is a **Feature Extractor**   
Its input is an **array** of **strings** Its output is a **vector**

```python
#Fitting the CountVectorizer
from pyspark.ml.feature import CountVectorizer 
cv = CountVectorizer(inputCol='words',
  outputCol="features") 
model = cv.fit(df) 
result = model.transform(df)
print(result)
```

### Text Classification 

```python
# 加label
df_true = df.where("endword in ('she', 'he', 'hers', 'his', 'her', 'him')")\ .withColumn('label', lit(1))
df_false = df.where("endword not in ('she', 'he', 'hers', 'his', 'her', 'him')")\ .withColumn('label', lit(0))

# Combining the positive and negative data
df_examples = df_true.union(df_false)

# Splitting the data into training and evaluation sets
df_train, df_eval = df_examples.randomSplit((0.60, 0.40), 42)

# Training
from pyspark.ml.classification import LogisticRegression
logistic = LogisticRegression(maxIter=50, regParam=0.6, elasticNetParam=0.3) 
model = logistic.fit(df_train)
print("Training iterations: ", model.summary.totalIterations)
# Print the number of test examples
print("Number test: ", df_testset.count())


# Import the lit function
from pyspark.sql.functions import lit

# Select the rows where endword is 'him' and label 1
df_pos = df.where("endword = 'him'")\
           .withColumn('label', lit(1))

# Select the rows where endword is not 'him' and label 0
df_neg = df.where("endword <> 'him'")\
           .withColumn('label', lit(0))

# Union pos and neg in equal number
df_examples = df_pos.union(df_neg.limit(df_pos.count()))
print("Number of examples: ", df_examples.count())
df_examples.where("endword <> 'him'").sample(False, .1, 42).show(5)
```

### Predicting and evaluating

```python
# Applying a model to evaluation data
predicted = df_trained.transform(df_test) #返回的结果是一个dataframe

#prediction column: double 不过在这个语境是0或者1
#probability column: vector of length two 是概率值，加起来1 第一个值是estimated probability to be false; second is true

x = predicted.first
print("Right!" if x.label == int(x.prediction) else "Wrong")

#Evaluating classi cation accuracy
model_stats = model.evaluate(df_eval)
type(model_stats) #pyspark.ml.classification.BinaryLogisticRegressionSummary)
print("\nAccuracy: %.2f" % model_stats.areaUnderROC)

# Apply the model to the test data
predictions = df_fitted.transform(df_testset).select(fields)

# Print incorrect if prediction does not match label
for x in predictions.take(8):
    print()
    if x.label != int(x.prediction):
        print("INCORRECT ==> ")
    for y in fields:
        print(y,":", x[y])
```

prediction : 1.0   
label : 1   
endword : him   
doc : \['and', 'pierre', 'felt', 'that', 'their', 'opinion', 'placed', 'responsibilities', 'upon', 'him'\]   
probability : \[0.28537355252312796,0.714626447476872\]

