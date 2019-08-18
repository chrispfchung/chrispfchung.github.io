---
title: "Constructed Functions to Extract Live Concert Music Data from SeatGeek's API"

date: 2019-08-16
tags: [pandas, API, python, JSON, music data]
categories: [Store Data, Extract Data]
header:
  image: "/images/seatgeek-extract-data/live-concert-blue-bg-header.jpg"
excerpt: "It's always been interesting to me how people can utilize tools to retrieve massive amounts of data from the internet..."
mathjax: "true"
classes: wide #expand the page

---

{% include toc icon="book-open" title="Table of Contents" %}<br/>


## Project Description

Built a function that retrieves data from a public website for visualization and analysis by using SeatGeek's API to extract data of about 3000 upcoming music events in New York. I also import this data into a dataframe for easier access.

## Introduction

It's always been interesting to me how people can utilize tools to retrieve massive amounts of data from the internet. Many websites now have an API interface that allows you to access their data by following their API documentation. In this project, "Constructed Functions to Extract Live Concert Music Data from SeatGeek's API", I will utilize requests.get() to get access to their API and navigate event data within a nested JSON dictionary to extract specific information about upcoming music concert events. Then I will save it into a JSON file and finally a dataframe for future use.

## Import Libraries


```python
import urllib
import requests
import json
import pandas as pd
```

## Access API with requests.get(url)

We use a client ID to access their API. You can request your own with instructions at <https://platform.seatgeek.com/>


```python
# CONTROL CENTER / BASE URL LOCATION
# Testing
client_ID = 'MTUwODYzODJ8MTU0ODg3NjU2Mi4yOQ'
client_SECRET = 'aa2c.......'
url = 'https://api.seatgeek.com/2/events/?' + 'client_id=' + client_ID
r = requests.get(url)
# r.json()
```

## Overview of Event Data

First, I use the 'meta' key to look at how many events are available


```python
# Meta key shows quick stats, includes all types of seatgeek events (broadway, sports, festivals, concerts)
r.json()['meta'] #109754 results
```




    {'geolocation': None, 'per_page': 10, 'total': 109754, 'took': 7, 'page': 1}



Sample of what our data can return:


```python
#data in JSON format
data = r.json()

# Format - ['events'][event number]['data you want']

for i in range(3):
    data['events'][i]['title']

# Michel Simard
# West Virginia Black Bears at Batavia Muckdogs
# Mahoning Valley Scrappers at State College Spikes

for i in range(3):
    data['events'][i]['type']

# concert
# minor_league_baseball
# minor_league_baseball
```

## Looking at Data of One NY Concert


```python
# Look at NY concerts metadata
# Testing for 1 result to see what info is available
url = 'https://api.seatgeek.com/2/events?type=concert\
&venue.state=NY&client_id=' + client_ID + \
'&per_page=5&format=json'
r = requests.get(url)
events = r.json()
```


```python
events['meta'] # number of NY events: 2783
```




    {'geolocation': None, 'per_page': 5, 'total': 2783, 'took': 5, 'page': 1}



## Calling SeatGeek API for NY venue data for concerts

Let's focus only on New York concert data.

There's a ton of information that is nested into even more info! I list the the keys which you can navigate even further to get more details.


```python
# There's a long list of available data for one event! Let's get more specific.
list(events['events'][0].keys())
```

    ['links',
     'event_promotion',
     'conditional',
     'is_open',
     'id',
     'stats',
     'title',
     'announce_date',
     'score',
     'access_method',
     'announcements',
     'taxonomies',
     'type',
     'status',
     'description',
     'datetime_local',
     'visible_until_utc',
     'time_tbd',
     'date_tbd',
     'performers',
     'url',
     'created_at',
     'popularity',
     'venue',
     'enddatetime_utc',
     'short_title',
     'datetime_utc',
     'datetime_tbd']


### Venue Data

Looking at Venue information, we get location information like postal code which is great for locating neighborhoods, link to purchase the ticket and other info like score and capacity. Score is based on ticket sales and Capacity is the venue's occupancy.


```python
# Create new url string that focuses only on New York concerts.

url = 'https://api.seatgeek.com/2/events?type=concert\
&venue.state=NY&client_id=' + client_ID + \
'&per_page=5&format=json'
r = requests.get(url)
events = r.json()
events['meta'] # Number of NY concerts: 2812
events['events'][1]['venue']
```

    {'links': [],
     'metro_code': 538,
     'postal_code': '14424',
     'timezone': 'America/New_York',
     'has_upcoming_events': True,
     'id': 434155,
     'city': 'Canandaigua',
     'extended_address': 'Canandaigua, NY 14424',
     'display_location': 'Canandaigua, NY',
     'state': 'NY',
     'score': 0,
     'location': {'lat': 42.8451, 'lon': -77.2218},
     'access_method': None,
     'num_upcoming_events': 1,
     'address': '3792 New York 247',
     'capacity': 0,
     'slug': 'lincoln-hill-farms',
     'name': 'Lincoln Hill Farms',
     'url': 'https://seatgeek.com/venues/lincoln-hill-farms/tickets',
     'country': 'US',
     'popularity': 0,
     'name_v2': 'Lincoln Hill Farms'}

## Our NY Concert Data

*I use a large function to extract all the data that I want. The data includes:*

#### Event Information
1. Event Title
2. Type of Event
3. Date and Time of Event
4. Event Announcement Date
5. Event Visible Until Date

#### Performer Information
6. Performer Name
7. Performer Genre

#### Ticket Data
8. Ticket Prices - Average, Median, Lowest and Highest
9. Ticket Listing Count

#### Venue
10. Venue Name
11. Venue City
12. Venue Zipcode
13. Upcoming events (True/False)
14. Venue Score
15. Venue Capacity

#### Other
16. URL for event reference

*Within the function, I have included code to save our data into a json file called 'ny-concerts.json'.*


## Final API Function with Comments


```python
# If you alter the type_concert parameter, you can retrieve info for the other types of events
# All events pulled on 8/16/19

client_ID = 'MTUwODYzODJ8MTU0ODg3NjU2Mi4yOQ'
# client_SECRET = 'aa2c.......'

def ny_events(type_concert):

    # This is where all of our data will be at the end
    parsed_events= []

    # Range: since there are 2783 events, start from page 1 and loop 3 pages (1000 events each)
    for i in range(1, 4):
        url = 'https://api.seatgeek.com/2/events?type='+type_concert+\
        '&venue.state=NY&client_id=' + client_ID + \
        '&per_page=1000&format=json&page='+str(i) # Create URL again with looped page count
        r = requests.get(url)
        events = r.json()

        # Loop through events on each page
        for j in range(1, 1000):

            # 'stats' for ticket info, current events to extract data
            stats = events['events'][j]['stats']
            current_event = {}

            # Event Data
            current_event['event_title']= events['events'][j]['title']
            current_event['type_event']= events['events'][j]['type']
            current_event['date&time_event']= events['events'][j]['datetime_local']
            current_event['announce_date']= events['events'][j]['announce_date']
            current_event['visible_until_utc']= events['events'][j]['visible_until_utc']

            # Performer Data
            current_event['performer_name']= events['events'][j]['performers'][0]['name']
            if 'genres' not in events['events'][j]['performers'][0].keys():
                current_event['performer_genre'] = ''
            else:
                current_event['performer_genre'] = events['events'][j]['performers'][0]['genres'][0]['name']

            # Ticket Data
            current_event['ticket_listing_count']= stats['listing_count']
            current_event['average_price']= stats['average_price']
            current_event['median_price']= stats['median_price']
            current_event['lowest_price']= stats['lowest_price']
            current_event['highest_price']= stats['highest_price']

            # Venue Data
            current_event['venue_name']= events['events'][j]['venue']['name']
            current_event['venue_city']= events['events'][j]['venue']['city']
            current_event['venue_zipcode']= events['events'][j]['venue']['postal_code']
            current_event['upcoming_events?'] = events['events'][j]['venue']['has_upcoming_events']
            current_event['venue_score'] = events['events'][j]['venue']['score']
            current_event['venue_capacity'] = events['events'][j]['venue']['capacity']

            # Other
            current_event['url'] = events['events'][j]['url']

#             print(range(1,j)) , show the loop is working

            # Events stop at 783
            if i == 3 and j == 782:
                break

            # Add all of this data to our edited events
            parsed_events.append(current_event)

            # Save file
    with open('ny-concerts.json', 'w') as f:
        json.dump(parsed_events, f)
    return parsed_events
# ny_events('concert')  to run the function, it may take a few minutes
```

## Storing Data

Within the function above, I have included code to save the file into my directory. We will load it into our notebook and convert it into a dataframe to view our data easier.


```python
# First I load it into a variable called 'datastore'
with open('ny-concerts.json', 'r') as f:
    datastore = json.load(f)

# Then I use pandas to convert it
import pandas as pd
df = pd.DataFrame(datastore)
df.head()
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
      <th>0</th>
      <td>2019-08-14T00:00:00</td>
      <td>NaN</td>
      <td>2019-08-16T17:00:00</td>
      <td>Big Eyed Phish Live</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td></td>
      <td>Big Eyed Phish Live</td>
      <td>NaN</td>
      <td>concert</td>
      <td>True</td>
      <td>https://seatgeek.com/big-eyed-phish-live-ticke...</td>
      <td>0</td>
      <td>Canandaigua</td>
      <td>Lincoln Hill Farms</td>
      <td>0.0</td>
      <td>14424</td>
      <td>2019-08-17T01:00:00</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2019-04-20T00:00:00</td>
      <td>NaN</td>
      <td>2019-08-16T17:00:00</td>
      <td>George Fitzgerald</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Electronic</td>
      <td>George Fitzgerald</td>
      <td>NaN</td>
      <td>concert</td>
      <td>True</td>
      <td>https://seatgeek.com/george-fitzgerald-tickets...</td>
      <td>0</td>
      <td>Brooklyn</td>
      <td>Elsewhere</td>
      <td>0.0</td>
      <td>11237</td>
      <td>2019-08-17T01:00:00</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2019-08-13T00:00:00</td>
      <td>NaN</td>
      <td>2019-08-16T17:30:00</td>
      <td>The Birdland Big Band</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Blues</td>
      <td>The Birdland Big Band</td>
      <td>NaN</td>
      <td>concert</td>
      <td>True</td>
      <td>https://seatgeek.com/the-birdland-big-band-tic...</td>
      <td>0</td>
      <td>New York</td>
      <td>Birdland</td>
      <td>0.0</td>
      <td>10036</td>
      <td>2019-08-17T01:30:00</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2019-05-09T00:00:00</td>
      <td>NaN</td>
      <td>2019-08-16T18:00:00</td>
      <td>Gatsby</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td></td>
      <td>Gatsby</td>
      <td>NaN</td>
      <td>concert</td>
      <td>True</td>
      <td>https://seatgeek.com/gatsby-tickets/new-york-n...</td>
      <td>0</td>
      <td>New York</td>
      <td>Club Bonafide</td>
      <td>0.0</td>
      <td>10022</td>
      <td>2019-08-17T02:00:00</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2019-06-16T00:00:00</td>
      <td>NaN</td>
      <td>2019-08-16T18:00:00</td>
      <td>No Zodiac, Kaonashi, VCTMS</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td></td>
      <td>No Zodiac, Kaonashi, VCTMS</td>
      <td>NaN</td>
      <td>concert</td>
      <td>True</td>
      <td>https://seatgeek.com/no-zodiac-kaonashi-vctms-...</td>
      <td>0</td>
      <td>Brooklyn</td>
      <td>Gold Sounds Bar</td>
      <td>0.0</td>
      <td>11237</td>
      <td>2019-08-17T02:00:00</td>
    </tr>
  </tbody>
</table>
</div>


We have a lot of information that we may or may not need. I would rather take as much data as I can before finding out later that I need more. Also, the data here can be altered and used in many ways, such as changing date and time into evening or afternoon.

At first glance using df.head(), I see the first 5 events contain missing ticket info. When I visit the links, there are currently no tickets available. It might be because the event is about to happen and they're off the market. We can look into why and dealing with these missing values in another post!

## Conclusion
In this project, we accessed SeatGeek's API by following their documentation on <https://platform.seatgeek.com/>. We took a bird's eye view of all data and what's available for us to extract. Then we honed in on NY concert data and created a function that pulls all the information that I want. Finally we saved it into a json file and converted it into a dataframe for easier access.

Looking online for more info on saving JSON files, I reminded myself that many other people are also looking programming information up and--people are providing this information for us. This community is vast and I love how people are in different stages getting involved.
