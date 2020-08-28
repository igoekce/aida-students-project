# aida-students-project
Students Project - Portuguese University Slots

## Introduction

As a project in our Data Science journey, we had the task to analyze the Student Performance Data Set[(click here for the UCI Link)](https://archive.ics.uci.edu/ml/datasets/student+performance).
To summarize briefly, the data displays student performance of two Portuguese schools in secondary education. The grades are collected at three distinct times and are distinguished for subjects Mathematics and Portuguese. Data is expanded through many attributes such as `sex, age, studytime` etc. For a full description, please follow the link above.
Our task was not to create an application or deliver certain predictions, but rather compare and identify several prediction strategies. 
Mainly we want to know how well we can predict our **target**
`y = mean(grades)`
in three different scenarios:
* 0 out of 3 numeric grades are present in the feature set.
* 1 out of 3 numeric grades are present in the feature set.
* 2 out of 3 numeric grades are present in the feature set.
Unless stated otherwise, all calucalations and figures are based on the  Mathematics part of the dataset.

## EDA

Let's have a brief look at our dataset and get a better feel for it.

![![alt text](https://github.com/igoekce/aida-students-project/blob/master/Docs/Pictures/distributionofbinaryattributes.png)](https://github.com/igoekce/aida-students-project/blob/master/Docs/Pictures/distributionofbinaryattributes.png)

Magenta label (grey in brackets)
- `school`: Mousinho da Silveira (Gabriel Pereira)
- `sex`: Male (Female)
- `address`: Urban (Rural)
- `famsize`: Less or equal than 3 (Greater than 3)
- `pstatus`: living together (apart)
- `schoolsup, famsup, paid, activities, nursery, higher, internet, romantic`: yes (no)

Some interesting insights regarding our task:
1. 95% of the students want to pursue higher education
2. 45% of the students have paid classes to support their education.

![![Correlation Heatmap](https://github.com/igoekce/aida-students-project/blob/master/Docs/Pictures/Corr%20heatmap.png?raw=true)](https://github.com/igoekce/aida-students-project/blob/master/Docs/Pictures/Corr%20heatmap.png?raw=true)

The heatmap has three distinct spots we would like to comment on:

1. Education level of the parents seems to be somewhat correlated. This is interesting when you follow discussions regarding education level of parents and how this correlates with educational performance of their children.
2. Daily alcohol consumption is correlated with weekly alcohol consumption and both are (although slightly less) correlated with how often students go out with their friends.
3. All three grades are highly correlated with one another. The only other attribute which could be of importance seems to be failures (indicating previous class failures)

## Strategy

###Regression

####Baseline and Random Forest

First we conducted some data preprocessing (no missing or duplicate values were present), in particular binary encoding and one hot encoding all relevant variables.

Afterwards we proceeded as follows:

1. Establishing a linear regression baseline and calcualate cross validation scores `cv=5` on all three subtasks.
2. See whether can improve on our metric R-squared with a Random Forest Regressor.
3. Check and interpret the `feature_importances_` attribute of our models to be able to answer the question.

![![Correlation Heatmap](https://github.com/igoekce/aida-students-project/blob/master/Docs/Pictures/crossvalscoreScenFam.png?raw=true)](https://github.com/igoekce/aida-students-project/blob/master/Docs/Pictures/crossvalscoreScenFam.png?raw=true)

When using Linear Regression as a baseline model, we can see that our cross validation score is slightly negative for our first scenario (when there are no numeric grades present in the feature set).

We can also see that once we introduce a single grade, the score scyrockets and with the second grade introduced, scratches a perfect prediction. 

The Random Forest Regressor is able to produce a small positive number in the first scenario, albeit still too low to use it in a productive session.

Scores in the second and third scenario are also higher than in the baseline configuration, yet differences are minorish (~4% and ~1% for second and third scenario respectively).

![![RandomForrestTree](https://github.com/igoekce/aida-students-project/blob/master/Docs/Pictures/RandomForrestTree.png?raw=true)](https://github.com/igoekce/aida-students-project/blob/master/Docs/Pictures/RandomForrestTree.png?raw=true)

To better visualize the underlying process, we decided to show a decision tree. For clarity we chose `max_depth=3` and will only present scenarios 1 and 2.

So we can see that the features used like `failures` and `schoolsup` are used in the regression model. Also the orange path depicts a random sample and how it would have fared in our decision tree.

![![RandomForrestTree2](https://github.com/igoekce/aida-students-project/blob/master/Docs/Pictures/RandomForrestTree2.png?raw=true)](https://github.com/igoekce/aida-students-project/blob/master/Docs/Pictures/RandomForrestTree2.png?raw=true)

In contrast, once we introduce a single grade (g1) nearly all nodes are relying on this feature (with only node 2 and 9 being exemptions).

#### Grid Search
We were curios how much we could improve our score in the first scenario with a grid search.

````param_grid = {
    'max_depth': range(5, 30, 5),
    'n_estimators': range(50, 300, 50)
    }
```
```
Fitting 5 folds for each of 25 candidates, totalling 125 fits
[Parallel(n_jobs=-1)]: Using backend LokyBackend with 2 concurrent workers.
[Parallel(n_jobs=-1)]: Done  37 tasks      | elapsed:    8.7s
[Parallel(n_jobs=-1)]: Done 125 out of 125 | elapsed:   36.1s finished
GridSearchCV(cv=5, error_score=nan,
             estimator=RandomForestRegressor(bootstrap=True, ccp_alpha=0.0,
                                             criterion='mse', max_depth=None,
                                             max_features='auto',
                                             max_leaf_nodes=None,
                                             max_samples=None,
                                             min_impurity_decrease=0.0,
                                             min_impurity_split=None,
                                             min_samples_leaf=1,
                                             min_samples_split=2,
                                             min_weight_fraction_leaf=0.0,
                                             n_estimators=100, n_jobs=None,
                                             oob_score=False, random_state=None,
                                             verbose=0, warm_start=False),
             iid='deprecated', n_jobs=-1,
             param_grid={'max_depth': range(5, 30, 5),
                         'n_estimators': range(50, 300, 50)},
             pre_dispatch='2*n_jobs', refit=True, return_train_score=False,
             scoring=None, verbose=2)
```


