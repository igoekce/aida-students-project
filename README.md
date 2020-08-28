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
