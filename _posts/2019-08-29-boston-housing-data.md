---
title: "Used Linear Regression to Model and Predict Housing Prices with the Classic Boston Housing Dataset"

date: 2019-08-29

datatags: [sample data, labeled data]
technologytags: [sklearn, seaborn]
techniquetags: [machine learning, linear regression]

categories: [Model Data]
header:
  image: "/images/boston-housing-data_files/house-header.jpg"
excerpt: "Linear Regression is one of the fundamental machine learning techniques in data science. It makes predictions..."
mathjax: "true"
classes: wide #expand the page
---
{% include toc icon="book-open" title="Table of Contents" %}<br/>

## Project Description
Predicted suburban housing prices in Boston of 1979 using Multiple Linear Regression on an already existing dataset, "Boston Housing" to model and analyze the results. I deal with missing values, check multicollinearity, check for linear relationship with variables, create a model, evaluate and then provide an analysis of my predictions.


## Project Replicated From
1. <https://www.weirdgeek.com/2018/12/linear-regression-to-boston-housing-dataset/>
2. <https://www.codeingschool.com/2019/04/multiple-linear-regression-how-it-works-python.html>
3. <https://towardsdatascience.com/linear-regression-on-boston-housing-dataset-f409b7e4a155>

## Introduction
Linear Regression is one of the fundamental machine learning techniques in data science. It makes predictions by discovering the best fit line that reaches the most points. Once it learns, it can start to predict prices, weight, and more. In this project, "Used Linear Regression to Model and Predict Housing Prices with the Classic Boston Housing Dataset," I will run through the steps to create a linear regression model using appropriate features, data, and analyze my results.


## Load Libraries & Dataset


```python
# Libraries . I will also import them again when I run the related code
import pandas as pd
import seaborn as sns
import numpy as np
from sklearn.model_selection import train_test_split
from sklearn.linear_model import LinearRegression
from sklearn.metrics import mean_squared_error

#Load Dataset from sklearn
from sklearn.datasets import load_boston


# Load Data
boston = load_boston()

# Data is in dictionary, Populate dataframe with data key
df = pd.DataFrame(boston.data)


# Columns are indexed, Fill in Column names with feature_names key
df.columns = boston.feature_names

# We need Median Value! boston.data contains only the features, no price value.

df['MEDV'] = pd.DataFrame(boston.target)

df.shape # Our dataset contains 506 data points and 14 columns

# Here is a glimpse of our data first 3 rows
df.head(3)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>CRIM</th>
      <th>ZN</th>
      <th>INDUS</th>
      <th>CHAS</th>
      <th>NOX</th>
      <th>RM</th>
      <th>AGE</th>
      <th>DIS</th>
      <th>RAD</th>
      <th>TAX</th>
      <th>PTRATIO</th>
      <th>B</th>
      <th>LSTAT</th>
      <th>MEDV</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.00632</td>
      <td>18.0</td>
      <td>2.31</td>
      <td>0.0</td>
      <td>0.538</td>
      <td>6.575</td>
      <td>65.2</td>
      <td>4.0900</td>
      <td>1.0</td>
      <td>296.0</td>
      <td>15.3</td>
      <td>396.90</td>
      <td>4.98</td>
      <td>24.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.02731</td>
      <td>0.0</td>
      <td>7.07</td>
      <td>0.0</td>
      <td>0.469</td>
      <td>6.421</td>
      <td>78.9</td>
      <td>4.9671</td>
      <td>2.0</td>
      <td>242.0</td>
      <td>17.8</td>
      <td>396.90</td>
      <td>9.14</td>
      <td>21.6</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.02729</td>
      <td>0.0</td>
      <td>7.07</td>
      <td>0.0</td>
      <td>0.469</td>
      <td>7.185</td>
      <td>61.1</td>
      <td>4.9671</td>
      <td>2.0</td>
      <td>242.0</td>
      <td>17.8</td>
      <td>392.83</td>
      <td>4.03</td>
      <td>34.7</td>
    </tr>
  </tbody>
</table>
</div>



### Our Features
Below are the definitions of each feature name in the housing dataset. We will be focused on using Median Value of homes in $1000s (MEDV) as our target variable. I was able to get this data with ***print(boston.DESCR)***

*Attribute Information (in order):*
        - CRIM     per capita crime rate by town
        - ZN       proportion of residential land zoned for lots over 25,000 sq.ft.
        - INDUS    proportion of non-retail business acres per town
        - CHAS     Charles River dummy variable (= 1 if tract bounds river; 0 otherwise)
        - NOX      nitric oxides concentration (parts per 10 million)
        - RM       average number of rooms per dwelling
        - AGE      proportion of owner-occupied units built prior to 1940
        - DIS      weighted distances to five Boston employment centres
        - RAD      index of accessibility to radial highways
        - TAX      full-value property-tax rate per \$10,000
        - PTRATIO  pupil-teacher ratio by town
        - B        1000(Bk - 0.63)^2 where Bk is the proportion of blacks by town (dataset created in 1979, questionable attribute. Will leave in for the purposes of following the project)
        - LSTAT    % lower status of the population
        - MEDV     Median value of owner-occupied homes in $1000's


## Prepare Dataset for Modeling

### Missing Data

Let's check if we have any missing values.


```python
df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 506 entries, 0 to 505
    Data columns (total 14 columns):
    CRIM       506 non-null float64
    ZN         506 non-null float64
    INDUS      506 non-null float64
    CHAS       506 non-null float64
    NOX        506 non-null float64
    RM         506 non-null float64
    AGE        506 non-null float64
    DIS        506 non-null float64
    RAD        506 non-null float64
    TAX        506 non-null float64
    PTRATIO    506 non-null float64
    B          506 non-null float64
    LSTAT      506 non-null float64
    MEDV       506 non-null float64
    dtypes: float64(14)
    memory usage: 55.4 KB


It doesn't show null values but when we look at df.head() from above, we can see that there are values of 0 which can also be missing values.<br> For good measure, we'll turn the 0 values into np.nan where we can see what is missing.<br><br>
The author from WeirdGeek.com made a good point to check what percentage of missing values exist in the columns and mentioned a rule of thumb to drop columns that are missing 70-75% of their data. If it consists of 20-25%, then there may be some hope and opportunity to finagle with filling the values in.


```python
# First replace the 0 values with np.nan values
df.replace(0, np.nan, inplace=True)
```


```python
# Check what percentage of each column's data is missing
df.isnull().sum()/len(df)
```




    CRIM       0.000000
    ZN         0.735178
    INDUS      0.000000
    CHAS       0.930830
    NOX        0.000000
    RM         0.000000
    AGE        0.000000
    DIS        0.000000
    RAD        0.000000
    TAX        0.000000
    PTRATIO    0.000000
    B          0.000000
    LSTAT      0.000000
    MEDV       0.000000
    dtype: float64



This shows that 73% of the ZN feature and 93% of CHAS feature are missing. We will leave them out of our variables to test as they do not give us enough information for our regression model to interpret.


```python
# Drop ZN and CHAS with too many missing columns
df = df.drop('ZN', axis=1)
df = df.drop('CHAS', axis=1)
```

### Check Multicollinearity

It's helpful to see which features increase/decrease together. Features that correlate together may make interpretability of their effectiveness difficult. An analogy that someone made on stackoverflow was that if you want to measure the strength of two people who are pushing the same boulder up a hill, it's hard to tell who is pushing at what rate. Another analogy was if two scientists contribute to a research report, and they are twins who work similarly, how can you tell who did what? A better situation would be if one scientist is good at creating experiments and the other one is good at writing the report--then you can tell how each scientist, or "feature" contributed to the report, or "target".


```python
import seaborn as sns

# Steps to remove redundant values
mask = np.zeros_like(df.corr())
mask[np.triu_indices_from(mask)] = True

# How to remove redundant correlation
# <https://stackoverflow.com/questions/33282368/plotting-a-2d-heatmap-with-matplotlib>

sns.set(rc={'figure.figsize': (8.5,8.5)})
sns.heatmap(df.corr().round(2), square=True, cmap='YlGnBu', annot=True, mask=mask);

# vmax emphasizes a color based on the gradient that you chose
# cmap is the color scheme of the heatmap
# square shapes the heatmap to a square for neatness
# annot shows the individual correlations of each pair of values
# mask removes redundacy and prevents repeat of the correlation values
```


![png](/images/boston-housing-data_files/boston-housing-data_16_0.png)


#### Analysis of Heatmap

From the heatmap, if I set a cut off for high correlation to be +- .75, I see that:
- *TAX* and *RAD* have a high correlation of **0.91**
- *NOX* and *INDUS* have a high correlation of **0.76**
- *DIS* and *NOX* have a high correlation of **-0.77**

I will drop all of these values for better accuracy.

I can also see that:
- *MEDV* has high correlation with *RM* and *LSTAT* **0.7 and -0.74**

*For Reference:*
- *MEDV* = Median value of house
- *RM* = Avg number of rooms per house
- *LSTAT* = Neighborhood Affluence




```python
# drop correlated values
columns = ['TAX', 'RAD', 'NOX', 'INDUS', 'DIS']
df = df.drop(columns=columns)
```

## Explore Dataset

I'm going to create a loop to plot each relationship between a feature and our target variable MEDV (Median Price). We'll be able to see which features have linear relationships.


```python
# Create multiple plots
features = df.drop('MEDV', 1).columns
target = df['MEDV']
plt.figure(figsize=(20,20))
for index, feature_name in enumerate(features):
    # 4 rows of plots, 13/3 == 4 plots per row, index+1 where the plot begins
    plt.subplot(4,len(features)/2, index+1)
    plt.scatter(df[feature_name], target)
    plt.title(feature_name, fontsize=15)
    plt.xlabel(feature_name, fontsize=8) #Removed for easier view of plots
    plt.ylabel('MEDV', fontsize=15)
```


![png](/images/boston-housing-data_files/boston-housing-data_22_0.png)


#### Linear Relationships
LSTAT and RM look like the only ones that have some sort of linear relationship. I would want to use these two features. As part of the assumptions of a linear regression, it is important because this model is trying to understand the linear relatinship between the feature and dependent variable. The model may underfit as a result of not checking this assumption. It underfits because if we draw a line through the data points in a non-linear relationship, the line would not be able to capture as much of the data.<br>

I can transform the non-linear relationship logging the values.


```python
import numpy as np
df["LOGLSTAT"] = df["LSTAT"].apply(np.log)
plt.figure(figsize=(20,10))

# showing plot 1
plt.subplot(1,2,1)
plt.scatter(df["LSTAT"], df['MEDV'], color='green')
plt.title('Original % Status of Neighborhood vs Median Price of House', fontsize= 20)
plt.xlabel('LSTAT',fontsize=20);
plt.ylabel('MEDV',fontsize=20);
plt.plot([0,40],[30,0])

# showing plot 2
plt.subplot(1,2,2)
plt.scatter(df["LOGLSTAT"], df['MEDV'], color='red')
plt.title('Log Transformed % Status of Neighborhood vs Median Price of House', fontsize= 20)
plt.xlabel('Transformed LSTAT',fontsize=20);
plt.ylabel('Transformed MEDV',fontsize=20);
plt.plot([0,4],[50,0])


#Apply global parameters
plt.rc('xtick', labelsize=20)
plt.rc('ytick', labelsize=20)

plt.show()
```


![png](/images/boston-housing-data_files/boston-housing-data_24_0.png)


In the left plot, I could not fit the data right through in one shot from corner to corner. I had to change where my line fits through to capture more data. After transformation, We were able to minimize the nonlinear relationship, it's better now.

## Create Model

First we create our list of features and our target variable. These are the values that we will train and test our values on. One author uses .values and another does not. Not sure what the difference is but I'd like to find out.


```python
X = df[['LOGLSTAT', 'RM']]
y = df.MEDV
```

Let's create our train test split data. We need the training set to teach our model about the true values and then we'll use what it learned to predict our prices.


```python
from sklearn.model_selection import train_test_split
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.33, random_state = 10)

#random_state 10 for consistent data to train/test

print(X_train.shape) # 339 rows 6 features
print(X_test.shape) # 167 rows 6 features
print(y_train.shape) # 339 rows 1 feature
print(y_test.shape) # 167 rows 1 feature
```

    (339, 2)
    (167, 2)
    (339,)
    (167,)


Now we instantiate a Linear Regression object, fit the training data and then predict.


```python
from sklearn.linear_model import LinearRegression
# Create LinearRegression Instance
lrm = LinearRegression()

# Fit data on to the model
lrm.fit(X_train, y_train)

# Predict
y_predicted = lrm.predict(X_test)

```

Let's evaluate how well our model did using metrics r-squared and root mean squared error (rmse). The r-squared value shows how strong our features determined the target value. The rmse defines the difference between predicted and the test values. The higher the value of the rmse, the less accurate the model.

## Evaluate Model with Metrics


```python
from sklearn.metrics import mean_squared_error
def linear_metrics():
    r2 = lrm.score(X_test, y_test)
    rmse = (np.sqrt(mean_squared_error(y_test, y_predicted)))
    print('r-squared: {}'.format(r2))
    print('---------------------------------------')
    print('root mean squared error: {}'.format(rmse))
linear_metrics()
```

    r-squared: 0.7155988559379332
    ---------------------------------------
    root mean squared error: 5.214330262011093


With an r-squared value of .72, the model is not terrible but it's not perfect. This could be improved by:

- bigger dataset
- better features or maybe more features
- remove outliers
- perform optimization techniques like Lasso and Ridge

The root mean squared error we can interpret that on average we are 5.2k dollars off the actual value.

### Actual Vs. Predicted Price Plot


```python
# Plot my predictions vs actual

plt.figure(figsize=(10,8))
plt.scatter(y_predicted, y_test)
plt.plot([0, 50], [0, 50], '--k')
plt.axis('tight')
plt.ylabel('The True Prices', fontsize=20);


plt.xlabel('My Predicted Prices', fontsize=20);
plt.title("Predicted Boston Housing Prices vs. Actual in $1000's", fontsize=20)

plt.rc('xtick', labelsize=15)
plt.rc('ytick', labelsize=15)

plt.show()
```


![png](/images/boston-housing-data_files/boston-housing-data_38_0.png)


## Plot Residual Errors

The closer we can get the points to be at the 0 line, the more accurate the model is at predicting the prices.


```python
#Create Residuals Function

def residuals():
    plt.style.use('fivethirtyeight')
    plt.figure(figsize=(6,4))
    plt.scatter(lrm.predict(X_test), lrm.predict(X_test) - y_test, s=30)
    plt.xlabel('')
    plt.title('Residual Errors of Test Data')
    plt.hlines(0, xmin=-10, xmax=50, linewidth=1);

# The closer to 1, the more perfect the prediction
print('Variance score: {}'.format(lrm.score(X_test, y_test)))


residuals()
```

    Variance score: 0.7155988559379332



![png](/images/boston-housing-data_files/boston-housing-data_41_1.png)


## Insight from Data


```python
# Combine coefficient with their value
coeff = list(zip(X, lrm.coef_))

# sort keys by value of coefficient
print(['y-intercept = {}'.format(lrm.intercept_), sorted(coeff, key = lambda x: x[1])])
```

    ['y-intercept = 25.77607508783125', [('LOGLSTAT', -9.968206663480515), ('RM', 3.23108538198644)]]


### Log Transformed Coefficient Understanding

- For every one percent increase in the independent variable, the dep. variable changes by: Coefficient * ln(1.01)
- ln(1.01) or ln(101/100) is also equal to just about 1%
- If you want to see a different percent increase, you can put ln(1.10) - a 10% increase

- log(coefficient) follows a log-normal distribution
- ln(coefficient) follows a normal distribution

<https://www.cscu.cornell.edu/news/statnews/stnews83.pdf><br>
<https://data.library.virginia.edu/interpreting-log-transformations-in-a-linear-model/><br>
(I want a better understanding of interpreting the log values)

### Analysis
- The y-intercept can be interpreted that in general the starting price of a house in Boston 1979 would be around 25K-26K.
- 'RM', or rooms per home, at 3.23 can be interpreted that for every room, the price increases by 3K.
- The Log Transformed 'LSTAT', % of lower status, can be interpreted as for every 1% increase of lower status, using the formula -9.96*ln(1.01), then our median value will decrease by 0.09, or by 100 dollars.


## Conclusion
In this project we went over the Boston dataset in extensive detail. Learning from other people's posts, I learned that although their steps were basically the same, they included and excluded different aspects of linear regression such as checking assumptions, log transforming data, visualizing residuals, provide some type of explanation for the results. This project was a combination of reading from other posts and customizing it to the way that I like it. I enjoyed working on this linear regression project, a fundamental part of machine learning, I've only reached tip of the iceberg as there are optimization techniques and other assumptions that I didn't include.

## Next Steps
I could check for all assumptions, as one author has posted an excellent explanation of how to check for them, <https://jeffmacaluso.github.io/post/LinearRegressionAssumptions/>. I would also play with Lasso and Ridge techniques especially if I have polynomial terms. Finally, I'd like to experiment with logging the dependent variable as well.
