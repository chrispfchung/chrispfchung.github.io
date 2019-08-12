---
title: "Created a detailed plot using Matplotlib to visualize sample sales data"

date: 2019-08-10
tags: [API, python, data extraction]
header:
  image: "/images/fireworks.gif"
excerpt: "We will visualize sample sales data using Matplotlib..."
mathjax: "true"
classes: wide #expand the page

---

{% include toc icon="book-open" title="Table of Contents" %}<br/>

## Project Description
Visualized companies' revenue using Matplotlib by showing how much different companies made using sample sales data

**Project Replicated From:**
1.<https://matplotlib.org/tutorials/introductory/lifecycle.html#sphx-glr-tutorials-introductory-lifecycle-py>
2. <https://pbpython.com/effective-matplotlib.html>

## Introduction
I looked for visualization projects to work on and saw that matplotlib's documentation contained this in-depth guide to creating a detailed plot. Visualizing data is one of the most important things in data science and I'd like to be very good at it. This guide will dive into Matplotlib, some Pandas, and more specifically help us nail down the differences between the *Axes* and *Figures* objects, customize plots, and do cool things like add a red dash line through the plot that could signify exceptional performance and combining plots. I will be following two main sources, the original blog post by Chris Moffitt and the transformed post-to-tutorial by Chris Holdgraf.

## Overview

The author makes it clear that understanding *Axes* and *Figure* instances are important to grasp the power of Matplotlib. *axes.Axes*, not to be confused with *Axis*, is an individual plot. *figure.Figure* is the final image rendered so that it can contain many plots. Holdgraf says,
>The Figure is like a canvas, and the Axes is a part of that canvas on which we will make a particular visualization

## Our Dataset

We'll create the sample data by reading in the original excel file from Moffitt (author of original post). Although we could plot many data points, we will work with the first 10 companies.


```python
import pandas as pd
import matplotlib.pyplot as plt
from matplotlib.ticker import FuncFormatter # to customize the little quantifier labels
import numpy as np # to calculate mean

# paste in given company to revenue data
data = {'Barton LLC': 109438.50,
        'Frami, Hills and Schmidt': 103569.59,
        'Fritsch, Russel and Anderson': 112214.71,
        'Jerde-Hilpert': 112591.43,
        'Keeling LLC': 100934.30,
        'Koepp Ltd': 103660.54,
        'Kulas Inc': 137351.96,
        'Trantow-Barrows': 123381.38,
        'White-Trantow': 135841.99,
        'Will LLC': 104437.60}

# group data by company
group_companies = list(data.keys()) # for our y-axis
group_sales = list(data.values()) # for our x-axis
group_mean = np.mean(group_sales) # we will use this to calculate our cut off line
```

## Plotting Data

First we will create our canvas and plot instance through matplotlib subplots. Our canvas is **figure** and our single plot is **axes**. We can create them in one line of code.


```python
fig, ax = plt.subplots()
```


![png](/images/lifecycle-of-a-plot_files/lifecycle-of-a-plot_10_0.png)


Once this is created, we can start plotting on top of it. We alter the plot by calling **ax** then the type of graph you want followed by your data in the parameters. If I input the alteration code, it will return an object. I need to call ```fig, ax = plt.subplots()``` within the same cell.


```python
ax.barh(group_companies, group_sales)
```




    <BarContainer object of 10 artists>




```python
fig, ax = plt.subplots()
ax.barh(group_companies, group_sales);
```


![png](/images/lifecycle-of-a-plot_files/lifecycle-of-a-plot_13_0.png)


<mark>You can remove the output text by adding a semicolon at the end of the code</mark>

## Customizing Plot

To access the list of styles to customize your plot:
Input ```plt.style.available```


```python
len(plt.style.available) # there are 26 different styles to choose from
plt.style.available[:5] # see first 5
```




    ['seaborn-dark',
     'seaborn-darkgrid',
     'seaborn-ticks',
     'fivethirtyeight',
     'seaborn-whitegrid']




```python
plt.style.use('fivethirtyeight')
```

After choosing my style, I will need to remake the plot by calling the code with fig, ax from before. Below I customized and commented my code.


```python
fig, ax = plt.subplots(figsize=(8,6)) # figsize(width, height)
ax.barh(group_companies, group_sales)
labels = ax.get_xticklabels() # create labels object
plt.setp(labels, rotation = 60, horizontalalignment='right') #set property an element on the figure
# horizontal alignment is which side of tick you want your label on
# you can customize labels
ax.set_xlabel('Total Revenue')
ax.set_ylabel('Company')

# you can also customize all at once with ax.set
ax.set(title='2014 Revenue', xlim=([-10000,140000])); #xlim space in beginning and cut extra space at end

```


![png](/images/lifecycle-of-a-plot_files/lifecycle-of-a-plot_20_0.png)


We can tighten up the figure by using rcParams. The 'r' stands for run and 'c' stands for config. With rcParams,
>You can control the defaults of almost every property in matplotlib: figure size and dpi, line width, color and style, axes, axis and grid properties, text and font properties and so on.


```python
plt.rcParams.update({'figure.autolayout': True})

fig, ax = plt.subplots()
ax.barh(group_companies, group_sales)
labels = ax.get_xticklabels()
plt.setp(labels, rotation = 60, horizontalalignment='right') #set property for anything on a figure
ax.set(title='2014 Revenue', xlim=([-10000,140000])); #xlim space in beginning and cut extra space at end
```


![png](/images/lifecycle-of-a-plot_files/lifecycle-of-a-plot_22_0.png)


## Customize Label
We will use our ticker.FuncFormatter we imported at the beginning to customize our labels in the form of functions. We create a function that will transform our label. Let's use this on your x-axis label to change revenue format. E.G. 60000 to 60K.

Looks like "f" represents the format of the number. Then you set the The digit before it is the amount of decimals to include. The number after the colon is the amount of space you want between the first '$' sign and the first digit. The colon is padding, the space between left and right side of your formatting. By default without any arrows signaling which direction to pad, it is left.

: = to format numbers <br>
f = to format float (our revenues contain decimals)<br>
1 = amount of space between '$' and 'K'<br>
0 = Number of digits to include after the decimal points

To change the value:<br>
x*1e-3 = snips the last 3 zeroes from the number<br>

```python
def currency(x, pos):
    return '${:1.0f}K'.format(x*1e-3)
```



```python
# create objects
fig, ax = plt.subplots(figsize=(8,6)) # figsize(width, height)
ax.barh(group_companies, group_sales)
labels = ax.get_xticklabels()
plt.setp(labels, rotation = 60, horizontalalignment='right') #set property an element on the figure
# horizontal alignment is which side of tick you want your label on
# you can customize labels
ax.set_xlabel('Total Revenue')
ax.set_ylabel('Company')

# you can also customize all at once with ax.set
ax.set(title='2014 Revenue', xlim=([-10000,140000])); #xlim space in beginning and cut extra space at end

# function
def currency(x, pos):
    return '${:1.0f}K'.format(x*1e-3)

#first create formatter variable
formatter = FuncFormatter(currency)
ax.xaxis.set_major_formatter(formatter) # set formatter currency function
```


![png](/images/lifecycle-of-a-plot_files/lifecycle-of-a-plot_25_0.png)


## Annotating Plot

Let's add a dashed red line representing the companies' average revenue.


```python
fig, ax = plt.subplots(figsize=(8,6)) # figsize(width, height)
ax.barh(group_companies, group_sales)
labels = ax.get_xticklabels()

# set labels and properties
plt.setp(labels, rotation = 60, horizontalalignment='right')
ax.set(title='2014 Revenue', xlim=[-10000,140000], xlabel='Total Revenue', ylabel='Company')

# formatter for currency
formatter = FuncFormatter(currency)
ax.xaxis.set_major_formatter(formatter) # set formatter currency function

# create red dash line showing average revenue
ax.axvline(group_mean, ls='--', color='r') #ls is line style, can be [':','-', and more]

# annotate graph to label 'new companies'
for group in [3,5,8]:
    ax.text(142000, group, "New Company", fontsize=12, verticalalignment='center')

#push up title position for space
ax.title.set(y=1.025)
```


![png](/images/lifecycle-of-a-plot/lifecycle-of-a-plot_files/lifecycle-of-a-plot_28_0.png)


## Save Plot


```python
# To see what type of files formats you can save in
print(fig.canvas.get_supported_filetypes())
```

    {'ps': 'Postscript', 'eps': 'Encapsulated Postscript', 'pdf': 'Portable Document Format', 'pgf': 'PGF code for LaTeX', 'png': 'Portable Network Graphics', 'raw': 'Raw RGBA bitmap', 'rgba': 'Raw RGBA bitmap', 'svg': 'Scalable Vector Graphics', 'svgz': 'Scalable Vector Graphics', 'jpg': 'Joint Photographic Experts Group', 'jpeg': 'Joint Photographic Experts Group', 'tif': 'Tagged Image File Format', 'tiff': 'Tagged Image File Format'}


#### Parameter overview
1. transparent boolean to set background transparent<br>
2. dpi controls resolution<br>
3. bbox_inches removes extra spacing around plot<br>
```python
fig.savefig('sales.png', transparent=False, dpi=80, bbox_inches="tight")
```

## Recall from Beginning


```python
# first create fig, ax instance
fig, ax = plt.subplots(figsize=(8,6))
# with ax to play with this one plot, we create our axes with our data
ax.barh(group_companies,group_sales)
# create labels
labels = ax.get_xticklabels() #ax.set_xticks() initially
# customize label property
plt.setp(labels, rotation = 45, horizontalalignment = 'right')
# label labels
ax.set(title='2014 Revenue', xlabel='Total Revenue', ylabel='Company', xlim=[-10000,
                                                                            140000]);
# create currency format function
def currency(x, pos):
    if x > 10000:
        return '${:1.0f}K'.format(x*1e-3)

# create formatter
formatter = FuncFormatter(currency)
ax.xaxis.set_major_formatter(formatter) # did not get this right off the bat

# create dash red line
ax.axvline(group_mean, ls = '--', color='r');

# Annotate New Company, make sure to input paramters in the correct order
for group in [3,5,8]:
    ax.text(145000, group, 'New Company', fontsize= 10, verticalalignment ='center')

# to bucket the x axis tick labels
ax.set_xticks([0, 25e3, 50e3, 75e3, 100e3, 125e3]);
```


![png](/images/lifecycle-of-a-plot_files/lifecycle-of-a-plot_33_0.png)


### Resources Consulted:
1. What is a tick? <https://www.geeksforgeeks.org/python-matplotlib-pyplot-ticks/>
2. How do you highlight text in markdown? <https://stackoverflow.com/questions/25104738/text-highlight-in-markdown>
3. What does the 'rc' stand for in rcparam? <https://stackoverflow.com/questions/37728087/rc-params-in-matplotlib-what-does-rc-stand-for>
4. How do I use .format()? <https://mkaz.blog/code/python-string-format-cookbook/>
5. Can you elaborate on formatting numbers? <https://pyformat.info/>

## Conclusion
We went over the fig and ax subplots. We work on each plot with ax and change the properties of all plots at once with fig. We read in our data and grouped them into our x/y axis. We labeled our x/y axis, titles, and set properties such as label format. We formatted the prices from long digits to a cleaner format. We added in another axes that represents the average mean of prices.  Finally, we saved our figure into PNG format.
