# aida-students-project
Students Project - Portuguese University Slots

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


```python
df = pd.read_csv(filepath_math_raw, sep=';')
df.columns = df.columns.str.lower()
```

# collect all binary columns
```python
columns_binary = df.nunique().loc[df.nunique() == 2].index
```
# encdoe binary columns
```python
dict_bin = { }
for column in columns_binary:
  lb = preprocessing.LabelBinarizer()
  df[column] = lb.fit_transform(df[column])
  dict_bin[column] = lb.classes_

df[columns_binary] = df[columns_binary].astype(bool)
```
