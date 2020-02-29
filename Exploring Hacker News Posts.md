
# Exploring Hacker News Posts

Hacker News is a site started by the startup incubator Y Combinator, where user-submitted stories (known as "posts") are voted and commented upon, similar to reddit. Hacker News is extremely popular in technology and startup circles, and posts that make it to the top of Hacker News' listings can get hundreds of thousands of visitors as a result.

You can find the data set here, but note that it has been reduced from almost 300,000 rows to approximately 20,000 rows by removing all submissions that did not receive any comments, and then randomly sampling from the remaining submissions. Below are descriptions of the columns:

* `id`: The unique identifier from Hacker News for the post
* `title`: The title of the post
* `url`: The URL that the posts links to, if it the post has a URL
* `num_points`: The number of points the post acquired, calculated as the total number of upvotes minus the total number of downvotes
* `num_comments`: The number of comments that were made on the post
* `author`: The username of the person who submitted the post
* `created_at`: The date and time at which the post was submitted

We're specifically interested in posts whose titles begin with either `Ask HN` or `Show HN`. Users submit `Ask HN` posts to ask the Hacker News community a specific question. Below are a couple examples:

>* Ask HN: How to improve my personal website?
* Ask HN: Am I the only one outraged by Twitter shutting down share counts?
* Ask HN: Aby recent changes to CSS that broke mobile?

Likewise, users submit `Show HN` posts to show the Hacker News community a project, product, or just generally something interesting. Below are a couple of examples:

>* Show HN: Wio Link  ESP8266 Based Web of Things Hardware Development Platform'
* Show HN: Something pointless I made
* Show HN: Shanhu.io, a programming playground powered by e8vm

We'll compare these two types of posts to determine the following:
* Do `Ask HN` or `Show HN` receive more comments on average?
* Do posts created at a certain time receive more comments on average?


```python
#Read in the data
from csv import reader
opened_file = open("hacker_news.csv")
read_file = reader(opened_file)
hn = list(read_file)
hn[:5]
```




    [['id', 'title', 'url', 'num_points', 'num_comments', 'author', 'created_at'],
     ['12224879',
      'Interactive Dynamic Video',
      'http://www.interactivedynamicvideo.com/',
      '386',
      '52',
      'ne0phyte',
      '8/4/2016 11:52'],
     ['10975351',
      'How to Use Open Source and Shut the Fuck Up at the Same Time',
      'http://hueniverse.com/2016/01/26/how-to-use-open-source-and-shut-the-fuck-up-at-the-same-time/',
      '39',
      '10',
      'josep2',
      '1/26/2016 19:30'],
     ['11964716',
      "Florida DJs May Face Felony for April Fools' Water Joke",
      'http://www.thewire.com/entertainment/2013/04/florida-djs-april-fools-water-joke/63798/',
      '2',
      '1',
      'vezycash',
      '6/23/2016 22:20'],
     ['11919867',
      'Technology ventures: From Idea to Enterprise',
      'https://www.amazon.com/Technology-Ventures-Enterprise-Thomas-Byers/dp/0073523429',
      '3',
      '1',
      'hswarna',
      '6/17/2016 0:01']]




```python
#Remove header
headers = hn[0]
hn = hn[1:]
print(headers)
print(hn[:5])
```

    ['id', 'title', 'url', 'num_points', 'num_comments', 'author', 'created_at']
    [['12224879', 'Interactive Dynamic Video', 'http://www.interactivedynamicvideo.com/', '386', '52', 'ne0phyte', '8/4/2016 11:52'], ['10975351', 'How to Use Open Source and Shut the Fuck Up at the Same Time', 'http://hueniverse.com/2016/01/26/how-to-use-open-source-and-shut-the-fuck-up-at-the-same-time/', '39', '10', 'josep2', '1/26/2016 19:30'], ['11964716', "Florida DJs May Face Felony for April Fools' Water Joke", 'http://www.thewire.com/entertainment/2013/04/florida-djs-april-fools-water-joke/63798/', '2', '1', 'vezycash', '6/23/2016 22:20'], ['11919867', 'Technology ventures: From Idea to Enterprise', 'https://www.amazon.com/Technology-Ventures-Enterprise-Thomas-Byers/dp/0073523429', '3', '1', 'hswarna', '6/17/2016 0:01'], ['10301696', 'Note by Note: The Making of Steinway L1037 (2007)', 'http://www.nytimes.com/2007/11/07/movies/07stein.html?_r=0', '8', '2', 'walterbell', '9/30/2015 4:12']]


We can see above that the data set contains the title of the posts, the number of comments for each post, and the date the post was created. Let's start by exploring the number of comments for each type of post.

## Extracting Ask HN and Show HN Posts

we're ready to filter our data. Since we're only concerned with post titles beginning with `Ask HN` or `Show HN`, we'll create new lists of lists containing just the data for those titles.


```python
# Identify posts that begin with either `Ask HN` or `Show HN` and separate the data into different lists.
ask_posts = []
show_posts = []
other_posts = []

for post in hn:
    title = post[1]
    if title.lower().startswith('ask hn'):
        ask_posts.append(post)
    elif title.lower().startswith('show hn'):
        show_posts.append(post)
    else:
        other_posts.append(post)
        
print(len(ask_posts))
print(ask_posts[:5])
print(len(show_posts))
print(len(other_posts))
```

    1744
    [['12296411', 'Ask HN: How to improve my personal website?', '', '2', '6', 'ahmedbaracat', '8/16/2016 9:55'], ['10610020', 'Ask HN: Am I the only one outraged by Twitter shutting down share counts?', '', '28', '29', 'tkfx', '11/22/2015 13:43'], ['11610310', 'Ask HN: Aby recent changes to CSS that broke mobile?', '', '1', '1', 'polskibus', '5/2/2016 10:14'], ['12210105', 'Ask HN: Looking for Employee #3 How do I do it?', '', '1', '3', 'sph130', '8/2/2016 14:20'], ['10394168', 'Ask HN: Someone offered to buy my browser extension from me. What now?', '', '28', '17', 'roykolak', '10/15/2015 16:38']]
    1162
    17194


## Calculating the Average Number of Comments for Ask HN and Show HN Posts


```python
#Find the total number of comments in ask posts.
total_ask_comments = 0

for row in ask_posts:
    total_ask_comments += int(row[4])
print(total_ask_comments)
avg_ask_comments = total_ask_comments / len(ask_posts)
print(avg_ask_comments)
```

    24483
    14.038417431192661



```python
#Find the total number of comments in show posts.
total_show_comments = 0

for row in show_posts:
    total_show_comments += int(row[4])
print(total_show_comments)
avg_show_comments = total_show_comments / len(show_posts)
print(avg_show_comments)
```

    11988
    10.31669535283993


On average, ask posts in our sample receive approximately 14 comments, whereas show posts receive approximately 10. Since ask posts are more likely to receive comments, we'll focus our remaining analysis just on these posts.

## Finding the Amount of Ask Posts and Comments by Hour

Next, we'll determine if ask posts created at a certain time are more likely to attract comments. We'll use the following steps to perform this analysis:

* Calculate the amount of ask posts created in each hour of the day, along with the number of comments received.
* Calculate the average number of comments ask posts receive by hour created.


```python
#Calculating the amount of ask posts and comments by hour created.
import datetime as dt

result_list = []
for post in ask_posts:
    create_time = post[6]
    num_comments = int(post[4])
    result_list.append([create_time, num_comments])

counts_by_hour = {}
comments_by_hour = {}
f_date = "%m/%d/%Y %H:%M"

for row in result_list:
    date = row[0]
    num_comments = row[1]
    time = dt.datetime.strptime(date, f_date).strftime("%H")
    if time not in counts_by_hour:
        counts_by_hour[time] = 1
        comments_by_hour[time] = num_comments
    else:
        counts_by_hour[time] += 1
        comments_by_hour[time] += num_comments

comments_by_hour
```




    {'00': 447,
     '01': 683,
     '02': 1381,
     '03': 421,
     '04': 337,
     '05': 464,
     '06': 397,
     '07': 267,
     '08': 492,
     '09': 251,
     '10': 793,
     '11': 641,
     '12': 687,
     '13': 1253,
     '14': 1416,
     '15': 4477,
     '16': 1814,
     '17': 1146,
     '18': 1439,
     '19': 1188,
     '20': 1722,
     '21': 1745,
     '22': 479,
     '23': 543}




```python

```
