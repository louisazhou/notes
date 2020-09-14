# Challenge Prophet with LSTM models

This is a project forecasting 110,000 time series with data from the previous 2 years.

The current model, Prophet, finishes such forecast in only 2 hours, assisted by Spark RDD and cluster work nodes in distributed system. 

Intuitively, the first option of using a Deep Learning model to do it is to explore the possible ways that support **Distributed Deep Learning**. The following packages/platforms/tools are available for this task.

> But not all of them are available at ML Studio!

## Distributed Deep Learning

![](../.gitbook/assets/image%20%2872%29.png)

Highlights of each one, in one sentence: 

* BigDL ---&gt; developed by Intel, modeled after Torch, check [this](https://github.com/intel-analytics/BigDL) [this](https://bigdl-project.github.io/master/#whitepaper/) link to learn more
* TensorFlowOnSpark  ----&gt; developed by Yahoo, check [this](https://github.com/yahoo/TensorFlowOnSpark) link
* Deeplearning4j  --&gt; [link](https://deeplearning4j.konduit.ai/distributed-deep-learning/intro), designed for JVM, great for Java users, supports CPU&GPU
* H2O   ----&gt; can refer to [this chapter of online book](https://livebook.manning.com/book/spark-in-action/chapter-14/19) to learn more of it
* Deep Learning Pipelines  ---&gt; developed by Databricks team, check [this](https://docs.databricks.com/applications/machine-learning/train-model/deep-learning-pipelines.html) [this](https://databricks.github.io/spark-deep-learning/docs/_site/quick-start.html) [this](https://github.com/databricks/spark-deep-learning) links to learn more 
* SparkNet
* Submarine ---&gt; This one is very new. Came most recently, _Support only Spark 3.0+_

The following options are either deprecated, too new or not mature enough for production.

* MLSQL ---&gt; check [this](https://github.com/allwefantasy/mlsql) [this](http://docs.mlsql.tech/zh/) link
* TensorFrames --&gt; deprecated, [link](https://github.com/databricks/tensorframes)
* DeepDist --&gt; deprecated, [link](https://github.com/dirkneumann/deepdist)

**However, I didn't get to explore much of them** because there wasn't enough time to put a mature distributed deep learning model to action, from scratch, within 5 weeks. 

My priority was instead on

* Either **predict as many time series as possible, reasonably good** on a standalone machine, with CPU or GPU.
* Or, alternatively, choose the best NN structure to **predict one time series that is good enough**, map the NN as a function to cluster workers using RDD, Spark UDF, or UDF-Pandas \(the first inspired by Elizabeth, the latter two inspired by Rez's team\).

> Thanks Elizabeth, Rez, and Austin.

## Use Cases at Amex

Amex mainly use RNN/LSTM for Anomaly Detection, Default Prediction, Risk Management purposes. To this end,[ IND-ML](https://enterprise-confluence.aexp.com/confluence/pages/viewpage.action?pageId=227614270) team has developed a toolkit called [RNN-Suite2](https://enterprise-confluence.aexp.com/confluence/display/MLDSW/RNN+Suite+V2). They've been working on it for quite some time now and I believe this is a great product. For use cases of classification, anomaly detection, regression, this is a wonderful place to start. 

However, RNN-Suite2 can only give one output, which is not suitable for our use case.

## How Prophet Makes Predictions

The procedure makes use of a [decomposable](https://en.wikipedia.org/wiki/Decomposition_of_time_series) time series model with three main model components: 

### Trend + Seasonality + Holiday\_Effects

#### y\(t\) = g\(t\) + s\(t\) + h\(t\) + e\(t\)

#### g\(t\)

* **trend** models _non-periodic_ changes; linear or logistic

#### s\(t\)

* **seasonality** represents _periodic_ changes; i.e. weekly, monthly, yearly

#### h\(t\)

* ties in effects of **holidays**; on potentially irregular schedules ≥ 1 day\(s\)

{% hint style="info" %}
**Holidays,** as implemented in Prophet, can be thought of as unnatural events when the trend will deviate from the baseline but return once the event is over.

Need to be specific on the dates **'ds'**
{% endhint %}

* Prophet **automatically** detects changes in trends by selecting **change points** from the data.
* A **yearly** seasonal component modeled using Fourier series.
* A **weekly** seasonal component using dummy variables_._
* A **user-provided** list of important holidays.
* Multiple seasonality, 
  * hourly, daily, or weekly observations with at least a few months \(preferably a year\) of history
  * strong multiple “human-scale” seasonalities: day of week and time of year
  * important holidays that **occur at irregular intervals** that are known in advance \(e.g. the Super Bowl\)
* Changing growth rates, 
  * trends that are non-linear growth curves, where a trend hits a natural limit or saturates
  * provides uncertainty intervals for the trend component
* Can deal with a reasonable number of missing observations or large outliers
* Detects historical trend changes, for instance due to product launches or logging changes
* **Overall, the default settings** are enough to produce forecasts that are often **accurate** as those produced by skilled forecasters, with much less effort, very **intuitive to non-experts.**
* The forecast package **includes** many different forecasting techniques \(**ARIMA**, **exponential smoothing**, etc\)

> OK reading all those, it seems unlikely to have anything beats Prophet... but there's hope.

## What Prophet fails to achieve

In one sentence, the correlations between time series.

Since the 110,000 models are trained separately, this is essentially a **univariate autoregression** problem: forecasting into the future using its past values, and only one feature of observation.  

However, the 110,000 time series at hand by themselves may have strong correlations; in other words, the increase / decrease of a value from one time series might indicate the rise / fall of the value from another time series.

The simplest example is retail: at discount seasons, say Labor Day, Black Friday, Cyber Monday, the same pattern is observed by multiple threads of time series data. This makes it possible to group multiple time series together in one training process. This way, NN gets to treat many series altogether, reduces time in repetitive computation and the original input dataset is also augmented. This purpose can only be achieved under one condition: that the series by themselves are indeed similar in a certain way.

Taking a batch of data, US-issued cards, consumer business units, I have ~2700 time series to predict, using Deep Learning Models. With further bucketing, the \# of network to train will go down drastically. 

#### Bucket on a certain category

Certain industries can be put together to form a larger dataset. 

> * Travel related: Airlines, Car Rental, Cruise, Oil Companies, Travel Related, Other Transportation, lodging, auto-related 
> * Health related: Healthcare, Insurance Services 
> * Retail: all retails
> * Living expenses: utilities, communications 
> * Education & Government & Charities
> * Entertainment & Restaurant 
> * Business: Fin Services; Advertising & Media
> * Other

Now the number's down to 8. Build 8 NN for 2700 time series, not that bad. Training the more correlated time series together, there's hope that LSTM result can beat Prophet. 

## Check their correlations to test assumption

It's time to take a step back and check if my original assumption 'time series from the same category should have similar behaviors' is correct. This can be achieved in at least five ways.

* Correlation Matrix 
* Dynamic Time Warping \(DTW\)  [ref link1](https://medium.com/datadriveninvestor/dynamic-time-warping-dtw-d51d1a1e4afc) [link2](https://github.com/wannesm/dtaidistance)
* B Spline Clustering ref link [ref link](https://towardsdatascience.com/using-b-splines-and-k-means-to-cluster-time-series-16468f588ea6)
* Auto-Encoder, check reconstruction error [ref link](https://github.com/tejaslodaya/timeseries-clustering-vae)1 [link2](https://towardsdatascience.com/time-series-clustering-and-dimensionality-reduction-5b3b4e84f6a3)
* Granger Causality Check

> I did two tests for correlation. DTW was suggested by Qiong during our coffee chats \(Thanks Qiong\).

### 1. Granger Causality Check

The _Causality_ here isn't the traditional statistical meaning of causality, saying cause-and-effect. In statistics, a variable X is causal to variable Y if X is the cause of Y _or_ Y is the cause of X. However, with Granger causality, we aren’t testing a true cause-and-effect relationship; What you want to know is if **a particular variable comes before another** in the time series. 

The null hypothesis is that lagged x-values do not explain the variation in y. So if the calculation ends up to be &lt;0.05, we can safely reject the null hypothesis and then the data sets are analyzed to see if they are correlated. 

On a dataset with 400 time series from 'retail' category, 70% are correlated, proving it is appropriate to group them together for training. 

### 2. DTW for clustering

DTW calculates the distance between each pair of time series and decide if they are similar. This can be done regardless of the lengths of each series \(though in our use case all series are of the same length\). It is more often used in the domain of sound-wave extraction, determining if two sound-waves are saying the same thing.

In this project, after using `dtaidistance` to calculate the distance matrix, the distance information is then used to plot dendrograph, showing how they should be clustered, and also histograms, showing the distance distribution. 

From each of the 8 dataset \(bucketed by category\), all the colors fall in the lighter blue range, indicating the distance between each pair isn't that large.

I selected the dis-similar and the most-similar pairs from each of the bucketed data to check them visually & make sure it's the case. See Appendix of the slides for detail.

## Start From a Naive Solution: Vanilla LSTM

From the Power BI dashboard, we can clearly see that Vanilla LSTM, with just the training data \(1.6 years of daily input\) can **at best predict lines with very little variations**. The LSTM overfits on the training data so heavily that it only forecasts on the mean value to reduce loss.

There's a lot of room to improve from here.

## What we can add, to boost LSTM predictions

### Time-Date Encoding

Unlike Prophet that have all the input's dates as a variable, the vanilla LSTM I built doesn't actually have any information on date-time. This hinders its ability to make reliable predictions. I found three possible ways to encode time in it. 

* Cyclical Encoding, representing date-time using sine and cosine relations [ref link](https://towardsdatascience.com/cyclical-features-encoding-its-about-time-ce23581845ca)
* Add day\_of\_week, day\_of\_month, day\_of\_year, is\_weekend
* \*\*\*\*[**Time2Vec:**](https://towardsdatascience.com/time2vec-for-time-series-features-encoding-a03a4f3f937e) provide a representation of time, in form of vector embedding [ref link](https://arxiv.org/abs/1907.05321)

I adopted the first approach, encoded time info with sine and cosine. 

### Holiday Encoding

Prophet accepts user defined 'holidays' as input. These dates are not necessarily real 'holidays', but days that might give high/low value data. Say, COVID is a 'holiday' because almost everything is stagnated during this time and all activities slowed down; 'super bowl' is a holiday because it brings sell/buy in certain merchant channels.

For the challenger model I build, it's fair to give the model a 'heads-up', reminding it that certain abnormal data comes from certain dates, and these are the dates that need to pay more attention to. 

For a very crude estimation, I encoded the 'online holidays' \(from Elizabeth's table\) and 'US holidays' \(from Python's `holidays` package\) in it as a binary encoding. This is a very crude and brute-force estimation. For the current version, the team are encoding the holidays based on different merchant countries and load in holidays of different countries for different segments. But since I only sampled out the batch 'US-issued, consumer', this should work on my sample batch.

### Smarter ways to do it, but I didn't have time to take actions

The two encoding I mentioned above adds 11 more columns to the input series. For each NN, the input increases. However, it's not like 'the more, the better'. Keep in mind the curse of dimensionality.

There are actually smarter ways to encode these information, through any one of the following ways 

> see the related papers and Github Links with code in the last section

1.  'Attention' mechanism: intentionally train NN's attention for certain things, and come back to check its 'memory', see if it actually 'remembers' them as important event-points.   
  
2. 'Multi-Model' or 'Multiple Output' model, aided by Keras Functional API. It's still one giant network, but output two different data types, the first time, labels, whether it thinks some date can be an anomaly; and the second time, our forecasted values. We can also add weights or customize the loss function so that the network is punished harder when making wrong predictions at those dates. This allows a better catch on the holiday effect without any manual input to define which dates are holidays. The NN takes care of it by itself. [ref link](https://becominghuman.ai/neural-networks-for-algorithmic-trading-multimodal-and-multitask-deep-learning-5498e0098caf)

![](https://img-blog.csdnimg.cn/20190520190847987.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FaUlJS,size_16,color_FFFFFF,t_70)



## It's time to be greedy 

It's understandable that we try to use Neural Network to beat statistical models. In both computer vision and natural language processing, RNN and CNN become the buzzword that everyone holds so much hope on. 

Statistical techniques are actually adequate when we face an autoregressive problem when the future is related only to the past. The truth is, at least in time series domain, there really is no point deifying neural network models, no matter how fancy they sound. In fact, prior to N-Beats and ES-RNN, the Winners of M4/3/2/1 \(time series competitions\) were all statistical models. Neural networks never stood out in time series.

> And I learned this lesson the hard way... through researching on all the possible ways the first 3 weeks here...

The secret for NN to stand out in CV and NLP is because their \# size for trainings are at million-level. Ideally, we build a sophisticated network, and provide a very long vector to the model and let it figure out a correlation by itself. That would work but in case of time series that usually we don’t have that much training data \(and we cannot just create more\). Even if we gather data from each day over the last 5 years, we have \(on average\) **1826** records. That’s way too low if we want to create a deep model with a very long input vector. 

So what did N-Beats & ES-RNN do to win the game and alter NN's fate and how can we learn from them? 

They have one thing in common--- trying to combine statistics with NN, leveraging the pros of both approaches.

## Get Inspired by Uber & M4 Winners

One of the M4 Winner and author of ES-RNN wrote this blog post 👇 one year prior to winning the game. He is an Uber engineer, trying to build RNN for extreme event forecasting. 

> Quote from them: From our experience, we define three dimensions for deciding if the neural network model is right for your use case: \(a\) number of time series, \(b\) length of time series, and \(c\) correlation among time series. All three of these dimensions increase the likelihood that the neural network approach will forecast more accurately relative to the classical time series model.

{% embed url="https://eng.uber.com/neural-networks/" %}

Then one year later, they went to compete in M4, wrote this blog post on how they used a hybrid method to leverage statistical and neural network models.

> Quote from them: [The results](https://www.scribd.com/document/382185710/IJF-Published-M4-Paper) confirmed this hypothesis: pure machine learning and neural network \(NN\) methods performed worse than standard algorithms like ARIMA or Exponential Smoothing \(ES\), and still worse against various combinations of these base statistical methods.

{% embed url="https://eng.uber.com/m4-forecasting-competition/" %}

## ✅Hybrid Model: VAR-LSTM = Statistical + Neural Network

NN and statistical methods are complementary to the other: a statistical model's pro becomes the NN's cons and vice versa.

It is held conventionally that statistical methods deal with linear correlations well, and NN deals with non-linear correlations well. This is where Hybrid Model can come into play and act as a conjunction to present the desired result.

### Two-Step Training Procedure

The strategy I'm adopting here applies a two-step training procedure. 

* First step, statistical: Estimate a VAR properly on our training data;
* Second step, neural network: Extract what VAR has learned and use it to improve the training process of an LSTM model performing a two-step training.

The VAR has learned the internal behavior of our multivariate data source adjusting the insane values, correcting the anomalous trends and reconstructing properly the NaNs. All these pieces of information are stored in the fitted values, they are a modified version of the original data which have been manipulated by the model during the training procedure. In other words, we can see these values as a kind of augmented data source of the original train.

Start by feeding our LSTM model, for one step ahead forecasting of all the series at our disposal \(multivariate output\), using the fitted values produced by VAR. Then conclude the training with the raw data, in our case they are the same differential data we used before to fit the VAR. 

I tested both stacked LSTM and Bidirectional LSTM, and the latter one works waaaaay better. I wouldn't get any results if it weren't for Manikanth.

> Fun Fact: when I reached out to him and arranged a one-hour chat, I was initially setting it with the plan to ask them if it's possible to apply RNN Suite2 \(the product they developed\) in our use case. Though this suit case was not for forecasting, but for one-value output, that 1 hour WebEx with him saved me so much of research time. He definitely pointed me to the correct direction.

Useful suggestions from him: 

* Add dropout layers 
* Use bidirectional LSTM
* Use Keras Functional API to build more complicated network structures instead of using the sequential model
* Encoder-Decoder structure \(the only thing that I didn't get to try out\)

### Conclusion \#1: Still not good enough

From the Power BI dashboard, almost all the orange lines \(VAR-LSTM model\) behave better than purple lines \(vanilla-LSTM model\), but they all fail in most of the cases when competing against Prophet. 

The few exceptions where VAR-LSTM are indeed better are: 

* Retail-Department Store
* Retail-Shops
* Retail-Supermarkets
* Travel-Related

### Two explanations for this:

#### 1. Need to tune network or parameters for the other segments.

Retail was the first category I started from, and all my hyper-parameter tunings and heavy work loads were applied to this particular bucket, 410 of Time Series. Although I did write the code using Hyperopt and Hyperas to tune hyper-parameters automatically, the final version of model came August 11th \(2 days before the presentation\) so there wasn't time for me to wait till all the combinations are tried out in the other 7 datasets. 

####  2. This particular category is in nature more correlated an thus more predictable by VAR-LSTM

When I asked Elizabeth, the export in our team who worked hard to put Prophet to its current forecast product, if I were to pick one segment to start, to test if my methodology would work, where I should start from. She suggested retail, within 3 seconds. It apparently has very clear seasonal trend. It came to me as a surprise that Prophet did not catch as good trend in this category as VAR-LSTM does.   
  
If the data is correct, then we can give credits to VAR, because LSTM alone cannot achieve it \(seen from the purples\).

### Conclusion \#2: Not a structure for longer term forecast, but...

In the model framing, I used 120-day window to forecast the next 30-day in each input batches. There are altogether 30 batches that cover the 1.6 years training period. Note that this is not equal to 'using 120 day to predict 30 day', because all the 1.6 years of data were under account to train the LSTM, allowing it to learn seasonal patterns and trend. It's just the state within LSTM neurons are reset more frequently. 

This particular structure is designed to simulate a stateful modeling using stateless setting. Austin suggested to use non-overlapped windows with longer time period, a stateful modeling. From a blogpost I read [here](https://machinelearningmastery.com/stateful-stateless-lstm-time-series-forecasting-python/) and from experimenting on my data following the same approach, stateless model doesn't seem to have that much of problem because of my \(relatively large\) size of batch. 

Due to this particular structure I designed for this model, the longer-term forecast, tasks like 'What will the spending per account level going to be like in the next year' will not be so reliable. But still, don't easily turn LSTM down because there are so many other model structures to use. Multi-step forecast, multiple one-step forecast, sequence generation... there's a whole new world out there awaiting to explore.

The ones that I feel to have the most potential are Seq2Seq \(Encoder-Decoder\) and Transformer. During my six weeks of research, I have collected `.pynb` with step-by-step tutorials on how to use them, included in the last section of this doc. 

This area witnessed vast growth in the last year. It's almost as if the news that ES-RNN successfully paved out a way to stand out in M4 suddenly inspired everyone in Time Series to have a brand new perspective on Time Series Modeling. I've read interesting academic papers and their Github Repo to learn that most of the big things happened in the last year. The Github Repo & Papers that I feel will benefit Amex use cases are also included in the final section. 

### Q: How predictable are the time series at hand?

There are several ways to check if a certain time series is predictable. 

Rez & Austin's team use entropy to filter out high-entropy data, because a high entropy means a high noise. The similar approach was described[ in this link](https://www.machinelearningplus.com/time-series/time-series-analysis-python/) as well. 

Second way is to use Pyros and add the confidence interval in the NN predictions. It's called Bayesian Neural Network. Details will follow in the next session.

Another way is to build a seq2seq LSTM model, treating forecasting of time series the same manner as 'Machine Translation' in NLP. More specifically, there is an encoder that learns the pattern of data, using its own 'judgements' to understand their meanings. It then decodes out the information, trying to reconstruct the original input series. We can use the reconstruction error to determine how good/bad the LSTM is in learning historical data. Then we'd have good reason to believe that an encoder that grasps the meanings most accurately can make the best forecasts, even long term, 1 year of forecast \(the most desirable use case for our team\). 

## Must Read: More Promising Methods & Codes

### No.1 GluonTS  \(good for Holiday+RNN+ PySpark, compare against Prophet\)

> for probabilistic forecasting + RNN + Possible PySpark integration, with Spark RDD

![](../.gitbook/assets/image%20%2874%29.png)

This is by far **the most promising tool I found** that can actually help us to solve the use case 'forecast values using RNN'. I found it this Monday and it was already too late----- I didn't have time to try this out myself. 

The reason for me to rank it the highest are: 

* Already a mature library, we can easily map it to all the time series, building 110,000 RNN models with Spark RDD, just like how we now do it with Prophet
* All we need to do is to sort out the data in the format that GluonTS supports. The workload is very manageable
* We can add **is\_holiday** as a categorical input in the `dataset.metadata`, then it will take this external source of data into account 
* It gives **probability distribution of forecast result.** Trust me. Not all NN models can do that. They'd usually need a bit more help to actually output the confidence interval. A popular way to do that is 'Bayesian Neural Network', which I will touch upon later.
* The format of output \(predictions, confidence interval\) is closest to Prophet's output, so we can easily compare the two models side-by-side, knowing which method works better, not just from the prediction values, but also from the range of confidence interval.

{% embed url="https://gluon-ts.s3-accelerate.dualstack.amazonaws.com/master/examples/extended\_forecasting\_tutorial/extended\_tutorial.html\#5.4.1-Training" %}

#### If we build our own NN, not using GluonTS, we can add Pyro to give explainability 

Pyro can help to combine Bayesian + Neural Network. This way, when the model sees some data that is hard to predict, we would be able to see that from the confidence interval/probability in the output. NN will not be a pure black box to us.

{% embed url="https://pyro.ai/examples/" %}

{% embed url="https://medium.com/@alexrachnog/financial-forecasting-with-probabilistic-programming-and-pyro-db68ab1a1dba" %}

### No.2 Seq2Seq 

> If I can go back to 6 weeks ago and restart this internship, I'll go directly from seq2seq on Day1. Then we can probably have great models that actually beats Prophet in more segments \(or maybe I'm just too optimistic and dream-talking right now\).

#### Great Jupyter Notebook Tutorials on Seq2Seq

{% embed url="https://github.com/mohcinemadkour/TimeSeries\_Seq2Seq" %}

{% embed url="https://github.com/aaxwaz/Multivariate-Time-Series-forecast-using-seq2seq-in-TensorFlow" %}

{% embed url="https://github.com/manohar029/TimeSeries-Seq2Seq-deepLSTMs-Keras/blob/master/Keras\_Enc-Dec\_MinTempMel.ipynb" %}

#### Seq2Seq with Attention

{% embed url="https://github.com/LongxingTan/Time-series-prediction" %}

{% embed url="https://github.com/dyq0811/EEG-Transformer-seq2seq" %}

### No.3 Transformer

#### Code/Jupyter Notebook Tutorial Links 

{% embed url="https://github.com/Vsooong/pattern\_recognize" %}

{% embed url="https://github.com/shashwattrivedi/TimeSeriesTransformerLSTM/blob/master/TransformerTimeSeries.ipynb" %}

{% embed url="https://github.com/gk5894/Time-series-forecasting-using-transformers/blob/master/Time%20Series%20Forecasting%20using%20transformers.ipynb" %}

{% embed url="https://github.com/yrbahn/timeseries\_transfomer/blob/master/TimeSeries\_Transformer.ipynb" %}

### No.4 Other Networks

#### Tutorial links below

* [3. DNN](https://github.com/deKeijzer/Multivariate-time-series-models-in-Keras/blob/master/notebooks/3.%20DNN.ipynb)
* [4.1 CNN & RNN Data Preperation](https://github.com/deKeijzer/Multivariate-time-series-models-in-Keras/blob/master/notebooks/4.1%20CNN%20%26%20RNN%20Data%20Preperation.ipynb)
* [4.2 LSTM](https://github.com/deKeijzer/Multivariate-time-series-models-in-Keras/blob/master/notebooks/4.2%20LSTM.ipynb)
* [4.3 GRU](https://github.com/deKeijzer/Multivariate-time-series-models-in-Keras/blob/master/notebooks/4.3%20GRU.ipynb)
* [5.1 Multivariate time-series to images](https://github.com/deKeijzer/Multivariate-time-series-models-in-Keras/blob/master/notebooks/5.1%20Multivariate%20time-series%20to%20images.ipynb)
* [5.2 CNN](https://github.com/deKeijzer/Multivariate-time-series-models-in-Keras/blob/master/notebooks/5.2%20CNN.ipynb)
* [6. TimeDistributed\(CNN\)+RNN+DNN](https://github.com/deKeijzer/Multivariate-time-series-models-in-Keras/blob/master/notebooks/6.%20TimeDistributed%28CNN%29%2BRNN%2BDNN.ipynb)

### No.5 Adapted LSTM Models From the Most Recent Research Papers

#### ES-RNN / N-Beats / LSTNet / DSANet / TPA-LSTM / DeepGlo / MLCNN

> I categorize them as a whole set, because they are all research-based methods... I found them from reading academic papers. No need to be frightened by this because I also found their source code on Github. So if we decide to be adventurous, may as well just refer to their source code and adapt them.

{% embed url="https://github.com/damitkwr/ESRNN-GPU" %}

{% embed url="https://github.com/autonlab/esrnn" %}

{% embed url="https://github.com/philipperemy/n-beats" %}

{% embed url="https://github.com/fbadine/LSTNet" %}

{% embed url="https://github.com/bighuang624/DSANet" %}

{% embed url="https://github.com/gantheory/TPA-LSTM" %}

{% embed url="https://github.com/yuqirose/tensor\_train\_RNN" %}

{% embed url="https://github.com/smallGum/MLCNN-Multivariate-Time-Series" %}

{% embed url="https://github.com/rajatsen91/deepglo" %}

### No.6 Transfer Learning 

> Maybe, just an idea, we can pre-train separate networks by category, think of them as source domain and then transfer the trained weights to serve as initialization for the model to be trained on the target time series \(the more granular specified segment-level\)

{% embed url="https://github.com/NshanPotikyan/TransferLearningTimeSeries" %}

## \(Written For Tech-People\) About building this Model: 

> If you are reading this section, you are either technically-savvy enough to learn these or is here to just have a look at the detours I took while learning & trying to build a RNN model the first time in my life. Either way, I feel like it would be better if I can have them documented, just in case another newbie like me come & join the team at some point in the future and they probably also don't know where to start.

### Dense Output from Keras

The biggest headache that troubled me for two weeks was to figure out appropriate input/output structure for Keras. The second biggest was to set the input batches correctly, as \[samples, time steps, features\].

The real obstacle is, in almost all the examples/tutorials I read, Keras Dense Layer output are all 1D. It's either one value \(true-false / classification\) or a list of values \(multi-step output of one value, or one step output of multiple values\). 

How to make the output compatible to what we want? Multi-step, Multi-Dimensional Output?

1. Use TimeDistributed function, or a higher version of Keras. In older versions, we'll need to wrap a TimeDistributed function outside of Dense, say, `TimeDistributed(Dense(400))` for \[30, 400\] output. In the new version, this seems to have been fixed? Refer to the update info from [this link](https://machinelearningmastery.com/timedistributed-layer-for-long-short-term-memory-networks-in-python/)
2. Customize the output, first convert it to 1D and then convert it back, with the aid of two functions
3. Use Time Series Generator to generate batches of input/output, the generator will take care of the rest by itself, no need to convert back & forth

```python
def create_dataset(data,n_predictions,n_next):
    '''
    To customize input train_Y
    '''
    dim = data.shape[1]
    train_X, train_Y = [], []
    for i in range(data.shape[0]-n_predictions-n_next-1):
        a = data[i:(i+n_predictions),:]
        train_X.append(a)
        tempb = data[(i+n_predictions):(i+n_predictions+n_next),:]
        b = []
        for j in range(len(tempb)):
            for k in range(dim):
                b.append(tempb[j,k])
        train_Y.append(b)
    train_X = np.array(train_X,dtype='float64')
    train_Y = np.array(train_Y,dtype='float64')

    return train_X, train_Y
    
def reshape_y_hat(y_hat,dim):
    '''
    When finish, convert y back with this function
    '''
    re_y = []
    i = 0
    while i < len(y_hat):
        tmp = []
        for j in range(dim):
            tmp.append(y_hat[i+j])
        i = i + dim
        re_y.append(tmp)
    re_y = np.array(re_y,dtype='float64')
    return  re_y
```

### What worked for our use case, ranked by importance 

* Bidirectional LSTM, dense layer\(s\) with fewer neurons, definitely add DropOut! \[0.2,0.5\] advisable
* Two-Step Training, first on VAR, and then on LSTM 
* Standard Scaling \(for some reason MinMax-Scaling doesn't help at all! A bug so hard to find...\)
* Reducelronplateau and EarlyStopping
* MSE or MAE as loss function, equally good for my case  
* Adam or RMSProp optimizer both work okay but both needs a lot of tuning, starting from 0.002.
* Simulate stateful LSTM with large batch size, stateless LSTM, no shuffling \(though Austin & Rez thought of it differently. Check their confluence page **Commercial Data Engineering** to see more\)

### What didn't work

* Stacked LSTM \(more than 1 LSTM\)    ---&gt; gradient explode/vanish
* LSTM with more than 256 neurons   ----&gt; heavily overfitted, predict on mean values
* Min-Max Scaling                                   ---&gt; heavily overfitted, predict on mean values
* Add decomposed residual term for the first round of training \(like VAR\) 
* Add LOESS smoothing term \(LOcalized regrESSion\) for the first round of training \(like VAR\) 

Comment on the last two bullets: 

> Andrew, another intern working on GBM model, inspired me to have the idea of 'training on the extracted residual term' during one of our coffee chats \(Thanks, Andrew\).

Our intention was actually good: try to lower the burden of NN, lets it train on the residual and then we can add the residual term on top of seasonal and trend term to get the final output. I had high hope on these two, but unfortunately NN doesn't take the bite and doesn't like the favor I'm doing for it. 

It can only train for 2-3 epochs. From the validation loss, NN didn't actually learn anything from here, the validation loss kept increasing and triggered the early\_stopping I set. So they are all considered as noise by LSTM.

### It would work better if... 

* \(from Austin's use case\) Non-overlapped sliding window & small batch size
* Add a CNN layer first, converting each series to a graph and then apply LSTM
* Seq2Seq, encoder-decoder structure, with 'Attention' mechanism
* Filter out the less predictable billings beforehand, judging from Entropy

## Options & Packages Available at ML Studio

> To check compatibility at ML Studio is pure labor. Since I've already explored everything, it's better to list them out here just in case someone's interested in trying one of these out at ML Studio...just install them in VE

### Category \#1 ML-related packages for Time Series study

* \*\*\*\*[**Sktime**](https://github.com/alan-turing-institute/sktime)     ****[**Seglearn**](https://github.com/dmbee/seglearn)   ****[**fireTS**](https://github.com/jxx123/fireTS) ****

These are three libraries, all Scikit-learn compatible for time series study. I'll need to give a shout to Sktime here because it is most similar to sklearn---- we can easily do **ensembling** **pipelining** and even **cross validation** on Time Series data! Without worrying about framing the data in any way to avoid information leakage. For forecasting task, check out [this link](https://github.com/alan-turing-institute/sktime/blob/master/examples/01_forecasting.ipynb) to know how to use them, it covers pretty much everything. 

* \*\*\*\*[**Stumpy**](https://github.com/TDAmeritrade/stumpy)\*\*\*\*

Calculates matrix profile efficiently, which can be used for anomaly detection and pattern discovery.

* \*\*\*\*[**Cesium**](https://github.com/cesium-ml/cesium) 

Good for feature extraction for non-uniformly sampled signals

* \*\*\*\*[**TSfresh**](https://github.com/blue-yonder/tsfresh) ****

Feature extraction, allows supervised classifiers and regressors

* \*\*\*\*[**Pyts**](https://github.com/johannfaouzi/pyts)   ****

Time series preprocessing, transformation and classification

* \*\*\*\*[**pyFTS**](https://github.com/PYFTS/pyFTS) ****

Fuzzy set rule-based models for time series forecasting, including multi-step, point, interval and probabilistic forecasting

### Category \#2 DL-related packages

* [MLflow](https://mlflow.org/docs/latest/tracking.html)   link to the demo by MLNinjas is [here](https://amex.webex.com/recordingservice/sites/amex/recording/play/3ff1eea898634e8dbd1f72482921c6fa) 

I'm glad that I was one of the first users at Amex to try mlflow on ML Studio, the person to arrange the first demo on mlflow & invited two teams to watch the demo. If it comes to the point that we need to track down the previous training metrics, the training results on different clusters, this will be our first tool to use.

* \*\*\*\*[**Dts**](https://github.com/albertogaspar/dts)    

 A Keras library that provides multiple deep architectures aimed at multi-step time-series forecasting.

* \*\*\*\*[**GluonTS**](https://gluon-ts.s3-accelerate.dualstack.amazonaws.com/master/examples/extended_forecasting_tutorial/extended_tutorial.html)\*\*\*\*

I mentioned earlier in this article

* \*\*\*\*[**Flint**](https://github.com/twosigma/flint)   

Time series library for Apache Spark, can generate `TimeSeriesRDD` from `DataFrame.`It will make Time Series + Spark a lot easier!!!

* \*\*\*\*[**hyperopt**](https://github.com/hyperopt/hyperopt)\*\*\*\*

Hyper parameter tuning. There is another package called hyperas but please don't use it, the author hasn’t updated it for more than 1 year and it's very unstable, hard to debug. hyperopt is the safer option.

* \*\*\*\*[**pyro**](https://github.com/pyro-ppl/pyro) ****

Make the NN say 'I don't know'--- with Pyro + Pytorch, we can get Bayesian NN, with proabibility 

* ~~**tensor2tensor**       cannot install on MLStudio~~

### Great places to start exploring LSTM/RNN/GRU \(written for newbies\)

1. Machine Learning Mastery

> I started my first day with knowledge of only 'Moving Average' in the Time Series domain but thanks to his tutorials, I learned most of the topics & put them to actions quickly in 6 weeks.
>
> This guy writes his tech blog here, answers to almost every question that readers ask, and is VERY good at explaining things, using code & texts at the same time! Don't need to search elsewhere for meanings of lingo or code reference... Starting from here makes the learning process so efficient!

{% embed url="https://machinelearningmastery.com/" %}

2. Confluence page and BitBucket page

> 'There must be someone else who are either working on or have worked on similar cases'. This mindset helped me to save so much time. The second I started to look for information from Amex Confluence page & Bitbucket Repo, I finally had the feeling that I'm on the right path \(That was already Week4\).
>
> I was reading in awe the [IND-ML page](https://enterprise-confluence.aexp.com/confluence/pages/viewpage.action?pageId=227614270), surprised by how many NN models they've tested thus far \(the number is still adding up\); learning and confirming some of my ideas from [Commercial Data Engineering team page](https://enterprise-confluence.aexp.com/confluence/display/ESCDE/GCG+Charge+Volume+Forecasting); we have use cases so similar that almost everything they did / are trying to do now is also in my bucket list for this project.

## Thank you for giving me support & inspirations

I learned quite a lot from the following people & teams. Thank you all for bringing me inspiration on building models. All of your previous experiences have helped me tremendously and saved me much time! I feel so warm to work with this group of wonderful people!

**Leader & Teammates at Finance Decision Science:**

* Kim Jun
* Elizabeth Lamm 
* Karthik Krishnamoorthy
* Qiong Wu \(technically not our team but close enough!\)

**GCS Team**

* Alireza Aliamiri
* Austin Polanco

**IND-ML Team**

* Gonchi ManiKanth Reddy

**Decision Science & Data Strategy**

* Wenke Li

**Global Merchant & Network Services**

* Will Chen

## More From Me

### Catalog of Files related to this Project

The final version of this project is displayed in the form of: 

* Jupyter Notebook code of the whole VAR-LSTM model
* Power BI dashboard, with three tabs, comparing values, %errors, sMAPE
* Graph folder, saving histogram and clustering results from each industry category
* Tutorial folder, saving seq2seq tutorials I found useful \(but no time to implement on this project\)

I pretty much brain-dumped everything to this document and my Jupyter Notebook code. I added markdown titles and subtitles in the code so It would be easier to explore---just click the outline view in the left panel in ML Studio \(otherwise you'll probably be lost in it...\).

### Go to my leader for feedbacks/details on this project

Since today \(Aug.14, 2020\) is the last day of my internship, I will not be able to answer any questions regarding details of this project anymore. Please connect my leader, Kim Jun, if you have good ideas on how to move forward. Our team will be interested in exploring other options. 

> Though I think I pretty much sold him on using 'GluonTS' in the next step if we were ever going to try RNN again.

### Thank you for reading till the end.

