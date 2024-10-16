# Hacker News Data Analyst Project

This notebook is following along with [this](https://www.dataquest.io/projects/guided-project-a-exploring-hacker-news-posts-2/) guided project by data quest.

## Step 1


```python
%pip install -r requirements.txt
import csv

with open ("./hacker_news.csv") as f:
  reader = csv.reader(f)
  hn = list(reader)

print(hn[:5])
```

    Processing /opt/homebrew/Cellar/python@3.13/3.13.0_1/libexec/wheel-0.44.0-py3-none-any.whl (from -r requirements.txt (line 1))
    wheel is already installed with the same version as the provided wheel. Use --force-reinstall to force an installation of the wheel.
    Note: you may need to restart the kernel to use updated packages.
    [['id', 'title', 'url', 'num_points', 'num_comments', 'author', 'created_at'], ['12224879', 'Interactive Dynamic Video', 'http://www.interactivedynamicvideo.com/', '386', '52', 'ne0phyte', '8/4/2016 11:52'], ['10975351', 'How to Use Open Source and Shut the Fuck Up at the Same Time', 'http://hueniverse.com/2016/01/26/how-to-use-open-source-and-shut-the-fuck-up-at-the-same-time/', '39', '10', 'josep2', '1/26/2016 19:30'], ['11964716', "Florida DJs May Face Felony for April Fools' Water Joke", 'http://www.thewire.com/entertainment/2013/04/florida-djs-april-fools-water-joke/63798/', '2', '1', 'vezycash', '6/23/2016 22:20'], ['11919867', 'Technology ventures: From Idea to Enterprise', 'https://www.amazon.com/Technology-Ventures-Enterprise-Thomas-Byers/dp/0073523429', '3', '1', 'hswarna', '6/17/2016 0:01']]


## Step 2


```python
headers = hn[0]
print(headers)

hn = hn[1:]
print(hn[:5])
```

    ['id', 'title', 'url', 'num_points', 'num_comments', 'author', 'created_at']
    [['12224879', 'Interactive Dynamic Video', 'http://www.interactivedynamicvideo.com/', '386', '52', 'ne0phyte', '8/4/2016 11:52'], ['10975351', 'How to Use Open Source and Shut the Fuck Up at the Same Time', 'http://hueniverse.com/2016/01/26/how-to-use-open-source-and-shut-the-fuck-up-at-the-same-time/', '39', '10', 'josep2', '1/26/2016 19:30'], ['11964716', "Florida DJs May Face Felony for April Fools' Water Joke", 'http://www.thewire.com/entertainment/2013/04/florida-djs-april-fools-water-joke/63798/', '2', '1', 'vezycash', '6/23/2016 22:20'], ['11919867', 'Technology ventures: From Idea to Enterprise', 'https://www.amazon.com/Technology-Ventures-Enterprise-Thomas-Byers/dp/0073523429', '3', '1', 'hswarna', '6/17/2016 0:01'], ['10301696', 'Note by Note: The Making of Steinway L1037 (2007)', 'http://www.nytimes.com/2007/11/07/movies/07stein.html?_r=0', '8', '2', 'walterbell', '9/30/2015 4:12']]


## Step 3


```python
ask_posts = []
show_posts = []
other_posts = []

for post in hn:
  title = post[1]
  if title.lower().startswith("ask hn"):
    ask_posts.append(post)
  elif title.lower().startswith("show hn"):
    show_posts.append(post)
  else:
    other_posts.append(post)

print(f"Total number of ask posts: {len(ask_posts)}")
print(f"Total number of show posts: {len(show_posts)}")
print(f"Total number of other posts: {len(other_posts)}")
```

    Total number of ask posts: 1744
    Total number of show posts: 1162
    Total number of other posts: 17194


## Step 4


```python
def calculate_number_of_comments(comments: list):
  total = 0
  for comment in comments:
    total += int(comment[4])
  return total

total_ask_comments = calculate_number_of_comments(ask_posts)
avg_ask_comments = total_ask_comments / len(ask_posts)
print(f"Average number of comments on ask posts: {avg_ask_comments}")

total_show_comments = calculate_number_of_comments(show_posts)
avg_show_comments = total_show_comments / len(show_posts)
print(f"Average number of comments on show posts: {avg_show_comments}")
```

    Average number of comments on ask posts: 14.038417431192661
    Average number of comments on show posts: 10.31669535283993


There are more comments on average on the ask posts than on the show posts.

## Step 5


```python
import datetime as dt

result_list = []
for post in ask_posts:
  created_at = post[6]
  number_of_comments = post[4]
  result_list.append((created_at, int(number_of_comments)))

counts_by_hour = {}
comments_by_hour = {}

for result in result_list:
  hour = dt.datetime.strptime(result[0],"%m/%d/%Y %H:%M")
  hour = dt.datetime.strftime(hour, "%H")
  if hour not in counts_by_hour:
    counts_by_hour[hour] = 1
    comments_by_hour[hour] = result[1]
  else:
    counts_by_hour[hour] += 1
    comments_by_hour[hour] += result[1]

print(counts_by_hour)
print(comments_by_hour)
```

    {'09': 45, '13': 85, '10': 59, '14': 107, '16': 108, '23': 68, '12': 73, '17': 100, '15': 116, '21': 109, '20': 80, '02': 58, '18': 109, '03': 54, '05': 46, '19': 110, '01': 60, '22': 71, '08': 48, '04': 47, '00': 55, '06': 44, '07': 34, '11': 58}
    {'09': 251, '13': 1253, '10': 793, '14': 1416, '16': 1814, '23': 543, '12': 687, '17': 1146, '15': 4477, '21': 1745, '20': 1722, '02': 1381, '18': 1439, '03': 421, '05': 464, '19': 1188, '01': 683, '22': 479, '08': 492, '04': 337, '00': 447, '06': 397, '07': 267, '11': 641}


## Step 6


```python
avg_per_hour = []

for hour in counts_by_hour:
  avg_per_hour.append([hour, comments_by_hour[hour] / counts_by_hour[hour]])

print(avg_per_hour)
```

    [['09', 5.5777777777777775], ['13', 14.741176470588234], ['10', 13.440677966101696], ['14', 13.233644859813085], ['16', 16.796296296296298], ['23', 7.985294117647059], ['12', 9.41095890410959], ['17', 11.46], ['15', 38.5948275862069], ['21', 16.009174311926607], ['20', 21.525], ['02', 23.810344827586206], ['18', 13.20183486238532], ['03', 7.796296296296297], ['05', 10.08695652173913], ['19', 10.8], ['01', 11.383333333333333], ['22', 6.746478873239437], ['08', 10.25], ['04', 7.170212765957447], ['00', 8.127272727272727], ['06', 9.022727272727273], ['07', 7.852941176470588], ['11', 11.051724137931034]]


## Step 7


```python
swap_avg_per_hour = []
for hour in avg_per_hour:
  swap_avg_per_hour.append([hour[1], hour[0]])

sorted_swap = sorted(swap_avg_per_hour, reverse=True)

print("Top 5 Hours for Ask Post Comments \n")

for avg, hour in sorted_swap[:5]:
  formatted_hour = dt.datetime.strptime(hour, "%H")
  formatted_hour = dt.datetime.strftime(formatted_hour, "%H:%M")
  print(f"{formatted_hour} {avg:.2f} average comments per post")
```

    Top 5 Hours for Ask Post Comments 
    
    15:00 38.59 average comments per post
    02:00 23.81 average comments per post
    20:00 21.52 average comments per post
    16:00 16.80 average comments per post
    21:00 16.01 average comments per post


## Conclusions

3 PM Eastern Time is the ideal time to post to receive comments. The overall good times seem to be spread out throughout the day with some clear tendencies to the early afternoon. 
