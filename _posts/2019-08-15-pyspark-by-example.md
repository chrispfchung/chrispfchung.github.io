---
title: "Utilized the Spark DataFrame API to extract data from Chicago's Reported Crime Dataset (2001-2018)"

date: 2019-08-15
tags: [government data, data analysis, Spark, Google Colab]
categories: [Import Data]
header:
  image: "/images/pyspark-by-example/police-ban.png"
excerpt: "Let's get into the Chicago Reported Crime Dataset to find out..."
mathjax: "true"
classes: wide #expand the page

---

{% include toc icon="book-open" title="Table of Contents" %}<br/>

## Project Description
Discovered what percentage of reported crimes in Chicago resulted in an arrest and pinpointed the top three locations of reported crimes by working with the Spark DataFrame API to extract data from a large dataset.

## Introduction
Apache Spark allows you to store and extract data from huge datasets. In this project, "Utilized the Spark DataFrame API to extract data from Chicago's Reported Crime Dataset (2001-2018)", I follow the coursework of "Apache PySpark by Example" to answer 2 questions:

1. "What percentage of reported crimes resulted in an arrest?"<br>
2. "What are the top 3 locations for reported crimes?"<br>



## Download and install Spark

I work within Google Colab, setup Spark, download the dataset, and run my own query attempt vs the author's query to contrast and improve my approach.

```python
# Check current directory. Only sample_data folder initally show up until post-installing/downloading
!ls #sample_data
```

Install FindSpark files. Author has this code cell nicely laid out.


```python
# !apt-get update
# !apt-get install openjdk-8-jdk-headless -qq > /dev/null
# !wget -q http://archive.apache.org/dist/spark/spark-2.3.1/spark-2.3.1-bin-hadoop2.7.tgz
# !tar xf spark-2.3.1-bin-hadoop2.7.tgz
# !pip install -q findspark
```

## Setup environment

Setup Spark session, import PySpark. Author also has this code cell nicely laid out.


```python
import os
os.environ["JAVA_HOME"] = "/usr/lib/jvm/java-8-openjdk-amd64"
os.environ["SPARK_HOME"] = "/content/spark-2.3.1-bin-hadoop2.7"

import findspark
findspark.init()
from pyspark import SparkContext
sc = SparkContext.getOrCreate()

import pyspark
from pyspark.sql import SparkSession
spark = SparkSession.builder.getOrCreate()
spark
```

## Downloading and preprocessing Chicago's Reported Crime Data

Copy download link address of dataset from Chicago government site and paste after !wget.
The download took me a while. Previously, there were exercises showing how to work with columns, rows, and schemas. Problem is, the exercise files were located in different notebooks. Every time I uploaded another notebook, I had to redownload this massive file. From where I am, it took a while each time.


```python
# !wget https://data.cityofchicago.org/api/views/ijzp-q8t2/rows.csv?accessType=DOWNLOAD
# !ls -l
```

Rename file to something shorter and understandable


```python
# !mv rows.csv\?accessType\=DOWNLOAD reported-crimes.csv
# !ls -l
```

    total 1783140
    -rw-r--r-- 1 root root 1825926298 Aug 14 11:22 reported-crimes.csv
    drwxr-xr-x 1 root root       4096 Aug  2 16:06 sample_data


Steps to read in CSV and organize the data <br>
1. Read in CSV using spark.read.csv(filename).
2. .withColumn, change the formatting to time stamp and enter format
3. Filter by dates before November 12, 2018.

Filter dates until November 12th because so that the author and my results remain consistent.


```python
from pyspark.sql.functions import to_timestamp,col,lit
rc = spark.read.csv('reported-crimes.csv',header=True).withColumn('Date',to_timestamp(col('Date'),'MM/dd/yyyy hh:mm:ss a')).filter(col('Date') < lit('2018-11-12'))
rc.show(5)
```

## Challenge questions

### Question 1 - What percentage of reported crimes resulted in an arrest?

#### My Way

After returning all values ,  I can add the count values together. My formula looks like:
(Successful Arrests / Succ. Arrests + No Arrest) <br>
With numbers plugged in: **(1873664 + (1873664/4877955))**<br>

This is my way but I wonder if I can return a query that calculates the percentage for me.


```python
# First showed the Boolean count of Arrests
rc.groupBy('Arrest').count().show()
```

    +------+-------+
    |Arrest|  count|
    +------+-------+
    | false|4877955|
    |  true|1873664|
    +------+-------+



**My Answer: (1873664 + (1873664/4877955)) = 27.78%**

#### Author's Way


```python
# First what values Arrest is made up of. Author prefers to look at all possible values before querying.
rc.select('Arrest').distinct().show()
```

    +------+
    |Arrest|
    +------+
    | false|
    |  true|
    +------+




```python
# Print out schema to see what data type Arrest is. It's String type
rc.printSchema() # Thus, in the filter must be string too, 'true'. If boolean type, True
```

    root
     |-- ID: string (nullable = true)
     |-- Case Number: string (nullable = true)
     |-- Date: timestamp (nullable = true)
     |-- Block: string (nullable = true)
     |-- IUCR: string (nullable = true)
     |-- Primary Type: string (nullable = true)
     |-- Description: string (nullable = true)
     |-- Location Description: string (nullable = true)
     |-- Arrest: string (nullable = true)
     |-- Domestic: string (nullable = true)
     |-- Beat: string (nullable = true)
     |-- District: string (nullable = true)
     |-- Ward: string (nullable = true)
     |-- Community Area: string (nullable = true)
     |-- FBI Code: string (nullable = true)
     |-- X Coordinate: string (nullable = true)
     |-- Y Coordinate: string (nullable = true)
     |-- Year: string (nullable = true)
     |-- Updated On: string (nullable = true)
     |-- Latitude: string (nullable = true)
     |-- Longitude: string (nullable = true)
     |-- Location: string (nullable = true)
     |-- Historical Wards 2003-2015: string (nullable = true)
     |-- Zip Codes: string (nullable = true)
     |-- Community Areas: string (nullable = true)
     |-- Census Tracts: string (nullable = true)
     |-- Wards: string (nullable = true)
     |-- Boundaries - ZIP Codes: string (nullable = true)
     |-- Police Districts: string (nullable = true)
     |-- Police Beats: string (nullable = true)




```python
# Query all successful arrests and divde by total
rc.filter(col('Arrest') == 'true').count() / rc.select('Arrest').count()

```
    0.27751328977538575

**Answer: 27.8% of reported crimes in Chicago lead to a successful arrest**

###  Question 2 - What are the top 3 locations for reported crimes?

#### My Way

I want to group by Location and then count them. Then I order them by highest to lowest. Finally I show it within a dataframe.

My query returns coordinates and the top location is null. Why is it null?


```python
rc.groupBy('Location').count().orderBy('count', ascending=False).show(3)
```

    +--------------------+-----+
    |            Location|count|
    +--------------------+-----+
    |                null|63783|
    |(41.976290414, -8...|13114|
    |(41.754592961, -8...| 9434|
    +--------------------+-----+
    only showing top 3 rows



**My Answer: The top 3 locations for reported crimes in Chicago are...coordinates, one being null.**

Although coordinates is technically correct, a location in coordinates is not useful to me. After seeing the author's way, I realize that looking at Location Description will provide much more insightful info. I am also curious about why 63783 cases have a null location.

### Author's Way

First the author looks at all the possible locations and counts them.


```python
rc.groupby('Location Description').count().show()
```

    +--------------------+------+
    |Location Description| count|
    +--------------------+------+
    |   RAILROAD PROPERTY|    13|
    |AIRPORT TERMINAL ...|  1604|
    |EXPRESSWAY EMBANK...|     1|
    |POLICE FACILITY/V...| 17599|
    |               MOTEL|     5|
    |            SIDEWALK|665531|
    |AIRPORT TERMINAL ...|    77|
    |PUBLIC GRAMMAR SC...|     1|
    |CTA GARAGE / OTHE...|  9939|
    |            CAR WASH|  2775|
    |   TRUCKING TERMINAL|     1|
    |    AIRPORT/AIRCRAFT| 16108|
    |            HOSPITAL|     5|
    |MEDICAL/DENTAL OF...|  7101|
    |    FEDERAL BUILDING|   777|
    |             TRAILER|     3|
    |         CTA STATION|  3916|
    |SCHOOL, PUBLIC, G...| 28994|
    |SPORTS ARENA/STADIUM|  4999|
    |                FARM|     2|
    +--------------------+------+
    only showing top 20 rows



Then, he orders them by the top 3. Pretty self-explanatory. However, because I got the answer wrong, I learned that I should not take question prompts at face value, I should dig a bit further to see where the usable information is. In this case, 'Location' does provide a location but it gives coordinates. 'Location Description' shows what the location is.


```python
rc.groupby('Location Description').count().orderBy('count', ascending=False).show(3)
```

    +--------------------+-------+
    |Location Description|  count|
    +--------------------+-------+
    |              STREET|1770710|
    |           RESIDENCE|1144091|
    |           APARTMENT| 697980|
    +--------------------+-------+
    only showing top 3 rows



**Answer: The top 3 locations for reported crimes are on Streets, Residences, and Apartments**

## Final Answers

**What percentage of reported crimes resulted in an arrest?**
- 27.8% of reported crimes in Chicago lead to a successful arrest<br>

**What are the top 3 locations for reported crimes?**<br>
- *Streets, Residences, and Apartments*

## Conclusion
In this project, I installed PySPark and set up the environment. Then I used !wget and downloaded the 1.6 GB Chicago Crime Dataset from the government website. I used groupBy, count(), select() and orderBy to extract and sort my query results. Using show() at the end of my queries, I return a neat dataframe. Using select(), I return a row result which I could then use to perform calculations.

With the first question, I queried the dataframe and manually calculated the answer with my results. The author used select() to directly get the value and divide it by another select() query. Let the  computer do the calculations!

In the second question, I made the mistake of following too closely to the question and grouped by the 'Location' column when I needed to group to 'Location Description' in order to return understandable locations.

Chris Chung is a data scientist with a background in retail who focuses on solving problems in the video game industry. Specifically, Chris uses Python and SQL to solve problems and generate insights to improve player retention and scale data systems using Tableau, AWS and regression.
