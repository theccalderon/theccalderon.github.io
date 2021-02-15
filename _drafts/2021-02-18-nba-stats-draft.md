---
layout: post
title:  "Parsing nba.stats.com draft data in tabular form (csv)"
---

# nba-stats: The tool

This post introduces `nba-stats`, a Swift command line application (yes, Swift, I'll explain why) that downloads data from https://www.nba.com/stats/ (only the [draft section](https://www.nba.com/stats/draft/) as of right now) into csv files. The goal is not so much to explain how the app was developed but to share the data. See below for some of the data that's available:

* Body fat %
* Hand length/width
* HEIGHT W/O SHOES
* HEIGHT W/ SHOES
* STANDING REACH
* WEIGHT (LBS)
* WINGSPAN
* PICK OVERALL
* SPOT UP AND NON STATIONARY SHOOTING DRILL DATA
* LANE AGILITY TIME (SECONDS)
* SHUTTLE RUN (SECONDS)	
* THREE QUARTER SPRINT (SECONDS)
* STANDING VERTICAL LEAP (INCHES)
* MAX VERTICAL LEAP (INCHES)
* MAX BENCH PRESS (REPETITIONS)

I see this data, even though far from everything is exposed to the "public", being valuable to answer questions about the following topics:
1. Physical trend through time. Have athletes gotten faster, stronger... through time?
2. School history. How many 1st round picks have come out of Kentucky  vs...
3. How fast do I have to be to meet 1st round pick standards? Since the data can be mapped to the draft pick information, imagine knowing your goals before approaching training every single day.
4. For fun. Haven't you wondered the difference between your hand length/width and the average NBA player?

Those are some of the topics that come to mind when I see this information but I'm sure many more can be discovered by the amazing open source community. I'll also try to explore the datasets in the future to see what insights I can find. I'm already looking forward to it.

## The Data
* [history](https://nba-shot-charts.s3.amazonaws.com/shots-2000.tgz)
* [combine-spot-up](https://nba-shot-charts.s3.amazonaws.com/shots-2001.tgz)
* [combine-non-stationary]()
* [combine-strength-agility]()
* [combine-anthro]()


## The app

### Why Swift
First of all, here's the code [](https://github.com/theccalderon/draft-combine-scrapper). Now, please don't judge, the code is far from perfect since it's my first interaction with Swift. I usually pick problems like these, where I'm comfortable with the workflow, to try to use new programming languages. In my experience, doing the same thing in a different way helps to develop technique and abstraction. It helps to think about the problem instead of jumping straight to try to solve it. The positive outcome of that exercise is very interesting.

The Repo has the instructions on how to use the app. You can check them out and reach out to me if you have any questions.

