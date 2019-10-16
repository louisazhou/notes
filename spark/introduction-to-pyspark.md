# Introduction to Pyspark

{% embed url="https://www.datacamp.com/courses/introduction-to-pyspark" %}

1. Connect to Python

Creating the connection is as simple as creating an instance of the `SparkContext` class. The class constructor takes a few optional arguments that allow you to specify the attributes of the cluster you're connecting to.

```python
# Verify SparkContext
print(sc)

# Print Spark version
print(sc.version)
```

To start working with Spark DataFrames, you first have to create a `SparkSession` object from your `SparkContext`. You can think of the `SparkContext` as your connection to the cluster and the `SparkSession` as your interface with that connection.

2. Create a SparkSession

```python
# Import SparkSession from pyspark.sql
from pyspark.sql import SparkSession

# Create my_spark
my_spark = SparkSession.builder.getOrCreate()

# Print the tables in the catalog
print(spark.catalog.listTables())
```

`SparkSession` has an attribute called `catalog` which lists all the data inside the cluster. This attribute has a few methods for extracting different pieces of information.

One of the most useful is the `.listTables()`method, which returns the names of all the tables in your cluster as a list.

3. Pandas Dataframe &lt;-&gt; Spark Dataframe

`.createDataFrame()` method takes a `pandas` DataFrame and returns a Spark DataFrame. `.toPandas()`does the opposite. 

```python
# Convert the results to a pandas DataFrame
pd_counts = flight_counts.toPandas()
```

Spark Dataframe的内容存在locally，不在`SparkSession` catalog. This means that you can use all the Spark DataFrame methods on it, but you can't access the data in other contexts. 比如不能直接在spark dataframe上`.sql()` ，必须`.createTempView()` 或者 `.createOrReplaceTempView()`。

```python
# Create pd_temp
pd_temp = pd.DataFrame(np.random.random(10))

# Create spark_temp from pd_temp
spark_temp = spark.createDataFrame(pd_temp)

# Examine the tables in the catalog
print(spark.catalog.listTables())

# Add spark_temp to the catalog
spark_temp.createOrReplaceTempView("temp")

# Examine the tables in the catalog again
print(spark.catalog.listTables())
```

比如上面这个代码，第一次tempview的listtable是\[\]，第二次是\[Table\(name='temp', database=None, description=None, tableType='TEMPORARY', isTemporary=True\)\]

![](../.gitbook/assets/image%20%2828%29.png)

