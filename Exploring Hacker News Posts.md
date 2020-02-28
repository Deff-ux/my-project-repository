
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


```python
# Identify posts that begin with either `Ask HN` or `Show HN` and separate the data into different lists.
ask_posts = []
show_posts = []
other_posts = []

for posts in hn:
    title = posts[1]
    if title.lower().startswith('ask hn'):
        ask_posts.append(posts)
    elif title.lower().startswith('show hn'):
        show_posts.append(posts)
    else:
        other_posts.append(posts)
        
print(len(ask_posts))
print(len(show_posts))
print(len(other_posts))
```


    

    AttributeErrorTraceback (most recent call last)

    <ipython-input-20-91b1ada8a6bb> in <module>()
          6 for posts in hn:
          7     title = posts[1]
    ----> 8     if title.lower().startswith('ask hn'):
          9         ask_posts.append(posts)
         10     elif title.lower().startswith('show hn'):


    AttributeError: 'int' object has no attribute 'lower'



```python

```
