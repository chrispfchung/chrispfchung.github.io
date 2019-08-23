---
title: "Used API calls to extract top articles from Hacker News (YCombinator)"

date: 2019-08-10

datatags: [API, blog data]
technologytags: [pandas, python]
techniquetags: [API calls]


categories: [Extract Data]
header:
  image: "/images/slanted-world.jpg"
excerpt: "Let's get the top stories with the most comments..."
mathjax: "true"
classes: wide #expand the page

---

{% include toc icon="book-open" title="Table of Contents" %}<br/>


## Project Description
Retrieved Hacker News' top articles and listed them by number of comments using API calls to extract data from a public source.

**Project Replicated From**:<br/>
Python Crash Course by Eric Matthes<br/>

```python
import requests

from operator import itemgetter
```

## Overview

Below is a sample of an API call for one article, "New Horizons: Nasa spacecraft speeds past Pluto." We have keys such as "descendents" that contains the number of comments, "kids" that represents the id of the items' comments and "url" that shows the articles URL.<br>
We will need these keys "descendents", "kids" and "url" to retrieve our articles.


```python
# Create Response text object that with 1 sample article
url = 'https://hacker-news.firebaseio.com/v0/item/9884165.json'
r = requests.get(url)
print({"Status Code": r.status_code}) # {'Status Code': 200}

r.text
```




    '{"by":"nns","descendants":297,"id":9884165,"kids":[9885099,9884723,9885165,9884789,9885604,9884137,9886151,9885220,9885790,9884661,9885844,9885029,9884817,9887342,9884545,9884372,9884499,9884881,9884109,9886496,9884342,9887832,9885023,9884334,9884707,9887008,9885348,9885131,9887539,9885880,9884196,9884640,9886534,9885152],"score":558,"time":1436875181,"title":"New Horizons: Nasa spacecraft speeds past Pluto","type":"story","url":"http://www.bbc.co.uk/news/science-environment-33524589"}'



Now to the URL we want to use. This new link contains the ids of the top stories on Hacker News. We'll access the top 30 articles. Again, we want to create a response text object which we will be interfacing.

## Create Top Articles Dictionary


```python
# Create Response text object with new url
url = 'https://hacker-news.firebaseio.com/v0/topstories.json' # to obtain list of top story ids
r = requests.get(url)
# We're in the green for this request, 200 is a good status code.
print({"Status Code": r.status_code}) # 200
```

    {'Status Code': 200}



```python
# The full json contains a long list of 500 ids
print(len(r.json())) # 500
r.json()[0:5] # [20660634, 20661311, 20658739, 20651658, 20660403]
```

Each link has this format ('https://hacker-news.firebaseio.com/v0/item/9884165.json').
We will loop through 5 articles by constructing each URL and retrieving the information we want - [Title, Link and Number of Comments].


```python
article_ids = r.json() # author used submission as variable name, but articles made more sense to me
five_articles_dicts = [] # this dict will contain all of our articles

for article_id in article_ids[:6]:
    url = 'https://hacker-news.firebaseio.com/v0/item/' + str(article_id) + '.json' # plug in looped id
    article_r = requests.get(url) # get response for each individual article

    #author prints out article status code to ensure no failures with requests
    print(article_r.status_code)

    one_article = article_r.json()

    # Now let's add our info to our main dictionary

    five_articles_dict = {
        'Title': one_article['title'],
        'Discussion link': one_article['url'],
        'Comments': one_article['descendants']
    }

    five_articles_dicts.append(five_articles_dict)

five_articles_dicts
```

    200
    200
    200
    200
    200
    200





    [{'Title': 'IBM Stops Buybacks to Pay for Red Hat',
      'Discussion link': 'https://investorplace.com/2019/08/ibm-stops-buybacks-to-pay-for-red-hat/',
      'Comments': 1},
     {'Title': "A mathematician's way of converting miles to kilometers",
      'Discussion link': 'https://twitter.com/TamasGorbe/status/1158348261683605504',
      'Comments': 133},
     {'Title': 'I am a horse in the land of booleans',
      'Discussion link': 'https://iloveponies.github.io/120-hour-epic-sax-marathon/I-am-a-horse-in-the-land-of-booleans.html',
      'Comments': 17},
     {'Title': "What Does a Coder Do If They Can't Type?",
      'Discussion link': 'http://nsaphra.github.io/post/hands/',
      'Comments': 0},
     {'Title': 'DEF Con and Stack Overflow: What Our Traffic Says About Cybersecurity',
      'Discussion link': 'https://stackoverflow.blog/2019/08/08/def-con-stack-overflow-traffic-data-trends/?cb=1',
      'Comments': 9},
     {'Title': 'Ted Chiang: Realist of a Larger Reality',
      'Discussion link': 'https://www.publicbooks.org/ted-chiang-realist-of-a-larger-reality/',
      'Comments': 5}]



## Sort Dictionary

Let's sort out our dictionary by Number of Comments. We utilize itemgetter from our operator module that we imported in the beginning.  <br>
According to documentation - "Return a callable object that fetches item from its operand using the operand’s __getitem__() method. If multiple items are specified, returns a tuple of lookup values" Since we need the number of comments, we will use the descendents value. <br>
We can sort the dictionary then use the itemgetter method to use number of comments as the key.


```python
sorted_dict = sorted(five_articles_dicts, key=itemgetter('Comments'), reverse=True) #'True' for highest to lowest
sorted_dict
```




    [{'Title': "A mathematician's way of converting miles to kilometers",
      'Discussion link': 'https://twitter.com/TamasGorbe/status/1158348261683605504',
      'Comments': 133},
     {'Title': 'I am a horse in the land of booleans',
      'Discussion link': 'https://iloveponies.github.io/120-hour-epic-sax-marathon/I-am-a-horse-in-the-land-of-booleans.html',
      'Comments': 17},
     {'Title': 'DEF Con and Stack Overflow: What Our Traffic Says About Cybersecurity',
      'Discussion link': 'https://stackoverflow.blog/2019/08/08/def-con-stack-overflow-traffic-data-trends/?cb=1',
      'Comments': 9},
     {'Title': 'Ted Chiang: Realist of a Larger Reality',
      'Discussion link': 'https://www.publicbooks.org/ted-chiang-realist-of-a-larger-reality/',
      'Comments': 5},
     {'Title': 'IBM Stops Buybacks to Pay for Red Hat',
      'Discussion link': 'https://investorplace.com/2019/08/ibm-stops-buybacks-to-pay-for-red-hat/',
      'Comments': 1},
     {'Title': "What Does a Coder Do If They Can't Type?",
      'Discussion link': 'http://nsaphra.github.io/post/hands/',
      'Comments': 0}]



Now let's print them out so that they look presentable out of json format.

## Final Output


```python
for article in sorted_dict:
    print('\nTitle:', article['Title']) # \n to add a break between each article's info
    print('Discussion Link:', article['Discussion link'])
    print('Comments:', article['Comments'])
```


    Title: A mathematician's way of converting miles to kilometers
    Discussion Link: https://twitter.com/TamasGorbe/status/1158348261683605504
    Comments: 133

    Title: I am a horse in the land of booleans
    Discussion Link: https://iloveponies.github.io/120-hour-epic-sax-marathon/I-am-a-horse-in-the-land-of-booleans.html
    Comments: 17

    Title: DEF Con and Stack Overflow: What Our Traffic Says About Cybersecurity
    Discussion Link: https://stackoverflow.blog/2019/08/08/def-con-stack-overflow-traffic-data-trends/?cb=1
    Comments: 9

    Title: Ted Chiang: Realist of a Larger Reality
    Discussion Link: https://www.publicbooks.org/ted-chiang-realist-of-a-larger-reality/
    Comments: 5

    Title: IBM Stops Buybacks to Pay for Red Hat
    Discussion Link: https://investorplace.com/2019/08/ibm-stops-buybacks-to-pay-for-red-hat/
    Comments: 1

    Title: What Does a Coder Do If They Can't Type?
    Discussion Link: http://nsaphra.github.io/post/hands/
    Comments: 0

**Resources Consulted:**
    1. Why use requests? <https://2.python-requests.org/en/master/user/quickstart/><br>
    2. What are the parameters of the HackerNews API? <br> <https://github.com/HackerNews/API><br>
    3. What is the purpose '\n'?<br> <https://teamtreehouse.com/community/why-does-the-print-syntax-append-a-n-at-the-end-of-a-line>

Chris Chung is a data scientist with a background in retail who focuses on solving problems in the video game industry. Specifically, Chris uses Python and SQL to solve problems and generate insights to improve player retention and scale data systems using Tableau, AWS and regression.
