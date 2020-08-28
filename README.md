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

First we conducted some data preprocessing (no missing or duplicate values were present), in particular binary encoding and one hot encoding all relevant variables.

Afterwards we proceeded as follows:

1. Establishing a linear regression baseline and calcualate cross validation scores `cv=5` on all three subtasks.
2. See whether can improve on our metric R-squared with a Random Forest Regressor.
3. Check and interpret the `feature_importances_` attribute of our models to be able to answer the question.
####Baseline and Random Forest

![![Correlation Heatmap](https://github.com/igoekce/aida-students-project/blob/master/Docs/Pictures/crossvalscoreScenFam.png?raw=true)](https://github.com/igoekce/aida-students-project/blob/master/Docs/Pictures/crossvalscoreScenFam.png?raw=true)
