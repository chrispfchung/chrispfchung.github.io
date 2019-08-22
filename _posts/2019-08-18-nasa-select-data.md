---
title: "Created a Database of Planet Information to Store Data so That Our Imaginary NASA Has Data to Query"

date: 2019-08-16
tags: [sqlite3, sample data, pandas, sql]
tooltags: [pandas, sql]
categories: [Store Data, Extract Data]
header:
  image: "/images/nasa-select-data/night-planets.jpg"
excerpt: "SQLite3 is a database where I can perform SQL commands to extract..."
mathjax: "true"
classes: wide #expand the page

---

{% include toc icon="book-open" title="Table of Contents" %}<br/>


## Project Description

Created a datastore on SQLite3 where I added planet attributes, altered columns and queried specific data regarding the 9 planets (8 when we remove Pluto from our database!).

## Project Replicated From:
<https://github.com/learn-co-students/dsc-1-05-06-selecting-data-lab-nyc-career-ds-102218>

## 1. Introduction

SQLite3 is a database where I can perform SQL commands to extract the data that I want. This is an easy to use database that I can use as a file from my computer. In this project "Created a Database of Planet Information to Store Data so That Our Imaginary NASA Has Data to Query", I'm going to create a sqlite3 object to work with, set up a table, add a column, add and remove data, and use select commands to query data about the planets. We'll set up our sqlite3 database into a pandas dataframe that we can visually see and work easily with.

## 2. Start SQLite3

To use sqlite3, we must establish a connection object that represents the database. We will call sqlite3 then connect() to a new database called 'planets.db'.

```python
import sqlite3 # import library
conn = sqlite3.connect('planets.db') # use connect class and enter database name
cur = conn.cursor() # then use that connection and enter the cursor class
```

The cursor class is important because it is used to perform SQL commands. Those are the first steps for beginning a sqlite3 project.


```python
import sqlite3 # import library
conn = sqlite3.connect('planets.db') # use connect class and enter database name
cur = conn.cursor() # then use that connection and enter the cursor class
```

## 3. Create a Table

I'd like to start the project with what the author's background story. The scenario, "NASA is interested in comparing each planet across several characteristics.  They want to know each planet's name,  color, number of moons, and mass (relative to earth).  Your columns should be the following types:"

|column | type  |
|-------|-------|
|id     |integer|
|name   |text   |
|color  |text   |
|num_of_moons  |integer|
|mass   | real  |

> **Notes:** Make sure to set the `id` column as the table's primary key."

We use our cursor object and execute() to perform a SQL command. I was originally trying to add to the database without 'cur.execute' but that would the computer does not know what I'm coding.  We'll need a triple """ before and after each command. I've also used a single quotation and it worked too. It may be a preference. Please let me know if you know why.

First I created the command format:<BR>
```python
cur.execute("""CREATE TABLE database_name
(column DATATYPE PRIMARY-KEY-IF-IT-IS,
column DATATYPE,
column DATATYPE);

""")
```

After a successful execution, it will return a cursor object.

*My thoughts*:
Follow closely to the format I wrote out above
Make sure to add id primary key

```python
cur.execute("""CREATE TABLE planets
(id INTEGER PRIMARY KEY,
name TEXT,
color TEXT,
num_of_moons INTEGER,
mass REAL);

""")
```
This query also returns cursor object.


## 5. Alter the table

We can update our table columns by using the ALTER TABLE command.<br>

Since our imaginary NASA notices that several planets have rings around them, we will add a new column that shows if a planet has rings or not. In other words, we need a boolean data type.<br>

First I'll type out cur.execute() to make a command to our table. It has similar syntax to CREATE TABLE.

```python
cur.execute("""ALTER TABLE planets
ADD COLUMN rings BOOLEAN;
""")
```

## 6. Add and remove data

### Add Data

The author has a table of planet data to add to our datastore. To save time typing out the data, the author recommends feeling free to open up the cell and copy/pasting some of the text to modify into our insert command. You could copy each row in the database, highlight each string and press ' . Remove the arrow in between each value and replace it with a ',' .

Note: sqlite3 does not use boolean values and instead uses 0 for false and 1 for true. Therefore, when I insert the data into the columns, I replace the yes/no values to 0/1 integers.

|name   |color |num_of_moons|mass|rings|
|-------|-------|-------|-------|-------|
|Mercury|gray   |0      |0.55   |no     |
|Venus  |yellow |0      |0.82   |no     |
|Earth  |blue   |1      |1.00   |no     |
|Mars   |red    |2      |0.11   |no     |
|Jupiter|orange |53     |317.90 |no     |
|Saturn |hazel  |62     |95.19  |yes    |
|Uranus |light blue|27  |14.54  |yes    |
|Neptune|dark blue|14   |17.15  |yes    |
|Pluto  |brown  |5      |0.003  |no     |



```python
#For each row, the format goes like:

# cur.execute("""INSERT INTO (database name) (column_names) VALUE (column_values); """)

cur.execute("""INSERT INTO planets
(name, color, num_of_moons, mass, rings) VALUES ('Mercury', 'gray', 0, 0.55, 0),
('Venus', 'yellow', 0, 0.82, 0),
('Earth', 'blue', 1,1.00,0),
('Mars', 'red', 2, 0.11, 0),
('Jupiter', 'orange', 53, 317.90, 0),
('Saturn', 'hazel', 62, 95.19, 1),
('Uranus', 'light blue', 27, 14.54, 1),
('Neptune', 'dark blue', 14, 17.15, 1),
('Pluto', 'brown', 5, 0.003, 0);


""")
```




    <sqlite3.Cursor at 0x10aa11d50>




```python
cur.execute("""INSERT INTO planets
(name, color, num_of_moons, mass, rings) VALUES ('Mercury', 'gray', 0, 0.55, 0),
('Venus', 'yellow', 0, 0.82, 0),
('Earth', 'blue', 1,1.00,0),
('Mars', 'red', 2, 0.11, 0),
('Jupiter', 'orange', 53, 317.90, 0),
('Saturn', 'hazel', 62, 95.19, 1),
('Uranus', 'light blue', 27, 14.54, 1),
('Neptune', 'dark blue', 14, 17.15, 1),
('Pluto', 'brown', 5, 0.003, 0);


""")
```




    <sqlite3.Cursor at 0x110e9b180>



### Remove data from the table

Since Pluto is no longer a planet, we'll remove it from the table using the DELETE FROM command.

It's getting easier to type things out without referring to the tutorial! The syntax is consistent throughout the different SQL commands.

```python
cur.execute("""DELETE FROM planets
WHERE name = 'Pluto';

""")
```

It's getting easier to type things out without needing to refer to the tutorial! The syntax is consistent throughout the different SQL commands.

### Update table data

The author says that NASA found out that Jupiter has 15 more moons so we use the `UPDATE` command to `SET` the number of moons column to 53 for the Jupiter row


```python
# Similar syntax as above

cur.execute("""UPDATE planets
SET num_of_moons = 53 WHERE name = 'Jupiter';



""")
```




    <sqlite3.Cursor at 0x10aa11d50>



## SQL to Pandas

Below is the `planets.db` database information that we just entered above. This was used from the author's repo.

|name   |color |num_of_moons|mass|rings|
|-------|-------|-------|-------|-------|
|Mercury|gray   |0      |0.55   |no     |
|Venus  |yellow |0      |0.82   |no     |
|Earth  |blue   |1      |1.00   |no     |
|Mars   |red    |2      |0.11   |no     |
|Jupiter|orange |67     |317.90 |no     |
|Saturn |hazel  |62     |95.19  |yes    |
|Uranus |light blue|27  |14.54  |yes    |
|Neptune|dark blue|14   |17.15  |yes    |

We will turn our planets database into a pandas dataframe for easier acces to our data. We continue to use cur.execute to perform SQL commands. The author first turns our data into a list and then converts the list into a dataframe.

We use cur.execute(SQL command to select what data we want).fetchall() to retrieve all data.


```python
import pandas as pd # import pandas to use
```


```python
# The command retrieves a list of all of our data
results = cur.execute("""select * from planets;""").fetchall()
results
```




    [(1, 'Mercury', 'gray', 0, 0.55, 0),
     (2, 'Venus', 'yellow', 0, 0.82, 0),
     (3, 'Earth', 'blue', 1, 1.0, 0),
     (4, 'Mars', 'red', 2, 0.11, 0),
     (5, 'Jupiter', 'orange', 53, 317.9, 0),
     (6, 'Saturn', 'hazel', 62, 95.19, 1),
     (7, 'Uranus', 'light blue', 27, 14.54, 1),
     (8, 'Neptune', 'dark blue', 14, 17.15, 1)]




```python
# Then turn the list into a dataframe
df = pd.DataFrame(results) # input data into DataFrame parameter
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
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Mercury</td>
      <td>gray</td>
      <td>0</td>
      <td>0.55</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>Venus</td>
      <td>yellow</td>
      <td>0</td>
      <td>0.82</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Earth</td>
      <td>blue</td>
      <td>1</td>
      <td>1.00</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>Mars</td>
      <td>red</td>
      <td>2</td>
      <td>0.11</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>Jupiter</td>
      <td>orange</td>
      <td>53</td>
      <td>317.90</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>



Columns are listed 0-5. We need to change them into our planets columns!

`cur.description()` pulls up a list of column names in tuple form. We can use it to extract the column names and plug it into `df.columns`



```python
cur.description
```




    (('id', None, None, None, None, None, None),
     ('name', None, None, None, None, None, None),
     ('color', None, None, None, None, None, None),
     ('num_of_moons', None, None, None, None, None, None),
     ('mass', None, None, None, None, None, None),
     ('rings', None, None, None, None, None, None))




```python
# Creating column names

# Breaking down the for loop in steps
columns = []
for i in cur.description:
    columns.append(i[0])
df.columns = columns

#Or you can do this with list comprehension, which the author used

df.columns = [i[0] for i in cur.description]
df.columns
```




    Index(['id', 'name', 'color', 'num_of_moons', 'mass', 'rings'], dtype='object')



Author recommends using a function to do this all in one step in case you want to do this multiple times or create a dataframe with different data. We will need to use this function to turn our SQL queries into pandas dataframes. This will make selecting data a breeze.

```python
def sql_select_to_df(SQL_COMMAND, cur=cur):
    results = cur.execute(SQL_COMMAND).fetchall()
    df = pd.DataFrame(results)
    df.columns =  [i[0] for i in cur.description]
    return df
```

I created it by plugging in each line of code I used above and replaced the SQL command with a parameter.




```python
df = sql_select_to_df('''select * from planets;''') # query to look at our entire database
df.head() # to save space we'll see the top 5
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
      <th>id</th>
      <th>name</th>
      <th>color</th>
      <th>num_of_moons</th>
      <th>mass</th>
      <th>rings</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Mercury</td>
      <td>gray</td>
      <td>0</td>
      <td>0.55</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>Venus</td>
      <td>yellow</td>
      <td>0</td>
      <td>0.82</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Earth</td>
      <td>blue</td>
      <td>1</td>
      <td>1.00</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>Mars</td>
      <td>red</td>
      <td>2</td>
      <td>0.11</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>Jupiter</td>
      <td>orange</td>
      <td>53</td>
      <td>317.90</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>



## Select Data

Below are several queries to select data from our df dataframe. There are a few that have queries similar to the one before it so I nested it under without the output. I also have 2 solutions that are different from the author's. Let's go!

### Select just the name and color of each planet

I love how this function turns all my queries into pandas dataframes. I find this very useful for looking at the data in this form. At first I messed up the SQL query by mixing up the order of the column names and values but I nailed it after. The query syntax is easy: select the column names of what you want from the name of your database! It gets easier.


```python
 sql_select_to_df('''select name, color FROM planets''')
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
      <th>name</th>
      <th>color</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Mercury</td>
      <td>gray</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Venus</td>
      <td>yellow</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Earth</td>
      <td>blue</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Mars</td>
      <td>red</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Jupiter</td>
      <td>orange</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Saturn</td>
      <td>hazel</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Uranus</td>
      <td>light blue</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Neptune</td>
      <td>dark blue</td>
    </tr>
  </tbody>
</table>
</div>



I love how this function turns all my queries into pandas dataframes. I find this very useful for looking at the data in this form. At first I messed up the SQL query but nailed it after. The query syntax is easy: select the column names of what you want from the name of your database! Select all columns for each planet whose mass is greater than 1.00


### Select all columns for each planet whose mass is greater than 1.00


The query syntax is snowballing off of each other in terms of easiness. The query syntax below: select all columns(\*) from the name of your database WHERE the column name and conditional.


```python
sql_select_to_df('''select * from planets WHERE mass > 1.00''')
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
      <th>id</th>
      <th>name</th>
      <th>color</th>
      <th>num_of_moons</th>
      <th>mass</th>
      <th>rings</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>5</td>
      <td>Jupiter</td>
      <td>orange</td>
      <td>53</td>
      <td>317.90</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>6</td>
      <td>Saturn</td>
      <td>hazel</td>
      <td>62</td>
      <td>95.19</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>7</td>
      <td>Uranus</td>
      <td>light blue</td>
      <td>27</td>
      <td>14.54</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>8</td>
      <td>Neptune</td>
      <td>dark blue</td>
      <td>14</td>
      <td>17.15</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>



#### Similar Query: Select the name and mass of each planet whose mass is less than or equal to 1.00
Essentially the same code except that we switch the greater than sign to less than sign. I will combine the code and my comments.
```python
sql_select_to_df('''select * from planets WHERE mass <= 1.00''')
```

### Select the name and color of each planet that has more than 10 moons


```python
sql_select_to_df('''select name, color from planets where num_of_moons > 10''')
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
      <th>name</th>
      <th>color</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Jupiter</td>
      <td>orange</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Saturn</td>
      <td>hazel</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Uranus</td>
      <td>light blue</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Neptune</td>
      <td>dark blue</td>
    </tr>
  </tbody>
</table>
</div>



Note: If we want to check our answer, we can always look back at the 'df' dataframe we first created by entering the code df.

#### Similar Query: Select the planet that has at least one moon and a mass less than 1.00
```python
sql_select_to_df('''select name from planets WHERE num_of_moons >= 1 AND mass < 1.00''')
```

*The author has it differently*: The query asked for the planet with a mass less than 1.00. The author's query is:
```python
sql_select_to_df("""select * from planets where num_of_moons >=1 and mass <= 1;""")
```

I may be totally wrong.

### Select the name and color of planets that have a color of blue, light blue, or dark blue

This was a really tricky one! But I'm so glad it worked out. I didn't want to use multiple OR or AND statements in order to get the planets with 'blue' in their names. After looking online, I found a post that nailed it.
<https://stackoverflow.com/questions/32734233/sql-select-rows-where-column-contains-any-of-word-from-string>

We use the LIKE SQL command to return values that contain that word.

Then we use the % wildcard operator that makes returns values that are after or after blue, which in our case is 'light' or 'dark'.


```python
sql_select_to_df("""select name, color FROM planets WHERE color LIKE '%blue%'""")
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
      <th>name</th>
      <th>color</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Earth</td>
      <td>blue</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Uranus</td>
      <td>light blue</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Neptune</td>
      <td>dark blue</td>
    </tr>
  </tbody>
</table>
</div>



The author does it differently - instead of using `LIKE` as I have, the author used `WHERE` and then listed the conditionals individually. My query is simpler because it does not require someone to type out all the column names and colors. However, I think the author's query would be a good idea if there were types of blues that you would not have wanted like sky blue or navy blue.

```python
sql_select_to_df("""SELECT name,
                           color
                           FROM planets
                           WHERE color == 'blue'
                           OR color == 'light blue'
                           OR color == 'dark blue';
                 """
                )
```

## Conclusion

Author ends with, "Congratulations! NASA is one step closer to embarking upon its mission to Mars. In this lab, we created a table to track all the planets in the solar system, altered the table to include another column, inserted values to populate the table, and we deleted data from the table. Then we practiced writing select statements that query a single table to get specific information. We also used other clauses and specified column names to cherry pick the data we wanted to retrieve."

We went over how to create a connection with the sqlite3 database. Then we used sql queries to create a table called planets into the database called planets.db. Then we made some changes using alter table and update. After, we created a function that transforms our queries into a dataframe. Finally, we made several SQL queries using WHERE, LIKE and the '>' '<' operators.

This project we went over several key SQL commands that I know will serve me well in data science. It lays the foundation to create tables and then make basic queries with it. We also have some queries that are different from how the author did it. As they have more experience and knowledge than me, I may be incorrect in that their solution is better -- cleaner, optimized. That being said, I stick to my strong reasoning that mine are correct.

## Next Steps
With these basic SQL queries demonstrated within this project, I want to not only continue to practice them but also find them in future queries that are more advanced.

Chris Chung is a data scientist with a background in retail who focuses on solving problems in the video game industry. Specifically, Chris uses Python and SQL to solve problems and generate insights to improve player retention and scale data systems using Tableau, AWS and regression.
