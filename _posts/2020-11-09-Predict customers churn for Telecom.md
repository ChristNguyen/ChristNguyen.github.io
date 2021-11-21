# Predict customers churn for Telecom
---
title: "Predict customers churn for Telecom"
excerpt: " "
categories:
  - Churn Prediction
  -  RNN
  - Random forest
---


we will try going through the entire analysis project from start to finish before attempting to apply more complex methods in the following sections.

## Read Data

![](https://miro.medium.com/max/60/1*cmAwnfd32-1cHLhd-lE59g.png?q=20)

![](https://miro.medium.com/max/1400/1*cmAwnfd32-1cHLhd-lE59g.png)

Read telco data from pickle file

We have about 900 thousand records, with 152 “independent” variables and 1  `churn`  variable to be predicted. The main variables are continuous variables representing the amount and number of times of using services with the latest 6-month history. Data only include ages 30–40 years old.

## EDA

![](https://miro.medium.com/max/60/1*6pdYpUvS_ind0jPtq_8fig.png?q=20)

![](https://miro.medium.com/max/1400/1*6pdYpUvS_ind0jPtq_8fig.png)

Checking and excluding missing data

The data is not missing, but there is quite a lot outlier. The data is also not balanced in the two classes — which is quite easy to understand. In the EDA step I usually do the 3 things above without doing other techniques such as distribution plot … because it can cause me to make wrong assumptions about the data.

-   We will focus on important features, not all variables.
-   We need the change of the independent variable and the dependent variable in relation to other variables.

The two above are only possible when we have a fairly accurate model. So let’s build a good enough model first.

## Feature correlation

![](https://miro.medium.com/max/60/1*B88mJuae68gr3o4fEh1vow.png?q=20)

![](https://miro.medium.com/max/1400/1*B88mJuae68gr3o4fEh1vow.png)

Dendogram

I use  `dendogram`  to see how the variables are correlated. Note that the C and D services are highly correlated with each other, probably these are two additional services. Similarly we also have high correlation between services B and F, A and G, A and E, C and D …

## Feature importance

I will use the permutation importance instead of the importance function provided by default by lightGBM. In this step, the highly correlated features will be grouped into a group to calculate the importance of the group.

Notice that Service A greatly affects whether the customer leaves or not. E, M, H services do not affect much. A user’s age does not affect whether he or she is likely to leave.

You will find it a bit strange that the age variable is separated into small variables, the value of these variables is 0 or 1 representing whether the customer is that age or not. Processes like this are just for the tree model to make decisions faster (because it only has to branch once) and not mean anything else.

## Model

I use lightgbm with the hyper-parameter

![](https://miro.medium.com/max/60/1*6wOsjrItCP570mIJn9D-pw.png?q=20)

![](https://miro.medium.com/max/1400/1*6wOsjrItCP570mIJn9D-pw.png)

The model that identifies the customer is still using the service or not (quite obvious) but not very good for the customer leaving. As can be seen from the graph, if the threshold of 0.5 is set, there is still about 50% chance of mistakenly identifying the customer leaving to still use.

## Model interpretation

Let’s see an example with customers who continue to use the service.

![](https://miro.medium.com/max/60/1*Gkalb9myptid_foBZrK_3Q.png?q=20)

![](https://miro.medium.com/max/1400/1*Gkalb9myptid_foBZrK_3Q.png)

And a customer leaves.

![](https://miro.medium.com/max/60/1*d4bdMg8T3Lc_H6vmPQoTkQ.png?q=20)

![](https://miro.medium.com/max/1400/1*d4bdMg8T3Lc_H6vmPQoTkQ.png)

Recall that Service A has a huge impact on whether or not customers leave, much like we saw in defining feature importance. Let’s look at it in a little more detail.

![](https://miro.medium.com/max/60/1*pjuJESCT8eXGcsmKNwXdOg.png?q=20)

![](https://miro.medium.com/max/1400/1*pjuJESCT8eXGcsmKNwXdOg.png)

In general, patterns are only really obvious in months near month t. This is quite understandable.

Total number of service times

Pattern is only quite clear with the last 2 months.

![](https://miro.medium.com/max/60/1*rDFuk5GB6NCWJXxDey-Sog.png?q=20)

![](https://miro.medium.com/max/1400/1*rDFuk5GB6NCWJXxDey-Sog.png)

However, the trend of these 2 months is opposite, if in month n-1, the increase in the total number of service usage increases the likelihood of customers leaving, then in month n, it is the opposite. again.

But certainty for feature at month n is much higher than for month n-1. At month n, customers are at risk of leaving if the total number of service uses is less than about 200 times of use. With the total number of service usage times greater than 200 times, customers tend to continue using the service.

## Total number of uses of service A (both free and paid)

![](https://miro.medium.com/max/60/1*Ewih0uFWfkkVJbedwSB5DA.png?q=20)

![](https://miro.medium.com/max/1400/1*Ewih0uFWfkkVJbedwSB5DA.png)

There is a discrepancy in the impact of service usage on the results. With the number of times from 0 — about 50 times, customers tend to continue using. With a higher number of uses, from about 50 times to about 200 times, this makes customers tend to leave, while this trend is in contrast to using service A greater than 200.

## Total duration of service usage A

In month: (n-5) we see an interesting pattern:

![](https://miro.medium.com/max/60/1*cmmhqyUzAg1mtlt-jd3pDg.png?q=20)

![](https://miro.medium.com/max/1400/1*cmmhqyUzAg1mtlt-jd3pDg.png)

The allocation at 30,000 in my opinion is not accidental but due to product policy. Maybe this is a product related to minutes of service. Groups with a total service duration of less than 500 minutes tended to continue using, while groups with more than 500 minutes of use tended to leave. However, it must be noted that the impact of this variable is not large. This pattern appears only in month: n-5, not in the following months, personally I find this a bit confusing.

![](https://miro.medium.com/max/60/1*OiLWQljCUbkodM5L-F46lQ.png?q=20)

![](https://miro.medium.com/max/1400/1*OiLWQljCUbkodM5L-F46lQ.png)

In month n, in general, the total time using service A increases, the more customers tend to use. This trend is similar to  **Total Service Usage.**

**Total number of times using service A (fee)**

![](https://miro.medium.com/max/60/1*ZoAIS4ValGEkWxb20gV08Q.png?q=20)

![](https://miro.medium.com/max/1400/1*ZoAIS4ValGEkWxb20gV08Q.png)

The trend is in complete contrast to the features above. In general, the more times you use the service, the more likely you are to leave.






Recall that we used  `lightgbm`  for the classification problem because the features are arranged as tabular data. However, such a treatment causes the model to treat features with consecutive time series as independent variables.

Let’s use RNN to see if this method is more effective in this post. Since the data is now a sequence of multiple variables, we will also try to find other solutions instead of the libraries available to interpret the model.

## Transfer learning

Transfer Learning in Deep Learning is a technique in which :

-   A  **_pre-trained model_**  has been trained on specific source tasks, so part or all of the  **_pre-trained model_**  can be reused depending on the task of each layer in the model.
-   A new model uses part or all of the pre-trained model to learn a target tasks and depending on the task of each layer, the new model can add other layers based on the pre-trained model available.

Transfer learning has proven to be understood through in computer vision and NLP. In this article we will try to see if it works for tabular data or not:

![](https://miro.medium.com/max/60/1*AHut60L1_Y2C_zx6kn5EnA.png?q=20)

![](https://miro.medium.com/max/1400/1*AHut60L1_Y2C_zx6kn5EnA.png)

Recall that with the carrier data we are using, there are 25 behavioral sequence features in 6 months. So, we can divide the training process into two steps:

-   **Step 1**: Temporarily called “behavior model” with the goal of predicting the value of the 25 features above in the nearest month. This is similar to predicting customer behavior given their past data.
-   **Step 2**: the step in predicting customers leaving. Now that the head used to predict above will be removed, the pre-trained weight will be reused for the RNN model, the new head part will be used to predict the customer leaving.

For RNN model, I use  [AWD-LSTM](https://arxiv.org/abs/1708.02182)  because it has good regularization ability. The 2 heads used to predict the above 2 tasks are simply FCN, not anything special.

Use attention to increase the accuracy of the model as well as as a basis for interpreting the model later. As above we have one attention layer for the feature, and one for the time series of each feature.

## The results of model

![](https://miro.medium.com/max/60/1*JRX_iW0uBiljXAviW_fqmg.png?q=20)

![](https://miro.medium.com/max/1400/1*JRX_iW0uBiljXAviW_fqmg.png)

The model has quite high accuracy is a good thing for us to proceed to the next model interpretation steps.

## Partial dependence

Using the Partial dependence to see how features change over the month. The algorithm is quite simple, at each month of each feature, for each value from low too high. I apply to all records then average the predicted result to know the impact of this value on result.

Note that the algorithm simply does not mean that it takes less time to compute. So we cannot choose too many from low to high.

![](https://miro.medium.com/max/60/1*Ul6S2B7Nu-4aR0pfazRBBg.png?q=20)

![](https://miro.medium.com/max/1400/1*Ul6S2B7Nu-4aR0pfazRBBg.png)

Here dark blue represents a guess value closer to 1 — That is, the customer leaves, and yellow indicates the customer tends to continue using the service.

We can see the general trend of variables, for example, with service A usage.

![](https://miro.medium.com/max/60/1*-xBoeFxR0lYzRtuDZDX5Pw.png?q=20)

![](https://miro.medium.com/max/1400/1*-xBoeFxR0lYzRtuDZDX5Pw.png)

In general, in months close to month t and with a large number of service use times, customers tend to leave.

![](https://miro.medium.com/max/1400/1*xkEyHoklU2k9RhSJ01JK8g.png)

For some other variables, the low service charge at all months is a sign of the customer leaving.

![](https://miro.medium.com/max/60/1*oC9xR8MnQ3gHAvxT7JvfWg.png?q=20)

![](https://miro.medium.com/max/1400/1*oC9xR8MnQ3gHAvxT7JvfWg.png)

## Shap value

We were able to understand overall how variables affect the predicted results of the model. However, that is only the population as we average all the predictions. Next let’s build an algorithm to see in a specific case, each variable will have a negative or positive impact on the predicted results.

I use the SHAP value to do this. However, the exact calculation of the SHAP value takes a lot of time, so I use an approximate algorithm described in the article:

![](https://miro.medium.com/max/60/1*jk0xO0VQr-5BJnJ9X0PiTQ.png?q=20)

![](https://miro.medium.com/max/1400/1*jk0xO0VQr-5BJnJ9X0PiTQ.png)

## Attention

That we use attention in our model, let’s see how they help in interpreting the results of the model. Here I combine the attention value and the variable’s value to see which patterns are noticed by the model. Combined with the shap values, we get a complete view of how variables affect the model, and which patterns are causing that result.

Here I focus only on the records that the model predicts will leave because that’s what we are concerned about.

![](https://miro.medium.com/max/60/1*lJhrpdJ1g8MYRRbl4qcRRw.png?q=20)

![](https://miro.medium.com/max/1400/1*lJhrpdJ1g8MYRRbl4qcRRw.png)

At the bottom left are the shap values, note that blue represents positive values — values that make the predicted values closer to 1. In that gas, red represents negative values — values that make the predicted values closer to 0. The rightmost is the value of attention corresponding to 6 months of 25 variables. The darker the green value, the greater the value at that time. The middle is a map graph between the attention value and the time series of each variable.



With the above 3 graphs we can see how the model makes a prediction about a customer about to leave. The example above is due to the sudden decrease in the number of A service usage (paid and free), the 2 months ago decrease in the C fee, or the increase in service A usage after 3 months ago.

Let’s try to see another record of the customer will leave another.

![](https://miro.medium.com/max/60/1*kTVaAV73XpF0slDe0spgUA.png?q=20)

![](https://miro.medium.com/max/1400/1*kTVaAV73XpF0slDe0spgUA.png)
