---
title: "OOP for machine learning"
excerpt: "Pipeline for Model deployment."
categories: 
  - Machine learning
  - Pipe line
  - OOP
---

#   Why need OOP for machine learning  \n Pipeline for Model deployment,



![Accelerating the Entire Deep Learning Pipeline | by NVIDIA AI | Medium](https://miro.medium.com/max/1400/0*cusRyqQTJEj2ZTCJ)

Figure 1 : Pipeline



# The “byte” of theory: 8 facts to know about a fully reproducible Machine learning pipeline

> **Fact #1**
> 
> **A typical Machine learning pipeline for production is mainly composed by four steps: Data Gathering, Features engineering, Features selection and Model Building.**

![](https://miro.medium.com/max/700/1*FlhMOVI26yBk0zYZrIMzpg.png)

Figure 2: A typical Machine learning pipeline
> 
> **It will be reproducible if you have to be able to create a copy of your model and both models return the same output given the same raw data as input.**
>
> 
> **If we focus only on the programming side, the two main approaches for Model Reproducibility are: Procedural Programming and Object-oriented programming (OOP)**
>
> **A procedural pipeline is a sequence of functions. Each one runs after the other. And they covers all steps of the ML process (feature engineering and model building) both for training and for scoring.**



![](https://miro.medium.com/max/487/1*HOfeuX8SDwGNnggrBcMUjw.png)

Figure 3: Procedural Pipeline

> **Fact #6**
> 
> **With OOP, the pipeline becomes an unique “object” instead. So you have a program-code-template that combines model variables (attributes) and functions (methods) values for each steps of Machine Learning process in each state (training and scoring).**
> 

![](https://miro.medium.com/max/384/1*q23pTGNm7_5c1uxxcVMEEQ.png)

Figure 4: Object-Oriented Pipeline

> **Fact #7**
> 
> **While the procedural pipeline is custom by definition, you can have both your OOP pipeline or you can leveraging third party machine learning pipeline. The most common example is Scikit-Learn Pipeline.**

In particular, in Scikit-Learn you have three main Objects:

1.  **Transformers**: class to transform data. Examples are Scalers, Feature selectors and One hot encoders.
2.  **Predictor**: class to fit and apply the model for predictions

and the  **Pipeline** is the  class to wrap all transformers and predictor in a sequence.

actice.

And, in this Part 1, I will describe how I transform a model from Kaggle to a Procedural Machine Learning Pipeline ready for production.

# A example of Procedural Machine Learning pipeline: From Kaggle…to “production-ready”.

Below you can see an self-explicable high level architecture.

![](https://miro.medium.com/max/1000/1*LqmvXEMt4ijimbsxgJETiQ.jpeg)

Compared to the previous projects, this time I add a development workflow in the repository *. 

# Build the Procedural Model Pipeline



Fully conscious of this,  **I study their analysis** and  **I create one notebooks for each steps.** Finally  **I wrap up the main steps and the model in the Model Deployment notebook.**

But,  **because the Procedural Pipeline needs variables and transformation parameters, for each step I had to store the relative information.**

> Procedural Pipelines implies Hard Coding ML MetaData.

For example, for the variable “Age”, this original feature engineering code


Despite this,  **one of the advantages to build a Procedural Machine Learning pipeline is that in the 90% of the time you can build functions straightforward to the code as it is in the notebook.**


# **Final Consideration**

For now, the only point I miss to verify is how much difficult might be building the machine learning system to serve that model.


**Actually, it’s a very small piece** based on Google’s  [Hidden Technical Debt in Machine Learning Systems](https://papers.nips.cc/paper/5656-hidden-technical-debt-in-machine-learning-systems.pdf)  paper.

![](https://miro.medium.com/max/700/1*7Azj1OeAcyR7eVV_uKBBLg.gif)

Figure 8: Elements for ML systems. Adapted from  [Hidden Technical Debt in Machine Learning Systems](https://papers.nips.cc/paper/5656-hidden-technical-debt-in-machine-learning-systems.pdf)

Even though several advantages this approach provides (for example, “soft” software developer skills required and the code that becomes your pipeline functions), I face several challenges.

Indeed,

-   **I spend a lot of time on debugging “get_dummies” effect on the feature inputs.**

But even worse,

-   I had to define a very detailed configuration  **for transformation parameters**

For those reasons, in this article I use the same example to explore the alternative programming approach to build machine learning pipeline:  **_Object-oriented programming (OOP)._**

But, before to jump into the code, I think it’s important to dive deep some  **main concepts**  I’m going to use later on.

# Object-Oriented Programming for Data Scientists

![](https://miro.medium.com/max/700/1*oj8wU56saFMGDvVjzlj8SA.png)

Figure 2: The classic ‘CAR’ OOP example

Imagine you have to explain  **how your Machine Learning Pipeline looks like and what it does to Machine Learning Engineer.** You might say

> **My pipeline is a “sequence of code snippets” I have in cells that generates some output given some “data input” and “parameters”**

Well,  you describe a  **_Class_** where

-   **“sequence of code snippets”**  represent its  **_Methods_**
-   **“data input” and “parameters”**  represent  its  **_Attributes_**

Of course,  **each time you change these Attributes** (data input and parameters in your code) you can build or “**_construct”_** a different Pipeline  **_Object_** from a Class calling Methods.

Notice I use “construct” term because one of the special methods is the  **Constructor.** It's the function which uses the  `__init__`  keyword and it allows to create the object from a class  **instantiating** the attributes of that class.

Below the high-level anatomy of the pipeline.

![](https://miro.medium.com/max/30/1*Ii0IfbSRi5QodZ7CMccurg.png?q=20)

![](https://miro.medium.com/max/700/1*Ii0IfbSRi5QodZ7CMccurg.png)

Figure 3: The Anatomy of a Pipeline

As you can see I design the Pipeline in order to have

> **The processing steps as objects. The Pipeline as a sequence of objects (OOP).**

A  **couple of considerations**  about the picture:

-   In this case, I only use  **Instance Variables**  ie variables which becomes accessible after the object is instantiated. And they differ from  **Class Variables** which are always accessible. Below the random_state variable if I had define it like a class variable.
-   `Missing_Imputer`  **is a component of the Pipeline**. This is because the  **Composition** property. In the example the  `fit`method in the Pipeline`class contains the`Missing_Imputer`  in the  `Preprocessing`  class. The idea here is that every Pipeline “has a” Data Preprocessing step. Then It has to access to that class.
-   Another important difference is between an  **Instance Method**  and a  **Class Method.** Essentially, an Instance method allows us to access instance variables (“self” variables) while Class method allows us to access class variables and consequently modify the class itself.

Of course, this is not an article about Object Oriented Programming. There are many other concepts a Data Scientist needs to know (for example Data Encapsulation, Inheritance, Polymorphism and soon).

But it is important to have an idea of  **these concepts because they justify OOP methods for building Machine Learning Pipeline**.

So the question is

> _What about the benefits of “Custom” Object Oriented Machine Learning Pipelines?_

# “Custom” Object Oriented Machine Learning Pipelines: Pros and Cons

Based on this experience, one of the greatest advantages of this approach is related at Development stage.

Indeed, because  **the Pipeline becomes just a representation of a series of entities which maybe tested, versioned, tracked and controlled easily.** And notice this is possible because the nature of class.

> _Because its nature, Pipeline class becomes a representation of a series of entities which maybe tested, versioned, tracked and controlled easily._

Indeed,  **this Pipeline promotes independence between entities of the entire process.** In my case, I thought it was great to have several Component Classes (Preprocessing, Models, PostProcessing) and then build a Pipeline methods based on them.  It made me feel like a mason with bricks and mortar.

> **_Object Oriented Pipelines promotes independence between steps of the entire process._**

Related to what I mentioned in the previous article, I can imagine how it could improves  **coding best practices in a development team**  and it could implies  time and financial cost savings.

> _They improve Software Development Practices_

Also because  **this independence I could imagine to build more articulated processes those could satisfy business needs better**. In my case I’ve just apply that to build a composite  `predict`method.

But think about the business uplift a cascade pipeline could generates because you can build a sequences of independent models each of them attempts to classify what the previous one misses.

> **_You can build more articulated processes those could satisfy business needs better._**

So, at that time, you may imagine the “Custom” Object Oriented Machine Learning Pipelines like one of the most powerful power ups of Super Mario World: the Star Power Up. It would allow you to run faster and kick out the model deployment challenges.

But, let me to show you my code

Even if I described it, It may looks like a bit  **“complex”**  right? Especially if you are a Research Data Scientist.

> _Building a Object Oriented Pipeline implies Software complexity_

Then if we deep dive to the code, we can see how **it strongly depends on the data the pipeline ingests and on the model.**

> _Each methods that deal with data may need to be code again. Also a new model may needs a new pipeline._

Below you can see that we decide to replace ‘?’ character with ‘missing’. But what if the data that come from the field have a different unknown character?

The same is for the model. Look at the model attributes of the pipeline. They work just for Sklearn’s RandomForestClassifier.

Of course, complexity and data-model pipeline dependency have several consequences we can summarize saying that  **you need software developers to build and maintain these pipelines. Otherwise, they slow down Data Scientists those would have to familiarize with the software**  (and not just the code).

That is, if you throw the Full-Stack Data Scientists ;)

> _To build a good Object Oriented Pipeline, you may need a Software Development Team._
I will write more detail about OOP with sklearn in next post!



