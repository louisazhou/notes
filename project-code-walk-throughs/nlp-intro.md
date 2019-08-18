# NLP intro

## TF-IDF

Term Frequency: count of word A in doc B/total words count in doc B. 词频，出现了几次就是几

Inverse Document Frequency: log\(total number of documents in the corpus/ \(number of documents where word A appears +1\)\) 词出现在了几个文档里；出现次数越多，词在文档中越不重要

TF-IDF\(wordA in docB\)=TF\(wordA\_in\_docB\)\*IDF\(wordA\)

```python
# define vectorizer parameters
# TfidfVectorizer will help us to create tf-idf matrix
# max_df : maximum document frequency for the given word
# min_df : minimum document frequency for the given word
# max_features: maximum number of words
# use_idf: if not true, we only calculate tf
# stop_words : built-in stop words
# tokenizer: how to tokenize the document
# ngram_range: (min_value, max_value), eg. (1, 3) means the result will include 1-gram, 2-gram, 3-gram
tfidf_model = TfidfVectorizer(max_df=0.8, max_features=2000,
                                 min_df=0, stop_words='english',
                                 use_idf=True, tokenizer=tokenization_and_stemming, ngram_range=(1,3))

tfidf_matrix = tfidf_model.fit_transform(synopses) #fit the vectorizer to synopses

print ("In total, there are " + str(tfidf_matrix.shape[0]) + \
      " synoposes and " + str(tfidf_matrix.shape[1]) + " terms.")
```

最后也可以cosine similarities看相似度 文字和文字之间的相似度

```python
from sklearn.metrics.pairwise import cosine_similarity
cos_matrix = cosine_similarity(tfidf_matrix)
print (cos_matrix)
```

## Ngram

1 gram: 1个词为单位分

2 gram, bigram: 2个词为单位，有词组的概念了，所以词的前后顺序就重要了

3 gram：3个词为单位

## Late Dirichlet Allocation \(LDA\)

LDA是一个topic model 文档内暗含了主题



