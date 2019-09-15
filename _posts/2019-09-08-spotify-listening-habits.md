---
title: "Visualized My Spotify Listening Habits Over The Last 3 Months With Tableau"

date: 2019-09-06

datatags: [music data, labeled data]
technologytags: [tableau]
techniquetags: []

categories: [Visualize Data, Communication, Next Steps]
header:
  image: "/images/seatgeek-data-cleaning/concert-header.jpg"
excerpt: "I will learn about my Spotify listening habits.."
mathjax: "true"
classes: wide #expand the page
---
{% include toc icon="book-open" title="Table of Contents" %}<br/>

## Project Description
In this project, I am going to use Tableau to visualize my Spotify listening habits over the last 3 months and answer questions like who are my favorite tracks and artists, how many times I listened to Spotify, and who I binge listened to. Interact with the [final dashboard](https://public.tableau.com/views/MySpotifyListeningHabitsOver3MonthsUsingTableau/MainSpotifyDashboard?:embed=y&:display_count=yes&publish=yes&:origin=viz_share_link) here.

**Project Replicated From**
<https://public.tableau.com/en-us/s/blog/2019/07/visualize-your-listening-habits-lastfm-and-spotify-data>

I consulted this site to understand my downloaded Spotify data. This was important so I know what my data means: <https://support.spotify.com/us/account_payment_help/privacy/understanding-my-data/>

### These questions guided my analysis.

1. Who do I listen to the most, top 3 tracks top 3 artists?
2. Which albums do I have on repeat?
3. How many times do I listen to a song before stopping?
4. How often do I listen to Spotify?
5. Per day? Per month? Per year?

## Introduction
Creating visualizations is one of the best ways to understand your data. It allows you to quickly answer questions in a memorable format. I created many worksheets in Tableau and combined them into dashboards with some answering multiple questions and some answering one. I have the downloadable Tableau worksheet available. In this write up I will analyze my visualizations and provide some of my reasoning and calculations. You can see individual sheets and donwload the entire dashboard to work with  [here](https://public.tableau.com/profile/christopher.chung1603#!/vizhome/MySpotifyListeningHabitsOver3MonthsUsingTableau/MainSpotifyDashboard) on Tableau Public.



## Most Listened To Artists
Top 3 artists: Lena Raine, Bill Evans, and Rain. As we'll see in the next sheet, I listened to one of Lena Raine's songs nonstop for some period of time. For rank 2, I recently discovered Bill Evans and he's become one of my favorite jazz musicians. One of his albums, "Symbiosis" has become one of my favorites. I also recently picked up Matchbox Twenty and I LOVE them. When I was younger, they were always on the radio...and actually this past year I heard their song "3 AM" while at Dugan's, my favorite sandwich shop, and that's when I "discovered" them.

![]({{ site.url }}{{ site.baseurl }}/images/spotify-listening-habits/Dashboard1.png)

### Most Listened To Tracks
Top 3 Tracks: "Quiet and Falling" by Lena Raine, a Korean song by Rain, and "Mississippi" by Bob Dylan. I'm pretty surprised by the amount of Bob Dylan I had on repeat. For "Quiet and Falling" and "Missisippi", I remember there was a period of time this summer when I heard from multiple sources that listening to a song on repeat helped you get into a flow state better. Did it work? Sort of. As long as the music was able to provide some consistent background noise, then it worked. Otherwise it's a distraction as though you put any other song on.

## Days I Listened The Most
To get the top 5 days, I created a line chart that showed the amount of times I listened each day. I copied the 5 highest peaks and created a **Set** that I could used in a **Filter** so that I could separately select days. I then use this set to visualize each days total listens and each days top 5 artists.

![]({{ site.url }}{{ site.baseurl }}/images/spotify-listening-habits/Dashboard2.png)


## Artists On Repeat
This one was tricky because I never came across **previous(artist name)** before. The author's final product of this chart looked great and showed how many times I listened to artists on repeat...I was curious to find out! What he did was create a a new **Measure** and use this formula:
```
IF ATTR([Artist Name]) = LOOKUP(ATTR([Artist Name]),-1) then  
 PREVIOUS_VALUE(0) + 1
ELSE
 1
END
```
Let's dissect each part of this calculation.<br><br>

*ATTR* = This is attribute. In plain English, "It returns a value if it's unique, else it returns \*." Another definition is "Does every row have the same value? If so, value. If not, \*." If we use **ATTR([Artist Name])** in *Rows* and place **Track Name** next to it, we'll see a \* next to each track that has more than one artist value. In other words, if a song's name is used by 2 or more artists, making it not unique, then \*. Otherwise, you'll get the unique artist name that matches with the track. More than one artist for a song name, \*. If not, artist name.
Consulted: <https://interworks.com/blog/tcostello/2014/05/15/attr-tableaus-attribute-function-explained>

*LOOKUP* = This is lookup. "At its most basic, it returns a value from a different row in the partition." Create a calculated field with formula ```LOOKUP([Listens], -1)```, place **Month(Date Time)** next to it Rows and you get the number of listens of each previous month. The *-1* looks at the value before, in this case a month before.

So if we combine LOOKUP() with ATTR()
Consulted: <https://breaking-bi.blogspot.com/2013/04/using-lookup-function-in-tableau.html>


![]({{ site.url }}{{ site.baseurl }}/images/spotify-listening-habits/Dashboard3.png)


## When I Listened The Most
My listening habits fluctuate through the days, it has not been crazy consistent. On Fridays and Mondays, I am listening in the afternoon-evening. I am listening a lot more during the wee hours of the night.

I also created a heatmap that shows similar insight that I'm listening a lot during the latest hours of the night as well as some in the prime of evening.

![]({{ site.url }}{{ site.baseurl }}/images/spotify-listening-habits/Dashboard4.png)


## How Frequent I Listened Every Day
The highest number of times I played a song was 190, on July 3, 2019. It looks like on this day I listened to a lot of jazz. Second, is 183 on July 8, 2019 which was a combination of Lauv, Korean songs, and more jazz by Bill Evans. According to the data, there were some days where I rarely listened to Spotify at all.

![]({{ site.url }}{{ site.baseurl }}/images/spotify-listening-habits/Dashboard5.png)




## Conclusion
I had a great time putting this project together. Tableau gets easier to use every time I run through a project. Although the author did not give step by step reasoning behind his calculations, I was able to use my data, slightly different from his, and put in the appropriate calculations, sheets and formatting to get the visualizations that I wanted. I listed several questions that I wasn't sure about to make sure that I could answer them post project.

You can interact with [this dashboard](https://public.tableau.com/views/MySpotifyListeningHabitsOver3MonthsUsingTableau/MainSpotifyDashboard?:embed=y&:display_count=yes&publish=yes&:origin=viz_share_link) on Tableau Public.

## Next Steps
I want to take what I learned and do my own project regarding non-profit organizations and the distribution of the money that they raise. If I can't get the visualizations that I want, I will take steps back and go through another author's Tableau project to practice more.






*Questions I asked*
1. What is Add to Context?
2. What is a Set?
3. What's the difference between continuous and discrete?
4. What is Moving Average?
5. How did I put Morning or Afternoon?
6. Dissect this:
```IF ATTR([Artist]) = LOOKUP(ATTR([Artist]),-1) then  
 PREVIOUS_VALUE(0) + 1
ELSE
 1
END
```
7. Why is it consecutive days start or stop?
8. What is Index? - Physical position of the record. Different from rank which deals with the value of the record.
9. What does Allow Section by Category and Include Command Buttons do in Edit Tooltip?
