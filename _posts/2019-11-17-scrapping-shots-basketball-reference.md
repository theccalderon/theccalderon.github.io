---
layout: post
title:  "Web spider to crawl the shot charts from basketball-reference.com"
---

# Scraping shots from basketball-reference

In this post I'll be introducing `shotChart`, a web spyder created using scrapy (python library) to get the shots from basketball-reference.com. It will mainly be focused on how it was implemented.

To see how to use it check https://github.com/theccalderon/shotChart

## Inspiration

I created shotChart for mainly two reasons:
1) Wanted to learn how to use the python library [scrapy](https://docs.scrapy.org/en/latest/index.html)
2) I was curious about what could we do if we had shot chart datasets?????

## Usage
1) `git clone git@github.com:theccalderon/shotChart.git`
2) `pip install -r requirements.txt`
3) `scrapy crawl basketball-reference -o ./shotChart/data/shots-2000.csv -a season=2000`

For more information on how to use, see [https://github.com/theccalderon/shotChart](https://github.com/theccalderon/shotChart).

## Implementation

I won't go deep into how to use scrapy since they have great tutorials in their documentation. I'm going to try to outline and explain the main parts of the code.

The `calendar.json` file has the start and end dates for each one of the NBA seasons (regular and playoffs) starting in 2000. This file is used to loop to every game every day of the season. 

Now, the main dish (actually this posts is more like a week day lunch so... there is only one dish) `basketball-reference.py`. 

As you would think, the idea behind the code should be something like the following (yayyyyy I'm going to use pseudo-code!):
```
for day from startDate to finishDate:
    list_of_games <- list of games in day
    for game in list_of_games:
      return list_of_shots
```
Now that's easy, why am I even reading this post????? Well, we do need to know how to use dynamic dates to go through each day, how to get the list of games for one day and how to get the actual shots. 

### Iterating through each day:

Since we have the start and end dates for the season (`calendar.json`) we can use the following code to iterate through each day:
```
from datetime import date, timedelta
start_date = date(start_year, start_month, start_day)
end_date = date(end_year, end_month, end_day)
delta = timedelta(days=1)
while start_date <= end_date:
    year = str(start_date).split('-')[0]
    month = str(start_date).split('-')[1]
    day = str(start_date).split('-')[2]
    urls.append('https://www.basketball-reference.com/boxscores/?' + 'month=' + month + '&day=' + day +
                '&year=' + year)
    start_date += delta

```

The code above creates two dates objects for the start and end date. We then proceed to get the year, month and day for each day since the format of basketball-reference is `https://www.basketball-reference.com/boxscores/?month=MM&day=DD&year=YYYY`. We need to store each one of these urls to later get the list of shots from each game that day. Finally, we increment the start_date by our delta (in this case just 1 day).

### Getting the list of games per day:

For each day (each URL), we proceed to then get the list of games, see below:
```
for game in response.css('div.game_summary.expanded.nohover'):
  next_page = game.css('p.links a::attr(href)')[2].get()
```
The most important part of the code above is how we get the `next_page`, which in this case means the actual shot chart. ![Image](/assets/shot-chart.png)

As you can see in the image, we can navigate to the shot-chart by appending `/boxscores/shot-chart/200010310ATL.html` to `https://www.basketball-reference.com/`. So that's `game in list_of_games`.

### Getting the list of shots
![Image](/assets/shot.png)

As you can see, we have a shot chart for each team ( 2 shot charts) per game. We do have two divs formatted as `<div is="shots-XXX" class="shot-area">` where `XXX` stands for a team. Under each one of these 2 divs, we have a list of divs with each one of the shots.

```
for chart in response.css('div.shot-area'):
    for shot in chart.css('div.tooltip'):
        style = str(shot.css('::attr(style)').get()).split(';')
        shot_description = str(shot.css('::attr(tip)').get())
        ...
        ...
```

We select the divs with class `shot-area` and iterate thorugh them. We then select the divs with class `tooltip` and we get the shot information.

# Datasets

These scraper has already been run and the datasets are publicly available (I did a little bit of software engineering on the original datasets) at:
* [2000-2001](https://nba-shot-charts.s3.amazonaws.com/shots-2000.tgz)
* [2001-2002](https://nba-shot-charts.s3.amazonaws.com/shots-2001.tgz)
* [2002-2003](https://nba-shot-charts.s3.amazonaws.com/shots-2002.tgz)
* [2003-2004](https://nba-shot-charts.s3.amazonaws.com/shots-2003.tgz)
* [2004-2005](https://nba-shot-charts.s3.amazonaws.com/shots-2004.tgz)
* [2005-2006](https://nba-shot-charts.s3.amazonaws.com/shots-2005.tgz)
* [2006-2007](https://nba-shot-charts.s3.amazonaws.com/shots-2006.tgz)
* [2007-2008](https://nba-shot-charts.s3.amazonaws.com/shots-2007.tgz)
* [2008-2009](https://nba-shot-charts.s3.amazonaws.com/shots-2008.tgz)
* [2009-2010](https://nba-shot-charts.s3.amazonaws.com/shots-2009.tgz)
* [2010-2011](https://nba-shot-charts.s3.amazonaws.com/shots-2010.tgz)
* [2011-2012](https://nba-shot-charts.s3.amazonaws.com/shots-2011.tgz)
* [2012-2013](https://nba-shot-charts.s3.amazonaws.com/shots-2012.tgz)
* [2013-2014](https://nba-shot-charts.s3.amazonaws.com/shots-2013.tgz)
* [2014-2015](https://nba-shot-charts.s3.amazonaws.com/shots-2014.tgz)
* [2015-2016](https://nba-shot-charts.s3.amazonaws.com/shots-2015.tgz)
* [2016-2017](https://nba-shot-charts.s3.amazonaws.com/shots-2016.tgz)
* [2017-2018](https://nba-shot-charts.s3.amazonaws.com/shots-2017.tgz)
* [2018-2019](https://nba-shot-charts.s3.amazonaws.com/shots-2018.tgz)
* [2019-2020](https://nba-shot-charts.s3.amazonaws.com/shots-2019.tgz)

# Future work:

Anybody can download [http://d2p3bygnnzw9w3.cloudfront.net/req/1/images/bbr/nbahalfcourt.png](http://d2p3bygnnzw9w3.cloudfront.net/req/1/images/bbr/nbahalfcourt.png) and use the columns `x` and `y` in the datasets to plot shot distributions, shots per player, shots by distance.........
