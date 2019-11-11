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
from pyspark.sql.functions import array, col, explode, lit, struct

def to_long(df, by = ["userId"]): # "by" is the column by which you want the final output dataframe to be grouped by

    cols = [c for c in df.columns if c not in by] 
    
    kvs = explode(array([struct(lit(c).alias("movieId"), col(c).alias("rating")) for c in cols])).alias("kvs") 
    
    long_df = df.select(by + [kvs]).select(by + ["kvs.movieId", "kvs.rating"]).filter("rating IS NOT NULL")
    # Excluding null ratings values since ALS in Pyspark doesn't want blank/null values
             
    return long_df


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
* alpha: Only used with implicit ratings. How much \(in integer\) should add to the model's confidence that a user actually likes the movie/song.
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

{% tabs %}
{% tab title="for data exploration" %}
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
{% endtab %}
{% endtabs %}

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

```python
def add_zeros(df):
# Extracts distinct users
    users = df.select("userId").distinct()
# Extracts distinct songs
    songs = df.select("songId").distinct()
# Joins users and songs, fills blanks with 0
    cross_join = users.crossJoin(items)\
    .join(df, ["userId", "songId"], "left").fillna(0)

return cross_join
```

### Data Exploration

因为这一次的data是implicit，所以需要filter那些非0的来过滤得到aggregation信息

```python
# Min num implicit ratings for a song
print("Minimum implicit ratings for a song: ")
msd.filter(col("num_plays") > 0).groupBy("songId").count().select(min("count")).show()

# Avg num implicit ratings per songs
print("Average implicit ratings per song: ")
msd.filter(col("num_plays") > 0).groupBy("songId").count().select(avg("count")).show()

# Min num implicit ratings from a user
print("Minimum implicit ratings from a user: ")
msd.filter(col("num_plays") > 0).groupBy("userId").count().select(min("count")).show()

# Avg num implicit ratings from users
print("Average implicit ratings per user: ")
msd.filter(col("num_plays") > 0).groupBy("userId").count().select(avg("count")).show()
```

### Rank Ordering Error Metrics \(ROEM\)

$$
\mathrm{ROEM}=\frac{\sum_{u, i} r_{u, i}^{t} \operatorname{rank}_{u, i}}{\sum_{u, i} r_{u, i}^{t}}
$$

现在就不能再用RMSE了，因为在implicit data下，我们没有true value，只有: the number of time that a certain song is played and confidence level \(how much confident our model is that they like that song\). 这个时候，就判断test set里，我们判断的值和if the prediction make sense, whether they played it more than once. ROEM的意义就是whether songs with higher number of plays have higher predictions.

```python
ranks = [10, 20, 30, 40]
maxIters = [10, 20, 30, 40]
regParams = [.05, .1, .15]
alphas = [20, 40, 60, 80]

create and store ALS models
for r in ranks:
    for mi in maxIters:
        for rp in regParams:
            for a in alphas:
                model_list.append(ALS(userCol= "userId", itemCol= "songId", ratingCol= "num_plays", rank = r, maxIter = mi, regParam = rp, alpha = a, coldStartStrategy="drop", nonnegative = True, implicitPrefs = True))

# Print the model list, and the length of model_list
print (model_list, "Length of model_list: ", len(model_list))

# Validate
len(model_list) == (len(ranks)*len(maxIters)*len(regParams)*len(alphas))

# Split the data into training and test sets
(training, test) = msd.randomSplit([0.8, 0.2])

#Building 5 folds within the training set.
train1, train2, train3, train4, train5 = training.randomSplit([0.2, 0.2, 0.2, 0.2, 0.2], seed = 1)
fold1 = train2.union(train3).union(train4).union(train5)
fold2 = train3.union(train4).union(train5).union(train1)
fold3 = train4.union(train5).union(train1).union(train2)
fold4 = train5.union(train1).union(train2).union(train3)
fold5 = train1.union(train2).union(train3).union(train4)

foldlist = [(fold1, train1), (fold2, train2), (fold3, train3), (fold4, train4), (fold5, train5)]

# Empty list to fill with ROEMs from each model
ROEMS = []

# Loops through all models and all folds
for model in model_list:
    for ft_pair in foldlist:

        # Fits model to fold within training data
        fitted_model = model.fit(ft_pair[0])

        # Generates predictions using fitted_model on respective CV test data
        predictions = fitted_model.transform(ft_pair[1])

        # Generates and prints a ROEM metric CV test data
        r = ROEM(predictions)
        print ("ROEM: ", r)

    # Fits model to all of training data and generates preds for test data
    v_fitted_model = model.fit(training)
    v_predictions = v_fitted_model.transform(test)
    v_ROEM = ROEM(v_predictions)

    # Adds validation ROEM to ROEM list
    ROEMS.append(v_ROEM)
    print ("Validation ROEM: ", v_ROEM)
    

# Import numpy
import numpy

# Find the index of the smallest ROEM
i = numpy.argmin(ROEMS)
print("Index of smallest ROEM:", i)

# Find ith element of ROEMS
print("Smallest ROEM: ", ROEMS[i])

# Extract the best_model
best_model = model_list[38]

# Extract the Rank
print ("Rank: ", best_model.getRank())

# Extract the MaxIter value
print ("MaxIter: ", best_model.getMaxIter())

# Extract the RegParam value
print ("RegParam: ", best_model.getRegParam())

# Extract the Alpha value
print ("Alpha: ", best_model.getAlpha())
```

{% tabs %}
{% tab title="ALS\_expected\_percent\_rank\_cv" %}
```python
def ROEM_cv(ratings_df, userCol = "userId", itemCol = "songId", ratingCol = "num_plays", ranks = [10, 50, 100, 150, 200], maxIters = [10, 25, 50, 100, 200, 400], regParams = [.05, .1, .15], alphas = [10, 40, 80, 100]):
#Originally run on a subset of the Echo Next Taste Profile dataset found here:
#https://labrosa.ee.columbia.edu/millionsong/tasteprofile
from pyspark.sql.functions import rand
from pyspark.ml.recommendation import ALS
  ratings_df = ratings_df.orderBy(rand()) #Shuffling to ensure randomness
#Building train and validation test sets
  train, validate = ratings_df.randomSplit([0.8, 0.2], seed = 0)
#Building 5 folds within the training set.
  test1, test2, test3, test4, test5 = train.randomSplit([0.2, 0.2, 0.2, 0.2, 0.2], seed = 1)
  train1 = test2.union(test3).union(test4).union(test5)
  train2 = test3.union(test4).union(test5).union(test1)
  train3 = test4.union(test5).union(test1).union(test2)
  train4 = test5.union(test1).union(test2).union(test3)
  train5 = test1.union(test2).union(test3).union(test4)
#Creating variables that will be replaced by the best model's hyperparameters for subsequent printing
  best_validation_performance = 9999999999999
  best_rank = 0
  best_maxIter = 0
  best_regParam = 0
  best_alpha = 0
  best_model = 0
  best_predictions = 0
#Looping through each combindation of hyperparameters to ensure all combinations are tested.
for r in ranks:
for mi in maxIters:
for rp in regParams:
for a in alphas:
#Create ALS model
          als = ALS(rank = r, maxIter = mi, regParam = rp, alpha = a, userCol=userCol, itemCol=itemCol, ratingCol=ratingCol,
coldStartStrategy="drop", nonnegative = True, implicitPrefs = True)
#Fit model to each fold in the training set
          model1 = als.fit(train1)
          model2 = als.fit(train2)
          model3 = als.fit(train3)
          model4 = als.fit(train4)
          model5 = als.fit(train5)
#Generating model's predictions for each fold in the test set
          predictions1 = model1.transform(test1)
          predictions2 = model2.transform(test2)
          predictions3 = model3.transform(test3)
          predictions4 = model4.transform(test4)
          predictions5 = model5.transform(test5)
#Expected percentile rank error metric function
def ROEM(predictions, userCol = "userId", itemCol = "songId", ratingCol = "num_plays"):
#Creates table that can be queried
              predictions.createOrReplaceTempView("predictions")
#Sum of total number of plays of all songs
              denominator = predictions.groupBy().sum(ratingCol).collect()[0][0]
#Calculating rankings of songs predictions by user
              spark.sql("SELECT " + userCol + " , " + ratingCol + " , PERCENT_RANK() OVER (PARTITION BY " + userCol + " ORDER BY prediction DESC) AS rank FROM predictions").createOrReplaceTempView("rankings")
#Multiplies the rank of each song by the number of plays and adds the products together
              numerator = spark.sql('SELECT SUM(' + ratingCol + ' * rank) FROM rankings').collect()[0][0]
              performance = numerator/denominator
return performance
#Calculating expected percentile rank error metric for the model on each fold's prediction set
          performance1 = ROEM(predictions1)
          performance2 = ROEM(predictions2)
          performance3 = ROEM(predictions3)
          performance4 = ROEM(predictions4)
          performance5 = ROEM(predictions5)
#Printing the model's performance on each fold
print ("Model Parameters: ")("Rank:"), r, ("  MaxIter:"), mi, ("RegParam:"), rp, ("Alpha: "), a
print("Test Percent Rank Errors: "), performance1, performance2, performance3, performance4, performance5
#Validating the model's performance on the validation set
          validation_model = als.fit(train)
          validation_predictions = validation_model.transform(validate)
          validation_performance = ROEM(validation_predictions)
#Printing model's final expected percentile ranking error metric
print("Validation Percent Rank Error: "), validation_performance
print(" ")
#Filling in final hyperparameters with those of the best-performing model
if validation_performance < best_validation_performance:
            best_validation_performance = validation_performance
            best_rank = r
            best_maxIter = mi
            best_regParam = rp
            best_alpha = a
            best_model = validation_model
            best_predictions = validation_predictions
#Printing best model's expected percentile rank and hyperparameters
print ("**Best Model** ")
print ("  Percent Rank Error: "), best_validation_performance
print ("  Rank: "), best_rank
print ("  MaxIter: "), best_maxIter
print ("  RegParam: "), best_regParam
print ("  Alpha: "), best_alpha
return best_model, best_predictions
```
{% endtab %}
{% endtabs %}

### Binary Implicit Ratings

如果是Binary Ratings，比如只是预测1和0，那可以在weighting上做更多工作。

Item Weighting: Movies with more user views = higher weight

User Weighting: Users that have seen more movies will have lower weights applied to unseen movies



## Links

{% embed url="https://www.mckinsey.com/industries/retail/our-insights/how-retailers-can-keep-up-with-consumers" %}

![](http://yifanhu.net/PUB/cf.pdf)

![](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.306.4684&rep=rep1&type=pdf)



