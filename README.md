# aida-students-project
Students Project - Portuguese University Slots

## Introduction

As a project in our Data Science journey, we had the task to analyze the Student Performance Data Set [(click here for the UCI Link)](https://archive.ics.uci.edu/ml/datasets/student+performance).
To summarize briefly, the data displays student performance of two Portuguese schools in secondary education. The grades are collected at three distinct times and are distinguished for subjects Mathematics and Portuguese. Data is expanded through many attributes such as `sex, age, studytime` etc. For a full description, please follow the link above.
Our task was not to create an application or deliver certain predictions, but rather compare and identify several prediction strategies. 
Mainly we want to know how well we can predict our **target**
`y = mean(grades)`
in three different scenarios:
* Scenario 1: 0 out of 3 numeric grades are present in the feature set.
* Scenario 2: 1 out of 3 numeric grades are present in the feature set.
* Scenario 3: 2 out of 3 numeric grades are present in the feature set.

Unless stated otherwise, all calculations and figures are based on the  Mathematics part of the dataset.

## EDA

Let's have a brief look at our dataset and get a better feel for it.

![![binary attributes](https://github.com/igoekce/aida-students-project/blob/master/Docs/Pictures/distributionofbinaryattributes.png)](https://github.com/igoekce/aida-students-project/blob/master/Docs/Pictures/distributionofbinaryattributes.png)

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

Further Insights we tried to gain through the correlation heatmap:

![![Correlation Heatmap](https://github.com/igoekce/aida-students-project/blob/master/Docs/Pictures/Corr%20heatmap.png?raw=true)](https://github.com/igoekce/aida-students-project/blob/master/Docs/Pictures/Corr%20heatmap.png?raw=true)

The heatmap has three distinct spots we would like to comment on:

1. Education level of the parents seems to be somewhat correlated. This is interesting when you follow discussions regarding working class vs academic children.
2. Daily alcohol consumption is correlated with weekly alcohol consumption and both are (although slightly less) correlated with how often students go out with their friends.
3. All three grades are highly correlated with one another. The only other attribute which could be of importance seems to be failures (indicating previous class failures)

## Strategy

### Regression

#### Baseline and Random Forest

First we conducted some data preprocessing (no missing or duplicate values were present), in particular binary encoding and one hot encoding all relevant variables.

Afterwards we proceeded as follows:

1. Establishing a linear regression baseline and calcualate cross validation scores `cv=5` on all three subtasks.
2. See whether can improve on our metric R-squared with a Random Forest Regressor.
3. Check and interpret the `feature_importances_` attribute of our models to be able to answer the question.

![![Correlation Heatmap](https://github.com/igoekce/aida-students-project/blob/master/Docs/Pictures/crossvalscoreScenFam.png?raw=true)](https://github.com/igoekce/aida-students-project/blob/master/Docs/Pictures/crossvalscoreScenFam.png?raw=true)

When using Linear Regression as a baseline model, we can see that our cross validation score is slightly negative for our first scenario (when there are no numeric grades present in the feature set).

We can also see that once we introduce a single grade, the score skyrockets and with the second grade introduced, scratches a perfect prediction. 

The Random Forest Regressor is able to produce a small positive number in the first scenario, albeit still too low to use it in a productive fashion.

Scores in the second and third scenario for the Random Forest model are also higher than the Linear Regression model, yet differences are minorish (~4% and ~1% for second and third scenario respectively).

#### Decision Tree

Scenario 1 Decision Tree:

![![RandomForrestTree](https://github.com/igoekce/aida-students-project/blob/master/Docs/Pictures/RandomForrestTree.png?raw=true)](https://github.com/igoekce/aida-students-project/blob/master/Docs/Pictures/RandomForrestTree.png?raw=true)

To better visualize the underlying process, we decided to show a decision tree. For clarity we chose `max_depth=3` and will only present scenarios 1 and 2.

So we can see that the features used like `failures` and `schoolsup` are used in the regression model. Also the orange path depicts a random sample and how it would have fared in our decision tree.

Scenario 2 Decision Tree:

![![RandomForrestTree2](https://github.com/igoekce/aida-students-project/blob/master/Docs/Pictures/RandomForrestTree2.png?raw=true)](https://github.com/igoekce/aida-students-project/blob/master/Docs/Pictures/RandomForrestTree2.png?raw=true)

In contrast, once we introduce a single grade (g1) nearly all nodes are relying on this feature (with only node 2 and 9 being exemptions).

#### Grid Search
We were curios how much we could improve our score in the first scenario with a grid search.

```
             param_grid={'max_depth': range(5, 30, 5),
                         'n_estimators': range(50, 300, 50)},
```

Our Grid Search returned a best score of **0.20** which is not a substantial increase compared to our non-tuned random forest.

#### Feature Selection

So, we were still a bit curious and wanted to double check, how exactly the features resonate with our target variable. We therefore decided to conduct a feature selection and compare it with the `feature_importances_` attribute of our forest.

![![Correlation between numerical features and target variable](https://github.com/igoekce/aida-students-project/blob/master/Docs/Pictures/corrNumAndTargetVar.png?raw=true)](https://github.com/igoekce/aida-students-project/blob/master/Docs/Pictures/corrNumAndTargetVar.png?raw=true)

When target and input variables are numerical, pearson correlation can be used to identify the most important features. As expected results indicated that the grades are highly correlated with our target variables (*r* > 0.9).

For the categorical features we opted to do a feature selection using the ANOVA test. As in the Decision Tree, we can see `failures` as a highly relevant feature. We were curious though just how similar the rankings of the features, so we decided to make a comparison.

```
corr dt_importances & skb_importances    0.572766
```
Our *r* is 0.57 which is pretty substanstial, given the fact that we selected a random Decision Tree which also had access to non-categorical data. In conclusion although it is clear that previous performance is highly relevant for actual performance, the ranking of the categorical columns seems to be stable.

### Classification

So, we have little success in predicting student performance without prior performance data, but what if we just want to know whether a student has *passed* or not. So we decided that for any mean grade 9 or lower that a student had *not passed*, while a grade 10 or higher meant that a student had *passed*. 

#### Baseline and Random Forest Classifier

![![Correlation Heatmap](https://github.com/igoekce/aida-students-project/blob/master/Docs/Pictures/crossvalscoreScenFamclass.png?raw=true)](https://github.com/igoekce/aida-students-project/blob/master/Docs/Pictures/crossvalscoreScenFamclass.png?raw=true)

Alright! So once we change the question from *how good* a student is to whether a student is *good enough*, we achieve quite respectable scores, even in scenario 1.

#### Confusion Matrix

![![Correlation Heatmap](https://github.com/igoekce/aida-students-project/blob/master/Docs/Pictures/confusion%20matrix.png?raw=true)](https://github.com/igoekce/aida-students-project/blob/master/Docs/Pictures/confusion%20matrix.png?raw=true)

Looking at our confusion matrix, we can see that we have little cases where we miss someone who could pass (5 cases) but we often predict someone to pass, who in reality would not pass (25 cases). So universities could use our model without grades as an upper bound, as probably less people will be passing anyway.

## Summary

![![Results table](https://github.com/igoekce/aida-students-project/blob/master/Docs/Pictures/2020-08-28%20Students_score.png?raw=true)](https://github.com/igoekce/aida-students-project/blob/master/Docs/Pictures/2020-08-28%20Students_score.png?raw=true)

**Conclusions:**

- Student's success is highly affected by previous performances.
- However, it is possible to achieve good predictions on student's success even without using previous grades G1, G2, when thinking of passed vs failed.
- Selection of features (e.g. SKB) or parameter gridsearch did not lead to major changes.
- Random Forest models slightly better than Linear or Logistic Regression models. 

## Future Research

- Predictions without knowledge of previous grades require deeper insights into social and economic features (eg. income data, parents job and education, living environment)
- Desirable would be an investigation into different cluster of students with respect to their success at school 
- Further analysis could help to improve the students' opportunities for more success in the eductional system!

Contribution by Yasin Edin, Andreas Neutze, Norbert Jendreizik, Ibrahim Gökce
