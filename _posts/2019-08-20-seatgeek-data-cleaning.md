---
title: "Dove Into SeatGeek New York Concert Dataset To Clean Data For Future Visualization and Analysis"

date: 2019-08-16

datatags: [concert data, labeled data]
technologytags: [pandas, python]
techniquetags: [data cleanse]

categories: [Tidy Data]
header:
  image: "/images/seatgeek-data-cleaning/concert-header.jpg"
excerpt: "It's a cliché that data cleaning takes 85% ..."
mathjax: "true"
classes: wide #expand the page
---

{% include toc icon="book-open" title="Table of Contents" %}<br/>

## Project Description
Cleaning concert data from SeatGeek that were previously extracted using a data pipeline in my previous project. We will use pandas to examine what data is available, deal with missing values and tidy up formatting. This dataset may be used in several projects including but not limited to creating interactive dashboards showing upcoming events for someone trying to sell their tickets or see what events are available and k-means clustering to group the concerts by category. I'm curious to find out what unsupervised learning will discover!

## Introduction
It's a cliché that data cleaning takes 85% or so of a data scientists time. In this project, "I want to have multiple purposes for this data. 1) for an interactive dashboard and 2) machine learning if possible. I can get super ambitious, unrealistic at times, but would love to eventually see this dataset come to life!

## Load Libraries and Dataset


```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import json
```


```python
# Loading json files into dataframes. See Previous project where data came from
df = pd.read_json('ny-concerts.json')
```

## Missing Data


```python
# Check how much data we have
df.shape # 2779 rows and 19 columns

df = df.replace('', np.nan) # performer_genre missing was set to '' in last project
df.isna().sum() # find out categories are missing data

# 982 tickets missing price / 725 missing performer genres / 3 missing venue zipcodes
```




    announce_date             0
    average_price           982
    date&time_event           0
    event_title               0
    highest_price           982
    lowest_price            982
    median_price            982
    performer_genre         725
    performer_name            0
    ticket_listing_count    982
    type_event                0
    upcoming_events?          0
    url                       0
    venue_capacity            0
    venue_city                0
    venue_name                0
    venue_score               0
    venue_zipcode             3
    visible_until_utc         0
    dtype: int64



With 2779 data points, my game plan is to keep as many rows as I can. It's a lot of data to lose if I remove them! <br><br>
Let's try to see why the info is missing and maybe I can find a solution to fill them in starting with prices.

### Missing Prices

I think there are 3 reasons why there is missing ticket price info.
1. Ticket sales have ended
2. Tickets are not on sale yet
3. No one is selling their tickets


```python
df[df['average_price'].isna()].sample(3) # My reasons based on looking at 'date&time_event' column
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
      <th>announce_date</th>
      <th>average_price</th>
      <th>date&amp;time_event</th>
      <th>event_title</th>
      <th>highest_price</th>
      <th>lowest_price</th>
      <th>median_price</th>
      <th>performer_genre</th>
      <th>performer_name</th>
      <th>ticket_listing_count</th>
      <th>type_event</th>
      <th>upcoming_events?</th>
      <th>url</th>
      <th>venue_capacity</th>
      <th>venue_city</th>
      <th>venue_name</th>
      <th>venue_score</th>
      <th>venue_zipcode</th>
      <th>visible_until_utc</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1963</th>
      <td>2019-07-24T00:00:00</td>
      <td>NaN</td>
      <td>2019-10-25T20:00:00</td>
      <td>Nightfall</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Nightfall</td>
      <td>NaN</td>
      <td>concert</td>
      <td>True</td>
      <td>https://seatgeek.com/nightfall-tickets/brookly...</td>
      <td>0</td>
      <td>Brooklyn</td>
      <td>Green-Wood</td>
      <td>0.000000</td>
      <td>11232</td>
      <td>2019-10-26T04:00:00</td>
    </tr>
    <tr>
      <th>1251</th>
      <td>2019-06-10T00:00:00</td>
      <td>NaN</td>
      <td>2019-09-25T20:00:00</td>
      <td>AbbeyRoad50</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>AbbeyRoad50</td>
      <td>NaN</td>
      <td>concert</td>
      <td>True</td>
      <td>https://seatgeek.com/abbeyroad50-tickets/new-y...</td>
      <td>0</td>
      <td>New York</td>
      <td>Rockwood Music Hall - Stage 2</td>
      <td>0.000000</td>
      <td>10002</td>
      <td>2019-09-26T04:00:00</td>
    </tr>
    <tr>
      <th>794</th>
      <td>2019-07-24T00:00:00</td>
      <td>NaN</td>
      <td>2019-09-11T20:30:00</td>
      <td>The Unlikely Candidates (18+)</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Pop</td>
      <td>The Unlikely Candidates</td>
      <td>NaN</td>
      <td>concert</td>
      <td>True</td>
      <td>https://seatgeek.com/the-unlikely-candidates-1...</td>
      <td>250</td>
      <td>Brooklyn</td>
      <td>Rough Trade NYC</td>
      <td>0.513713</td>
      <td>11249</td>
      <td>2019-09-12T04:30:00</td>
    </tr>
  </tbody>
</table>
</div>



Upon further investigation getting a description of the data, I might want to use the median values of average_price and median_price columns because although it isn't the true value, it won't affect the distribution too much. Most of the data is between 25 and 189. There's also an outlier for $34003 in average_price so I'm curious what that is.


```python
df.average_price.describe()
None
# number of priced tickets = 1797 out of 2779
# lowest price = 25
# highest price 340003
# 25-75% of data is between 25 and 189
```

*Let's investigate the events with ticket prices over 1000. I show only the first 5. Remove head() to see the rest.*<br>
1. They consist mostly of Madonna concerts for different dates. The highest priced tickets soar over 20000.
2. Some of the other concerts' average prices are under 100 yet the highest ticket is way over 1000.
3. There's about 200 high priced events

If I replaced the average_price and median_price of tickets with the median, I think they will fit right in.


```python
df[df['highest_price'] > 1000].head(3) # first 5
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
      <th>announce_date</th>
      <th>average_price</th>
      <th>date&amp;time_event</th>
      <th>event_title</th>
      <th>highest_price</th>
      <th>lowest_price</th>
      <th>median_price</th>
      <th>performer_genre</th>
      <th>performer_name</th>
      <th>ticket_listing_count</th>
      <th>type_event</th>
      <th>upcoming_events?</th>
      <th>url</th>
      <th>venue_capacity</th>
      <th>venue_city</th>
      <th>venue_name</th>
      <th>venue_score</th>
      <th>venue_zipcode</th>
      <th>visible_until_utc</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>30</th>
      <td>2019-05-30T00:00:00</td>
      <td>294.0</td>
      <td>2019-08-16T20:00:00</td>
      <td>Pink</td>
      <td>1889.0</td>
      <td>79.0</td>
      <td>250.0</td>
      <td>Pop</td>
      <td>Pink</td>
      <td>564.0</td>
      <td>concert</td>
      <td>True</td>
      <td>https://seatgeek.com/pink-tickets/uniondale-ne...</td>
      <td>16234</td>
      <td>Uniondale</td>
      <td>Nassau Veterans Memorial Coliseum</td>
      <td>0.766856</td>
      <td>11553</td>
      <td>2019-08-17T04:00:00</td>
    </tr>
    <tr>
      <th>75</th>
      <td>2019-02-23T00:00:00</td>
      <td>430.0</td>
      <td>2019-08-17T19:00:00</td>
      <td>Santana with The Doobie Brothers</td>
      <td>6353.0</td>
      <td>107.0</td>
      <td>266.0</td>
      <td>Country</td>
      <td>The Doobie Brothers</td>
      <td>95.0</td>
      <td>concert</td>
      <td>True</td>
      <td>https://seatgeek.com/santana-with-the-doobie-b...</td>
      <td>15000</td>
      <td>Bethel</td>
      <td>Bethel Woods Center for the Arts</td>
      <td>0.700694</td>
      <td>12720</td>
      <td>2019-08-18T03:00:00</td>
    </tr>
    <tr>
      <th>177</th>
      <td>2018-11-27T00:00:00</td>
      <td>249.0</td>
      <td>2019-08-20T19:30:00</td>
      <td>KISS</td>
      <td>5549.0</td>
      <td>54.0</td>
      <td>147.0</td>
      <td>Pop</td>
      <td>KISS</td>
      <td>679.0</td>
      <td>concert</td>
      <td>True</td>
      <td>https://seatgeek.com/kiss-tickets/brooklyn-new...</td>
      <td>19000</td>
      <td>Brooklyn</td>
      <td>Barclays Center</td>
      <td>0.830777</td>
      <td>11217</td>
      <td>2019-08-21T03:30:00</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Fill in average and median price with median prices
df['average_price'].fillna(df['average_price'].median(), inplace=True)
df['median_price'].fillna(df['median_price'].median(), inplace=True)
```

Since I plan on visualizing this data, I'm going to leave highest price and lowest price missing because I want to keep that price as close as possible for people like myself to see the lowest and highest prices as they are.

<div class="alert alert-block alert-info">
            <b>Fun fact:</b> The highest priced event is listed at $214749. Can you guess what artist? Hint: It's a pop artist. </div>

### Unavailable Venue Score

After listing out the value_counts for venue_score,  891 have a score of 0. That's about 30% of the data. As per the SeatGeek API documentation, the events "are based on estimated sales volume on the secondary ticket market (normalized such that the most popular document has a score of 1)." Since we had a lot of data without ticket prices, as I did with the prices, I'm going to fill them in with the median score.


```python
df.venue_score.median()
```




    0.46814686




```python
df['venue_score'].replace(0, df.venue_score.median(), inplace=True)
```

It's important to read the documentation when you're getting stuck. Looking at "to_replace" parameter let me see what data I could put in there.
<https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.replace.html>

### Unavailable Venue Capacity

There's 1109 rows without a venue capacity. It could be interesting to see the venue price changes depending on size. Unless I go through the events one by one to find their capacity, I'm not sure how else to find this data(any suggestions would be great!). Perhaps I could build a web scraper that goes on the web and finds it for me. Perhaps for another project. For these reasons, I will leave venue capacity at 0.

### Missing Venue Zipcode


```python
df[df['venue_zipcode'].isna()]
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
      <th>announce_date</th>
      <th>average_price</th>
      <th>date&amp;time_event</th>
      <th>event_title</th>
      <th>highest_price</th>
      <th>lowest_price</th>
      <th>median_price</th>
      <th>performer_genre</th>
      <th>performer_name</th>
      <th>ticket_listing_count</th>
      <th>type_event</th>
      <th>upcoming_events?</th>
      <th>url</th>
      <th>venue_capacity</th>
      <th>venue_city</th>
      <th>venue_name</th>
      <th>venue_score</th>
      <th>venue_zipcode</th>
      <th>visible_until_utc</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>72</th>
      <td>2019-08-12T00:00:00</td>
      <td>118.0</td>
      <td>2019-08-17T18:00:00</td>
      <td>Bob DiGiovanna</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>98.0</td>
      <td>NaN</td>
      <td>Bob DiGiovanna</td>
      <td>NaN</td>
      <td>concert</td>
      <td>True</td>
      <td>https://seatgeek.com/bob-digiovanna-tickets/ki...</td>
      <td>0</td>
      <td>Kismet</td>
      <td>Surf's Out</td>
      <td>0.468147</td>
      <td>NaN</td>
      <td>2019-08-18T02:00:00</td>
    </tr>
    <tr>
      <th>1649</th>
      <td>2019-05-22T00:00:00</td>
      <td>118.0</td>
      <td>2019-10-11T20:00:00</td>
      <td>Ripe with Castlecomer</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>98.0</td>
      <td>NaN</td>
      <td>Ripe</td>
      <td>NaN</td>
      <td>concert</td>
      <td>True</td>
      <td>https://seatgeek.com/ripe-with-castlecomer-tic...</td>
      <td>0</td>
      <td>Albany</td>
      <td>Pearl St Pub</td>
      <td>0.468147</td>
      <td>NaN</td>
      <td>2019-10-12T04:00:00</td>
    </tr>
    <tr>
      <th>1731</th>
      <td>2019-04-05T00:00:00</td>
      <td>118.0</td>
      <td>2019-10-14T19:00:00</td>
      <td>The Broadway Musical- The Book Of Mormon</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>98.0</td>
      <td>NaN</td>
      <td>The Broadway Musical- The Book Of Mormon</td>
      <td>NaN</td>
      <td>concert</td>
      <td>True</td>
      <td>https://seatgeek.com/the-broadway-musical-the-...</td>
      <td>0</td>
      <td>New York</td>
      <td>New York 226 W 46th St New York, NY 10036</td>
      <td>0.468147</td>
      <td>NaN</td>
      <td>2019-10-15T03:00:00</td>
    </tr>
  </tbody>
</table>
</div>



There's only three events so I'm going to go through the venues' url and manually input the zip code. Easy.



```python
df.loc[df.index == 72, 'venue_zipcode'] = '12207' #googled venue
df.loc[df.index == 1649, 'venue_zipcode'] = '07712' #googled city
df.loc[df.index == 1731, 'venue_zipcode'] = '10036' # zipcode is in the venue name
```

Actually, I had to look up how to change the value of one row value. Thank you to<br>
<https://stackoverflow.com/questions/19226488/change-one-value-based-on-another-value-in-pandas>

### Missing Performer Genre

I've decided to make missing performer genre into it's own category by replacing it with string 'NaN'. I don't want to run into any errors while modeling. The other option would've been to manually find their genre off their website. That would take too much time for data not necessarily critical to the project.


```python
df.performer_genre.replace(np.NaN, 'NaN', inplace=True) #replace NaN values with string 'NaN'
```

### Missing Ticket Listing Count

I'm going to leave this as is because if there's no tickets on sale, I'm not going to change that.

### Confirm Complete


```python
# Highest, lowest price and ticket listing count still have missing values, that's intentional
# All other missing data have been handled
df.isna().sum()
None
```

## DateTime

### Convert String Date Columns into DateTime

I plan to use the DateTime column to pick and choose what day/time to use. Eventually I want to visualize the events based on time and day. Below I convert any date related columns into DateTime columns.


```python
# Check current datatype of value date columns
type(df['announce_date'][0]) # str data type

# `df.columns` to list out all columns to find remaining date columns
df.columns
date_columns = ['announce_date', 'date&time_event', 'visible_until_utc']

# Change all date_columns to datetime format using a loop
for i in date_columns:
    df[i] = pd.to_datetime(df[i])

type(df['announce_date'][0]) # pandas._libs.tslibs.timestamps.Timestamp
None
```

## Drop Any Uneccessary Column

I'm going to drop the 'Type_event' column because all the events are concerts.


```python
df[df['type_event'].str.contains('concert')]
df.drop(columns='type_event',inplace=True)
```

##  Final Output


```python
df.sample(3)
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
      <th>announce_date</th>
      <th>average_price</th>
      <th>date&amp;time_event</th>
      <th>event_title</th>
      <th>highest_price</th>
      <th>lowest_price</th>
      <th>median_price</th>
      <th>performer_genre</th>
      <th>performer_name</th>
      <th>ticket_listing_count</th>
      <th>upcoming_events?</th>
      <th>url</th>
      <th>venue_capacity</th>
      <th>venue_city</th>
      <th>venue_name</th>
      <th>venue_score</th>
      <th>venue_zipcode</th>
      <th>visible_until_utc</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1905</th>
      <td>2019-04-02</td>
      <td>378.0</td>
      <td>2019-10-22 19:30:00</td>
      <td>Ghost with Nothing More</td>
      <td>11079.0</td>
      <td>23.0</td>
      <td>121.0</td>
      <td>Pop</td>
      <td>Ghost</td>
      <td>180.0</td>
      <td>True</td>
      <td>https://seatgeek.com/ghost-with-nothing-more-t...</td>
      <td>8000</td>
      <td>Syracuse</td>
      <td>War Memorial At OnCenter</td>
      <td>0.579858</td>
      <td>13202</td>
      <td>2019-10-23 03:30:00</td>
    </tr>
    <tr>
      <th>657</th>
      <td>2019-04-04</td>
      <td>67.0</td>
      <td>2019-09-06 20:00:00</td>
      <td>Blackberry Smoke with Simo (21+)</td>
      <td>106.0</td>
      <td>53.0</td>
      <td>64.0</td>
      <td>Country</td>
      <td>Blackberry Smoke</td>
      <td>25.0</td>
      <td>True</td>
      <td>https://seatgeek.com/blackberry-smoke-with-sim...</td>
      <td>600</td>
      <td>Brooklyn</td>
      <td>Brooklyn Bowl</td>
      <td>0.527452</td>
      <td>11201</td>
      <td>2019-09-07 04:00:00</td>
    </tr>
    <tr>
      <th>1903</th>
      <td>2019-06-03</td>
      <td>176.0</td>
      <td>2019-10-22 19:00:00</td>
      <td>The California Honeydrops</td>
      <td>387.0</td>
      <td>49.0</td>
      <td>156.0</td>
      <td>Blues</td>
      <td>The California Honeydrops</td>
      <td>12.0</td>
      <td>True</td>
      <td>https://seatgeek.com/the-california-honeydrops...</td>
      <td>200</td>
      <td>Pawling</td>
      <td>Daryl's House</td>
      <td>0.448582</td>
      <td>12564</td>
      <td>2019-10-23 03:00:00</td>
    </tr>
  </tbody>
</table>
</div>



## Conclusion

In this project, we used our ny-concert data from SeatGeek. We took stock of missing data and decided how we were going to deal with NaN prices and 0 value columns. We went through pricing and replaced the missing average and median prices with the median price of each respective column. We decided to leave the highest and lowest price columns as is because there were really no tickets available for those events. Because the venue_score was linked to ticket sales, I replaced the 0 value venue_scores with the median score in congruence with what we did with the ticket prices. I left venue_capacity as is as I did not have an optimal way of handling it. I also left ticket listing count at missing because there were no tickets on sale for those events. I handled missing zipcodes by googling them, easily because there were only 3. Then I made missing performer_genres in to its own category. I converted the string date columns into datetime. Finally we dropped a redundant column as all events were concerts.

It was tricky deciding what to do with missing values. At the back of my mind, it always occurred to me whether we put something there or not, the value is not 100% true. I think this is a limitation that I wonder if someone could tell me what would be the best way to handle a certain NaN value. Nonetheless, I tried to justify my reasoning for dealing with those values. In the future, I would like to use the datetime to get a look at what events occur during what time of the day.

Happy to get feedback on my project. Thank you.

<div class="alert alert-block alert-success"> The answer to the fun fact is Hozier. The average price for one of his tickets was $34003! </div>


```python

```
