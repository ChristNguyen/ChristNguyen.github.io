# Scikit-Learn Pipelines with Custom Transformer 

> Recently, i want to use much of reusable and  reproductive source code. And i think Scikit-learn has almost it for us. this example will give us a way to use pipeline in preprocessing data:

Data and Model Algorithm are the two core modules around which complete Machine Learning is contingent on. Within Data module, data extraction and data per-processing (or better known as feature engineering) play a crucial role in the complete model building life cycle.

In real life, any Machine Learning problem tends to have hundreds of input features around which solution needs to be conceptualized. To understand the data characteristics and generate fitment analysis report to respective use case, data need to be cleaned and processed via different techniques which are not limited to only Encoding Categorical values, Scaling Continuous features, Imputing , Normalization etc. This data transformation process becomes very tedious when dealing with large number of input features.

That’s why it is always a good practice to streamline this complete transformation process which can be applied on train and test data both seamlessly. That’s where Scikit-Learn Pipeline comes into picture to enablement this streamline transformation with a sequential list of Transformers and a final Estimator (Classifier). To avoid more theory into his post, if you want to read more about  **Transformers** and  **Estimators**, Sklearn tutorial site has good explanation on these terms.

In this post, I will try to cover following aspects.

-   How to write  **Standard Transformers**  in sklearn pipeline
-   How to write  **Custom Transformers**  and add them into sklearn pipeline
-   Finally, How to use Sklearn Pipeline for model building and prediction

> Note: I am using ‘Titanic-Survivor’ problem data set which is a Classification problem to explain Sklearn Pipeline integration.

To start with Sklearn Pipline Transformers, first I have imported the data into my Jupyter notebook. ‘**PassengerId’** column is dropped as it wont be used in model training.

![](https://miro.medium.com/max/1050/1*ZIVdtuGdVzKu5RyRVqoZeg.png)

In this data set, there are about 9 input features and 1 output label i.e. ‘Survived’.

-   Pclass, Sex, SibSp, Parch and Embarked are Categorical features. We will apply Standard transformers to handle empty values and to encode them into Continuous values.
-   Age and Fare are Continuous features. We will apply Standard transformers to handle empty values and to perform feature scaling
-   Name and Cabin are Free-Text features and can not be directly used in model training so we will write custom transformation to transform them into some useful data

Split the data into train and test data and group Column names according to theirs data type.

![](https://miro.medium.com/max/1050/1*_4rC5AcHuwKTxogM9R_OXA.png)

> **Transformers (known as Data pre-processor)**

To start with data transformation,

-   **Standard Transformers**: Lets first write different standard transformer for each data type (categorical and continuous). Here I created numeric_transformer and categorical_transformer for processing continuous and categorical features values. In numeric_transformer, there are two steps; first is to replace empty (NaN) values with median of respective column. Second step is to apply scaling on continuous features. Similarly there are two steps in categorical_transformer for imputing and applying One Hot Encoding on categorical attributes. Here, all these Transformers such as SimpleImputer, StandardScalar, OneHotEnocder will have in-built implementation of ‘fit’ and ‘transform’ method which will be invoked on pipeline execution.


![](https://miro.medium.com/max/1050/1*mArjFAyfdExk8EGiJVOsXg.png)

**Standard Transformers**

-   **Custom Transformers:** Now to process Cabin and Name input features, we are going to write Custom Transformers as these two features can’t be directly transformed using Standard transformer (you will understand it better in example put down below). Every Custom Transformer has to define at least two methods ‘**fit**’ & ‘**transform**’.

> **On execution of Pipeline’s fit method, Transformer’s ‘fit’ and ‘transform’ method will be called sequentially.**
> 
> **Where, on execution of Pipeline’s predict method, only Transformer’s transform method will be called.**

> **Detailed explanation with example**:


![](https://miro.medium.com/max/1050/1*KvlcwaIRm8OTp79oKUACpg.png)

**Custom transformer for ‘Cabin’ feature**

Let me explain fit and transform methods usage in detail by taking example of ‘Cabin’ input feature. For code snippet, refer above screenshot.

**In the ‘fit’ method**

-   For ‘Cabin’ feature, replacing all empty (na) values with ‘U’
-   Replacing cabin values with first char of theirs respective values
-   In next lines, we are determining unique values of ‘Cabin’ feature (via get_dummies method) and saving it in ‘self.cabin_columns’. It will be used in ‘transform’ method.

**In the ‘transform’ method**

-   For ‘Cabin’ feature, replacing all empty (na) values with ‘U’
-   Replacing cabin values with first char of theirs respective values
-   In next lines, we are using get_dummies method to convert Cabin’s categorical values into numeric by creating new columns for each unique value.
-   We are re-indexing these new columns based on already saved ‘self.cabin_columns’ values. The purpose of doing this is to avoid addition of new column based on new Cabin value in test data. Otherwise, model prediction will get fail on test data due to feature count mismatch. That’s why, we are not applying Label Encoder or One Hot Encoder on this feature and going with Custom Transformer. For more details, please refer this  [link]
Similarly, I have created one more Custom Transformer for ‘Name’ input feature.

> **ColumnTransformer to combine all transformers definition**

Next step is to combine all transformers definition using ColumnTransformer as shown in below screenshot. When we execute this block, ‘**_init_’** method will be invoked of each Custom transformer.



![](https://miro.medium.com/max/1050/1*zRy2suHlUKHE-V4z1c6-og.png)

**Combining Transformers definition**

> **Assembling — Combine Transformers and Estimators**

This is the final step where we combine Transformers and Estimators (in this example, it is RandomForestClassifier) and create final pipeline that will be used to train a model and also in prediction.


![](https://miro.medium.com/max/1050/1*gt-0TMRUmjXj6SAzXD5iog.png)

**Assembling of final pipeline**

> **Pipeline fit and transform method**

**Pipeline ‘fit’ method**

-   It is used for training a model on train data
-   It accepts two parameters; train input features and train output label i.e. x_train and y_train
-   **When this ‘fit’ method is called, then fit and transform both method of Transformers will be called in sequence and input features values will be transformed and pass on to Estimator for model training**

**Pipeline ‘predict’ method**

-   Pipeline ‘predict’ method is used for doing a prediction on test data as shown in below code snippet.
-   When we call ‘predict’ method, then only Transformer’s ‘transform’ method is getting called

![](https://miro.medium.com/max/60/1*XmaIcZVnrHG2-I_aZGjJDg.png?q=20)

![](https://miro.medium.com/max/1050/1*XmaIcZVnrHG2-I_aZGjJDg.png)

**Prediction using Pipeline**

**Note**: We can save this Pipeline object for future reference purpose as shown in below screenshot.

![](https://miro.medium.com/max/60/1*JGfDX88AgLoQcm9IhBmTnw.png?q=20)

![](https://miro.medium.com/max/1050/1*JGfDX88AgLoQcm9IhBmTnw.png)

**Save Pipeline and reuse it**

> **Flow Diagram of end to end pipeline for my example**

![](https://miro.medium.com/max/60/1*va14ybPOa1z3wF5ViEyLqQ.jpeg?q=20)

![](https://miro.medium.com/max/1050/1*va14ybPOa1z3wF5ViEyLqQ.jpeg)

**Flow Diagram of Sklearn Pipeline**
