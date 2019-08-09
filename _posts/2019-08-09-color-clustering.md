---
title: "Using K-means Clustering to Visualize Dominant Colors in an Image"

date: 2019-08-09
tags: [unlabeled, python, matplotlib, clustering, image, k-means]
header:
  image: "/images/k-means-color-cluster/bagofjellybeans.jpeg"
excerpt: "Has a teacher ever handed your class a bag of candy to count..."
mathjax: "true"
---
<br/>
## Project Description
Used k-means clustering to visualize dominant colors of an image whereas businesses can also group their unstructured data into insightful categories like in customer segmentation.<br/>

**Project Replicated From**:
 ["https://www.dataquest.io/blog/tutorial-colors-image-clustering-python/"]


## TL;DR <a name="tl;dr"></a>
Used k-means clustering to visualize dominant colors of an image whereas businesses can also group their unstructured data into insightful categories like in customer segmentation.

## Introduction <a name="introduction"></a>
Has a teacher ever handed your class a bag of candy to count out the number of each color? How would you have done this? K-means clustering is a machine learning technique that groups large amounts of data together based on their similarities. For this particular project, "Using K-means Clustering to Visualize Dominant Colors in an Image", I will identify and visualize the dominant colors of a web image.

## Basic of Images <a name="basic of images"></a>
```python
# Neat trick to print out everything at once, as seen in the next cell
from IPython.core.interactiveshell import InteractiveShell  
InteractiveShell.ast_node_interactivity = "all"

import matplotlib
matplotlib.__version__
#pillow to view imagess
import PIL
PIL.__version__
import scipy
scipy.__version__
```

    '3.0.2'

    '5.4.1'

    '1.1.0'



### JPG Images <a name="jpg images"></a>

A JPG image is made up of pixels and each pixel is made up of different values of three component colors, red, green and blue (RGB). In this project, we will work with Dataquest's logo in JPG format.

## Reading in an Image <a name="reading in an image"></a>

How do you read in the image? <br/>
First, using matplotlib's class image, input image.imread() to transform our logo into a matrix of RGB values.This matrix of RGB values represents every combination of color values of every pixel. Why? The computer reads in the RGB values and then is able to display it.<br/>
Our shape (200,200,3) represents width, height, and number of color lists (red + green + blue). 200x200 is a small image as we will see below.
<br/>
It returns an array with each pixels RGB values



```python
from matplotlib import image as img
img = img.imread('./dataquest.jpg')
img[0,0] # [255, 255, 255] : RGB values of 1 pixel
img.shape # (200, 200, 3)
```
    array([255, 255, 255], dtype=uint8)

    (200, 200, 3)

Building off the last cell, we will reuse our "img" variable that contains the color values of each pixel.


```python
%matplotlib inline
from matplotlib import pyplot as plt
plt.imshow(img)
```

![alt]({{ site.url }}{{ site.baseurl }}/images/k-means-color-cluster/k-means%20cluster_9_1.png)

Now before moving on to the clustering step. We have another step of processing. We need to create a list for each color that will contain every value of the color that shows up on each pixel. We should have 40,000 colors in each list because (200x200) means the area is 40,000 and each pixel contains red  green and blue values.
<br>
<br>
We will loop through each pixel in our logo which contains our 3 channel RGB values, and then loop through each RGB value within each pixel, identify it as a variable and then add them to their respective list.
<br>
Why are we doing this?
<br>
We need to know how dominant each color is on their value scale of 0 - 255. After getting


```python
r = []
g = []
b = []
for line in img:
    for pixel in line:
        rvalue, gvalue, bvalue = pixel
        r.append(rvalue)
        g.append(gvalue)
        b.append(bvalue)

r[39995:] # [255, 255, 255, 255, 255]
g[9950:9955] # [82, 82, 82, 82, 82]
b[0:5] # [255, 255, 255, 255, 255]
```

## Clustering Basics

Clustering is when you group items based on their similarities. First you determine how many clusters or groups your data should be in. If you determined 3 clusters, then three cluster centers will be plotted. That will be the first iteration. In the next iteration, each cluster center is assigned to their nearest data point. Then the distance is calculated and the center will readjust itself to be in the center of those points. This process is repeated until there are no more changes.

## Deciding # of Clusters <a name="number of clusters"></a>

Our next step is to determine how many clusters to group our colors into. By visualizing our data, we can see how many clusters we might need. We can also use this elbow method to determine how many clusters are optimal for the data that we have. In our case, after plotting out our data in a scatter plot, we have two areas where there are many more points suggesting that there are mainly two colors.


```python
%matplotlib inline
from mpl_toolkits.mplot3d.axes3d import Axes3D as axe
fig = plt.figure() # create figure class
ax = axe(fig) #use Axes3D class to render 3D space
ax.set_xlabel('$X = Red$')
ax.set_ylabel('$Y = Green$')
ax.set_zlabel('$Z = Blue$') # create axis labels
ax.scatter(r,g,b)
plt.show()

![alt]({{ site.url }}{{ site.baseurl }}/images/k-means-color-cluster/k-means%20cluster_16_4.png)

### K-means Clustering with SciPy

Let's look at our data easier by visualizing with a pandas dataframe.


```python
import pandas as pd
df = pd.DataFrame({'red': r, 'green': g, 'blue' : b})
df.sample(5)
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
      <th>red</th>
      <th>green</th>
      <th>blue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>20995</th>
      <td>77</td>
      <td>83</td>
      <td>99</td>
    </tr>
    <tr>
      <th>26694</th>
      <td>77</td>
      <td>86</td>
      <td>103</td>
    </tr>
    <tr>
      <th>36546</th>
      <td>69</td>
      <td>76</td>
      <td>94</td>
    </tr>
    <tr>
      <th>19181</th>
      <td>75</td>
      <td>82</td>
      <td>101</td>
    </tr>
    <tr>
      <th>6253</th>
      <td>75</td>
      <td>82</td>
      <td>101</td>
    </tr>
  </tbody>
</table>
</div>



## 3 Steps to Clustering
<b>
1. Standarize data<b>
<br>
2. Create cluster centers
<br>
3. Generate cluster labels for each data point (in our case we will only be identifying dominant colors)

### 1. Standardize Data <a name="standardize data"></a>
You want the scale of the data even. 255,255,255 for one pixel will affect our model more than a 60,60,60 pixel. Putting the pixels on an equal scale ensures accurate results. We will use the **whiten( )** method. Mathematically we divide each data point by its standard deviation.


```python
import numpy as np
np.std(r)
np.std(g)
np.std(b) # divide each color value by its respective standard deviation to get scaled value

255 / np.std(r)
255 / np.std(g)
255 / np.std(b) # scaled values match whiten() outputs below
```
    83.11570119050913

    80.44125788486512

    71.02506887282827






    3.0680123772945818

    3.1700150731727654

    3.5902816293861304

```python
# imported different classes than the guide as his were not working
# import libraries
# vq = vector quantization
# whiten = our standardizer class
from scipy.cluster.vq import vq, kmeans, whiten
df['scaled_r'] = whiten(r)
df['scaled_g'] = whiten(g)
df['scaled_b'] = whiten(b) # pretty straightforward code, whiten standarizes the values
df.sample(3) #to see a variety of values
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
      <th>red</th>
      <th>green</th>
      <th>blue</th>
      <th>scaled_r</th>
      <th>scaled_g</th>
      <th>scaled_b</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>34360</th>
      <td>73</td>
      <td>81</td>
      <td>100</td>
      <td>0.878294</td>
      <td>1.006946</td>
      <td>1.407954</td>
    </tr>
    <tr>
      <th>22878</th>
      <td>73</td>
      <td>76</td>
      <td>91</td>
      <td>0.878294</td>
      <td>0.944789</td>
      <td>1.281238</td>
    </tr>
    <tr>
      <th>1351</th>
      <td>255</td>
      <td>255</td>
      <td>255</td>
      <td>3.068012</td>
      <td>3.170015</td>
      <td>3.590282</td>
    </tr>
  </tbody>
</table>
</div>



### 2. Create Cluster Centers



```python
from scipy.cluster.vq import vq, kmeans, whiten
cluster_centers, distortion = kmeans(df[['scaled_r','scaled_g', 'scaled_b']], 2) # parameters our data and number of clusters
```


```python
cluster_centers
distortion # the author did not go indepth with distortion. My understanding is that the lower the distortion is, the better the clustering was.
```




    array([[2.94846993, 3.12453287, 3.52607825],
           [0.91844555, 1.05218697, 1.44734558]])






    0.15382134439326323



In our array, we have two sets that represent our cluster centers. Each center represents the color data points that were closest to them. We are almost there!


```python
print(cluster_centers) # our cluster centers
```

    [[2.94846993 3.12453287 3.52607825]
     [0.91844555 1.05218697 1.44734558]]


### 3. Display Dominant Colors

We will need to take our centroid_center values and multiply them by their standard deviation to essentially reverse standardize them so that they mean something to us.


```python
# standard deviations of each color
df[['red','green','blue']].std()
std_r, std_g, std_b = df[['red','green','blue']].std()
```




    red      83.116740
    green    80.442263
    blue     71.025957
    dtype: float64



After getting our color values, we need to render it in a format where we can display the image using imshow( ).<br/>
The output will be divided by 255 so that the imshow( ) method can read the values. If you plot the values then you will get 6 different colors. By putting the colors list into its own list, the centroid_centers are separated finally outputting two colors. I have displayed both colors and \[colors] below.


```python
colors = []
for center in cluster_centers:
    center
    scaled_r, scaled_g, scaled_b = center
    colors.append((
    scaled_r * std_r / 255,
    scaled_g * std_g / 255,
    scaled_b * std_b / 255)) #careful to make sure the append stays within the loop
```




    array([2.94846993, 3.12453287, 3.52607825])






    array([0.91844555, 1.05218697, 1.44734558])




```python
colors
[colors] #imshow reads in dimension of 1
```


    [(0.9610478786610961, 0.9856646918396453, 0.9821297287115202),
     (0.29936548922183137, 0.3319227505937144, 0.40313374434676263)]


    [[(0.9610478786610961, 0.9856646918396453, 0.9821297287115202),
      (0.29936548922183137, 0.3319227505937144, 0.40313374434676263)]]

```python
plt.imshow(colors)
```



![alt]({{ site.url }}{{ site.baseurl }}/images/k-means-color-cluster/k-means%20cluster_35_1.png)

## Final Output
And finally, the two most dominant colors in the image.


![alt]({{ site.url }}{{ site.baseurl }}/images/k-means-color-cluster/k-means%20cluster_5_6.png)

## Conclusion
In this project we overviewed what an image was made of. We referred to the red green blue values often throughout the project. The higher the value up to 255, the stronger the color was present in the pixel. We used matplotlib to read and show the image. We saw how the RGB values looked in a 3D plot and used this information to determine how many clusters to use. Finally we scaled the image values and transformed them in ways that helped us interpret their meaning and use in our k-means algorithm.<br/>

I enjoyed doing this project because I could see if I was off or not. I also underestimated the amount of time that it took to finish this tutorial. I wanted to understand the material as it was a good lesson to dig in and keep myself from skimming. Some of the author's code formatting was off so it was enjoyable to dig in to the documentation and find out what worked out. I also consulted wonderful stack overflow to resolve any of my confusions. Going forward I want to keep track of the sources that I used so that I can always refer back to them - great learning material.<br/>

1. How to convert jupyter notebooks into markdown format: [https://ipython.org/ipython-doc/dev/notebook/nbconvert.html]<br/>

In the future I want to try using a different image like a bag of gumballs with the actual counts of each color so that I can cross-reference my answers. I would also try a different image format. Finally, I would use the elbow method to determine the optimal number of clusters to use.<br/>

Chris Chung is a data scientist with a background in retail who focuses on solving problems in the video game industry. Specifically, Chris uses Python and Spark to solve problems and generate insights to improve player retention and scale data systems using Tableau, AWS and regression.

{% include toc icon="cog" title="Table of Contents" toc sticky="true" %}
