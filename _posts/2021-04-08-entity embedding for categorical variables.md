---
title: "Entity embedding for categorical variables"
excerpt: ""
categories:
  - Feature Engineering
  - Embedding
---

# Entity embedding for categorical variables

Handling categorical features is a complicated task. Some common aproach are: 
 -  Binarized variables
 -  LabelEncoder 
 -  One-hot encoding

We cannot end this chapter without using a neural network on this data. So, let’s take a look at a technique known as **entity embedding**. In entity embeddings, the categories are represented as vectors.  If we have tens of thousands of categories. One_hot embedding will create huge matrices and will take a long time for us to train complicated models. Embeddings are a solution to dealing with categorical variables while avoiding a lot of the pitfalls of one hot encoding.

The idea is super simple. Formally, an embedding is a mapping of a categorical variable into an n-dimensional vector. This provides us with 2 advantages. 

 1. First, we limit the number of columns we need per category. 
 2. Second, embeddings by nature intrinsically group similar variables together.


![](https://miro.medium.com/max/1400/1*mNdWU4z2Gw9mt1NCz5oelQ.png)

Word embeddings, the most well-known embeddings

The beauty of neural networks is that, as part of learning a mapping from inputs to outputs, they also learn representations of the data. Those representations are hidden layers in the network. Well-known examples include  [convolutional layers](https://arxiv.org/abs/1311.2901)  of ConvNets or  [word embeddings](https://blog.acolyer.org/2016/04/21/the-amazing-power-of-word-vectors/)  learnt by the word2vec algorithm. Similarly, we can learn representations of categorical variables that are relevant for a specific task by embedding their one-hot encodings in a lower-dimensional vector space. 


![](https://miro.medium.com/max/1400/1*39mlQ8Qsw2LM83_J0WbXYw.png)
Vector representation for words — or categories.

## Motivation

 The best introduction to the ideas behind this concept is Guo and Berkhahn (2016) in [post](https://towardsdatascience.com/deep-learning-structured-data-8d6a278f3088).

Entity embeddings are conceptually simple, but powerful and relevant for many data science applications.

1.  They provide a continuous representation of categorical variables, allowing to train more powerful neural networks.
2.  They provide a way to  [add metadata](https://arxiv.org/abs/1508.00021)  to deep learning on unstructured data, including images, video, natural language, or time series data.
3.  They can improve the performance of other machine learning algorithms, including XGBoost and Random Forest.
4.  Using  [transfer learning](https://www.datacamp.com/community/tutorials/transfer-learning), one can train embeddings on a large dataset and subsequently use them for problems with much smaller training data. This can be very helpful in industry.
5.  Because we learn vectors for categorical variables in the Euclidean space, it becomes straightforward to  [cluster](https://medium.com/airbnb-engineering/listing-embeddings-for-similar-listing-recommendations-and-real-time-personalization-in-search-601172f7603e)  observations on those variables.
6.  Information retrieval and  [recommender system problems](https://medium.com/airbnb-engineering/listing-embeddings-for-similar-listing-recommendations-and-real-time-personalization-in-search-601172f7603e)  commonly use embeddings.

Entity embeddings have been used to win Kaggle competitions with very little feature engineering and can be useful for data scientists in almost any industry, since categorical variables are extremely prevalent. 

## Training embeddings

For demonstration purposes, further details on what the library is trying to do in the snippet above, see the  [Notebook ](https://www.kaggle.com/abhishek/same-old-entity-embeddings) here. 
