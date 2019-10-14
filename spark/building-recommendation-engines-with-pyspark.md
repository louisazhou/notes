---
description: ALS
---

# Building Recommendation Engines with Pyspark

{% embed url="https://www.datacamp.com/courses/recommendation-engines-in-pyspark" %}

## Collaborative Filtering vs. Content-based Filtering

### Content-Based Filtering

Based on features of items

* Genre: Comedy, Action, Drama
* Animation: Animated, Not-animated
* Language: English, Spanish, Korean
* Decade Produced: 1950s. 1980s
* Actors: Meryl Streep, Tom Hanks

### Collaborative Filtering

Based on similar user preferences

## implicit vs explicit ratings



## Data Preparation 

### Get Integer IDs

1. Extract unique userIds and movieIds
2. Assign unique integers to each id
3. Rejoin unique integer ids back to the ratings data

```python
#1
users = long_ratings.select('userId').distinct()
user.show()

#2
# Tell Spark to convert the columns to the proper data types
ratings = ratings.select(ratings.userId.cast("integer"), ratings.movieId.cast("integer"), ratings.rating.cast("double"))

from pyspark.sql.functions import monotonically_increasing_id
users = users.coalesce(1)
#repartition the dataframe into one partition
users = users.withColumn("userIntId", monotonically_increasing_id()).persist()
#persist ensure the new integer IDs persist.
users.show()

movies = long_ratings.select("variable").distinct()
movies = movies.coalesce(1)
movies = movies.withColumn("movieId", monotonically_increasing_id()).persist()
movies.show()

#3
ratings_w_int_ids = long_ratings.join(users, "userId", "left").join(movies, "variable", "left")
ratings_w_int_ids.show()

# or alternatively 3
from pyspark.ml.functions import col

ratings_data = ratings_w_int_ids.select(
col("userIntId").alias("userid"),
col("variable").alias("movieId"),
col("rating"))

ratings_data.show()
```

## ALS Parameters and Hyperparameters

```python
als_model = ALS(userCol="userId", itemCol="movieId", ratingCol="rating",
rank=25, maxIter=100, regParam=.05, alpha=40,
nonnegative=True,
coldStartStrategy="drop",
implicitPrefs=False)

# Fit ALS to training dataset
model = als.fit(training_data)
# Generate predictions on test dataset
predictions = model.transform(test_data)
```

Arguments

* userCol: Name of column that contains user ids
* itemCol: Name of column that contains item ids
* ratingCol: Name of column that contains ratings

Hyperparameters

* rank, k: number of latent features
* maxIter: number of iterations
* regParam: Lambda; regularization parameter, term added to error matrix to avoid overfitting the training data
* alpha: Only used with implicit ratings.
* nonnegative = True: Ensures positive numbers
* coldStartStrategy = "drop": Addresses issues with test/train split; only use users that have ratings in both training and testing set, and not to use users that only appear in the testing set to calculate RMSE.

```python
# Split the ratings dataframe into training and test data
(training_data, test_data) = ratings.randomSplit([0.8, 0.2], seed=1234)

# Set the ALS hyperparameters
from pyspark.ml.recommendation import ALS
als = ALS(userCol="userId", itemCol="movieId", ratingCol="rating", rank = 10, maxIter = 15, regParam = .1,
          coldStartStrategy="drop", nonnegative = True, implicitPrefs = False)

# Fit the mdoel to the training_data
model = als.fit(training_data)

# Generate predictions on the test_data
test_predictions = model.transform(test_data)
test_predictions.show()
```

### Build RMSE Evaluator

An RMSE of 0.633 means that on average the model predicts 0.633 above or below values of the original ratings matrix.

```python
# Import RegressionEvaluator
from pyspark.ml.evaluation import RegressionEvaluator

evaluator = RegressionEvaluator(metricName="rmse", labelCol="rating", 
predictionCol="prediction")

# Evaluate the "test_predictions" dataframe
RMSE = evaluator.evaluate(test_predictions)

# Print the RMSE
print (RMSE)
```

## Dataset

### MovieLens - Explicit

{% embed url="https://grouplens.org/datasets/movielens/" %}

### Sparsity

$$
\text {Sparsity}=\frac{\text {Number of Ratings in Matrix}}{\text {(Number of Users)} \times( \text {Number of Movies)} }
$$

```python
# Number of ratings in matrix
numerator = ratings.count()

# Distinct users and movies
users = ratings.select("userId").distinct().count()
movies = ratings.select("movieId").distinct().count()

# Number of ratings matrix could contain if no empty cells
denominator = users * movies

#Calculating sparsity
sparsity = 1 - (numerator*1.0 / denominator)
print ("Sparsity: "), sparsity
```

### Explore with aggregation function

{% code-tabs %}
{% code-tabs-item title="for data exploration" %}
```python
# Min num ratings for movies
print("Movie with the fewest ratings: ")
ratings.groupBy("movieId").count().select(min("count")).show()

# Avg num ratings per movie
print("Avg num ratings per movie: ")
ratings.groupBy("movieId").count().select(avg("count")).show()

# Min num ratings for user
print("User with the fewest ratings: ")
ratings.groupBy("userId").count().select(min("count")).show()

# Avg num ratings per users
print("Avg num ratings per user: ")
ratings.groupBy("userId").count().select(avg("count")).show()
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## ALS model buildout on MovieLens Data

### ParamGridBuilder

```python
# Imports ParamGridBuilder package
from pyspark.ml.tuning import ParamGridBuilder# Creates a ParamGridBuilder, and adds hyperparameters and values
param_grid = ParamGridBuilder()
.addGrid(als.rank, [5, 40, 80, 120])
.addGrid(als.maxIter, [5, 100, 250, 500])
.addGrid(als.regParam, [.05, .1, 1.5])
.build()
```

### CrossValidator

```python
# Imports CrossValidator package
from pyspark.ml.tuning import CrossValidator
# Creates cross validator and tells Spark what to use when training # and evalua
cv = CrossValidator(estimator = als,
estimatorParamMaps = param_grid,
evaluator = evaluator,
numFolds = 5)
```

### RandomSplit

```python
# Create training and test set (80/20 split)
(training, test) = movie_ratings.randomSplit([0.8, 0.2])

# Build generic ALS model without hyperparameters
als = ALS(userCol="userId", itemCol="movieId", ratingCol="rating",
coldStartStrategy="drop", nonnegative = True,
implicitPrefs = False)
```

### In-order

```python
# Tell Spark what values to try for each hyperparameter
from pyspark.ml.tuning import ParamGridBuilder
param_grid = ParamGridBuilder().addGrid(als.rank, [5, 40, 80, 120]).addGrid(als.maxIter, [5, 100, 250, 500]).addGrid(als.regParam, [.05, .1, 1.5]).build()

# Tell Spark how to evaluate model performance
evaluator = RegressionEvaluator(metricName="rmse", labelCol="rating",predictionCol="prediction")

# Build cross validation step using CrossValidator
from pyspark.ml.tuning import CrossValidator
cv = CrossValidator(estimator = als,estimatorParamMaps = param_grid,evaluator = evaluator,numFolds = 5)

# Run the cv on the training data
model = cv.fit(training)

# Extract best combination of values from cross validation
best_model = model.bestModel

# Generate test set predictions and evaluate using RMSE
predictions = best_model.transform(test)
rmse = evaluator.evaluate(predictions)

# Print evaluation metrics and model parameters
print ("**Best Model**")print ("RMSE = "), rmse
print (" Rank: "), best_model.rank
print (" MaxIter: "), best_model._java_obj.parent().getMaxIter()
print (" RegParam: "), best_model._java_obj.parent().getRegParam()
```

### Model Performance Evaluation and Cleanup

```python
# Generate n recommendations for all users
recommendForAllUsers(n)
 # n is an integer
 
 ALS_recommendations.show()
 # 这里出来的recommendations是一个metadata
 
 #Cleaning Up Recommendation Output
 ALS_recommendations.registerTempTable("ALS_recs_temp")
 
 exploded_recs = spark.sql("SELECT uderId,
 explode(recommendations) AS MovieRec
 FROM ALS_recs_temp")
 exploded_recs.show()
 
 clean_recs = spark.sql("SELECT userId,
 movieIds_and_ratings.movieId AS movieId,
 movieIds_and_ratings.rating AS prediction
 FROM ALS_recs_temp
 LATERAL VIEW explode(recommendations) exploded_table
 AS movieIds_and_ratings")
 clean_recs.show()
 
 
  #Filtering Recommendations
 clean_recs.join(movie_info, ["movieId"], "left").show()

 clean_recs.join(movie_ratings, ["userId", "movieId"], "left")
.filter(movie_ratings.rating.isNull()).show()
```

## Dataset

### MillionSongs-implicit 

[http://millionsongdataset.com/tasteprofile/](http://millionsongdataset.com/tasteprofile/)



