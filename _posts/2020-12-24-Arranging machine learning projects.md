 As I said in the previous post: 
Please remember that we prefer working in an IDE/text editor rather than jupyter notebooks. You can also work in jupyter notebooks, and it’s totally up to you. However, I will be using jupyter only for things like data exploration and for plotting charts and graphs. We will build the classification framework in such a way that most problems will become plug and play. You will be able to train a model without making too many changes to the code, and when you improve your models, you will be able to track them using git.
Let’s look at the structure of the files first of all. For any project that you are doing, create a new folder. For this example, I am calling the project “project”. The inside of the project folder should look something like the following.
```
├── input
│   ├── train.csv
│   └── test.csv
├── src
│   ├── create_folds.py
│   ├── train.py
│   ├── inference.py
│   ├── models.py
│   ├── config.py
│   └── model_dispatcher.py
├── models
│   ├── model_rf.bin
│   └── model_et.bin
├── notebooks
│   ├── exploration.ipynb
│   └── check_data.ipynb
├── README.md
└── LICENSE
```




Let’s see what these folders and file are about.
input/: This folder consists of all the input files and data for your machine learning
project. If you are working on NLP projects, you can keep your embeddings here.
If you are working on image projects, all images go to a subfolder inside this folder.
src/: We will keep all the python scripts associated with the project here. If I talk
about a python script, i.e. any *.py file, it is stored in the src folder.
models/: This folder keeps all the trained models.
notebooks/: All jupyter notebooks (i.e. any *.ipynb file) are stored in the notebooks
folder.
README.md: This is a markdown file where you can describe your project and write instructions on how to train the model or to serve this in a production
environment.
LICENSE: This is a simple text file that consists of a license for the project, such as MIT, Apache,...


 ```python
    # train.py
    import argparse
    import os
    import joblib
    import pandas as pd
    from sklearn import metrics
    
    import config
    import model_dispatcher
    
    def run(fold, model):
	    # read the training data with folds
	    df = pd.read_csv(config.TRAINING_FILE)
	    # training data is where kfold is not equal to provided fold
	    # also, note that we reset the index
	    df_train = df[df.kfold != fold].reset_index(drop=True)
	    # validation data is where kfold is equal to provided fold
	    df_valid = df[df.kfold == fold].reset_index(drop=True)
	    # drop the label column from dataframe and convert it to
	    # a numpy array by using .values.
	    # target is label column in the dataframe
	    x_train = df_train.drop("label", axis=1).values
	    y_train = df_train.label.values

	    # similarly, for validation, we have
	    x_valid = df_valid.drop("label", axis=1).values
	    y_valid = df_valid.label.values
	    # fetch the model from model_dispatcher
	    clf = model_dispatcher.models[model]
	    # fir the model on training data
	    clf.fit(x_train, y_train)
	    # create predictions for validation samples
	    preds = clf.predict(x_valid)
	    # calculate & print accuracy
	    accuracy = metrics.accuracy_score(y_valid, preds)
	    print(f"Fold={fold}, Accuracy={accuracy}")
	    # save the model
	    joblib.dump(
	    clf,
	    os.path.join(config.MODEL_OUTPUT, f"dt_{fold}.bin"))

    if __name__ == "__main__":
	    parser = argparse.ArgumentParser()
	    parser.add_argument(
	    "--fold",
	    type=int
	    )
	    parser.add_argument(
	    "--model",
	    type=str
	    )
	    args = parser.parse_args()
	    run(
	    fold=args.fold,
	    model=args.model
	    )
 ```
**So we could see what is in # model_dispatcher.py**

```python
    from sklearn import ensemble
    from sklearn import tree
    models = {
    "decision_tree_gini": tree.DecisionTreeClassifier(
    criterion="gini"
    ),
    "decision_tree_entropy": tree.DecisionTreeClassifier(
    criterion="entropy"
    ),
    "rf": ensemble.RandomForestClassifier(),
    }
  ```
  So now we could run this code:
  

    ```  #!/bin/sh
    python train.py --fold 0 --model rf
    python train.py --fold 1 --model rf
    python train.py --fold 2 --model rf
    python train.py --fold 3 --model rf
    python train.py --fold 4 --model rf

