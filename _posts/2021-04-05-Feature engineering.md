---
title: "Feature engineering for machine learning"
excerpt: ""
categories: 
  - Machine learning
  - Feature engineering
  - Hyperparameter 
  - Feature selection
---


## Feature engineering

> Feature engineering is not just about creating new features from data but also includes different types of normalization and transformations.
For category data, I already approach some way inn previous post.

Now, we will review some other kind of data.
*Let’s say that you are dealing with date and time data.*
Using this column, we can create features like:

 - Year

 - Week of year

 - Month

 - Day of week

 - Weekend

 - Hour 

 - And many more.

    ```python
  # create some features based on datetime  
    features = {  
    "dayofweek": s.dt.dayofweek.values, 
    "dayofyear": s.dt.dayofyear.values, 
    "hour": s.dt.hour.values, 
    "is_leap_year": s.dt.is_leap_year.values, 
    "quarter": s.dt.quarter.values, 
    "weekofyear": s.dt.weekofyear.values
    }```
more over, we could create much more aggregated features like:
```python
 -  def generate_features(df):
    
	    # create features using the date column
	    
	    df.loc[:, 'year'] = df['date'].dt.year  
	    df.loc[:, 'weekofyear'] = df['date'].dt.weekofyear  
	    df.loc[:, 'month'] = df['date'].dt.month  
	    df.loc[:, 'dayofweek'] = df['date'].dt.dayofweek  
	    df.loc[:, 'weekend'] = (df['date'].dt.weekday >=5).astype(int)
	    
	    # create an aggregate dictionary
	    
	    aggs = {}
	    
	    # for aggregation by month, we calculate the  
	    # number of unique month values and also the mean aggs['month'] = ['nunique', 'mean'] aggs['weekofyear'] = ['nunique', 'mean']  
	    # we aggregate by num1 and calculate sum, max, min  
	    # and mean values of this column  
	    aggs['num1'] = ['sum','max','min','mean']  
	    # for customer_id, we calculate the total count aggs['customer_id'] = ['size']  
	    # again for customer_id, we calculate the total unique aggs['customer_id'] = ['nunique']
	    
	    # we group by customer_id and calculate the aggregates
	    
	    agg_df = df.groupby('customer_id').agg(aggs) agg_df = agg_df.reset_index()  
	    return agg_df ```
	 
```
In addition, we can create a bunch of statistical features such as: 


-   Mean
    
-   Max
    
-   Min
    
-   Unique
    
-   Skew
    
-   Kurtosis
    
-   Kstat
    
-   Percentile
    
-   Quantile
- Bin

for more detail, could check : [here](https://towardsdatascience.com/feature-engineering-on-time-series-data-transforming-signal-data-of-a-smartphone-accelerometer-for-72cbe34b8a60)
## **Feature selection**
1. The simplest form of selecting features would be to remove features with very low variance:
We could use *VarianceThreshold* from sklearn
2. We can also remove features which have a high correlation. For calculating the correlation between different numerical features, you can use the *Pearson correlation.*
3. We can move to some *univariate ways* of feature selection. *Univariate feature selection* is nothing but a scoring of each feature against a given target. Mutual information, ANOVA F-test and chi2 (Can use chi2 only for data which is non-negative in nature, is is a particularly useful feature selection technique in natural language processing when we have a bag of words or tf-idf based features) . There are two ways of using these in scikit- learn.

 - *SelectKBest*: It keeps the top-k scoring features
    
-    *SelectPercentile*: It keeps the top features which are in a percentage specified by the user

   ```python
    from sklearn.feature_selection import chi2  
    from sklearn.feature_selection import f_classif  
    from sklearn.feature_selection import f_regression
    
    from sklearn.feature_selection import mutual_info_classif from sklearn.feature_selection import mutual_info_regression from sklearn.feature_selection import SelectKBest  
    from sklearn.feature_selection import SelectPercentile
    
    class UnivariateFeatureSelction:  
	    def __init__(self, n_features, problem_type, scoring):
    
		    """  
		    Custom univariate feature selection wrapper on  
		    different univariate feature selection models from scikit-learn.  
		    :param n_features: SelectPercentile if float else SelectKBest :param problem_type: classification or regression  
		    :param scoring: scoring function, string  
		    """  
		    # for a given problem type, there are only  
		    # a few valid scoring methods  
		    # you can extend this with your own custom  
		    # methods if you wish  
		    if problem_type == "classification":
		    
			    valid_scoring = {  
			    "f_classif": f_classif,  
			    "chi2": chi2,  
			    "mutual_info_classif": mutual_info_classif
			    
		    } else:
		    
			    valid_scoring = {  
			    "f_regression": f_regression, "mutual_info_regression": mutual_info_regression
			    
		    }
		    
		    # raise exception if we do not have a valid scoring method
		    
		    if scoring not in valid_scoring:  
			    raise Exception("Invalid scoring function")
			    
			    # if n_features is int, we use selectkbest  
			    # if n_features is float, we use selectpercentile  
			    # please note that it is int in both cases in sklearn if isinstance(n_features, int):
			    
			    self.selection = SelectKBest( valid_scoring[scoring], k=n_features
			    
			    )  
			    elif isinstance(n_features, float):
		    
			    self.selection = SelectPercentile( valid_scoring[scoring], percentile=int(n_features * 100)
			    
		    ) else:
		    
			    raise Exception("Invalid type of feature")
		    
	    # same fit function
	    
	    def fit(self, X, y):  
		    return self.selection.fit(X, y)
	    
		    # same transform function
	    
	    def transform(self, X):  
		    return self.selection.transform(X)
		    
		    # same fit_transform function
		    
	    def fit_transform(self, X, y):  
		    return self.selection.fit_transform(X, y)```
```
  

  Using this class:

```python
ufs = UnivariateFeatureSelction(

    n_features=0.1, 
    problem_type="regression", 
    scoring="f_regression"
    )  
ufs.fit(X, y)  
X_transformed = ufs.transform(X)```
```
4. Greedy feature selection

> Univariate feature selection may not always perform well. Most of the time, people prefer doing feature selection using a machine learning model. Let’s see how that is done.

   
   5. Another greedy approach is known as recursive feature elimination (RFE) ( *from sklearn.feature_selection import RFE*)`
   In Greedy feature selection method, we started with one feature and kept adding new features, but in RFE, we start with all features and keep removing one feature in every iteration that provides the least value to a given model
   6. Feature coefficients or the importance of features.
  ## Hyperparameter optimization

1. GridSearchCV
2. RandomizedSearchCV.

> Note that we could use pipeline with them, and with Crossvalidation in them.
 3. Advanced hyperparameter optimization techniques, we can take a look at minimization of functions using different kinds of minimization algorithms.
 - such as downhill simplex algorithm, 
 - Nelder-Mead optimization, 
 - using a Bayesian technique with Gaussian process for finding optimal parameters or by using a genetic algorithm.
> 
> Application of downhill simplex and Nelder-Mead in ensembling and stacking
> 
hyperparameter optimization is good thing to learn more.
More detail could check [here] (https://www.jeremyjordan.me/hyperparameter-tuning/)
![Hyper parameter](https://www.googleapis.com/download/storage/v1/b/kaggle-forum-message-attachments/o/inbox/3966084/be79d3aa14c5534213930fd4395c2078/Hyperparameter%20tuning%20guide.png?generation=1609159689753686&alt=media)
