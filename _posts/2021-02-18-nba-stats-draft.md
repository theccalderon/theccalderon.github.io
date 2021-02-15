---
layout: post
title:  "Parsing nba.stats.com draft data in tabular form (csv)"
---

# The tool: nba-stats

This post introduces `nba-stats`, a Swift command line application that downloads data from https://www.nba.com/stats/draft/ into csv files. The goal is not so much to explain how the app was developed but to share the data. See below for some of the data that's available:

* Body fat %
* Hand length/width
* Height W O Shoes
* Height W Shoes
* Standing Reach
* Weight Lbs
* Wingspan
* Pick Overall
* Spot Up And Non Stationary Shooting Drill Data
* Lane Agility Time Seconds
* Shuttle Run Seconds
* Three Quarter Sprint Seconds
* Standing Vertical Leap Inches
* Max Vertical Leap Inches
* Max Bench Press Repetitions

#### I see this data, even though far from everything is exposed to the "public", being valuable to answer questions about the following topics:
1. Physical trend through time. Have athletes gotten faster, stronger... through time?
2. School history. How many 1st round picks have come out of Kentucky  vs...
3. How fast do I have to be to meet 1st round pick standards? Since the data can be mapped to the draft pick information, imagine knowing your goals before approaching training every single day.
4. For fun. Haven't you wondered the difference between your hand length/width and the average NBA player?

Those are some of the topics that come to mind when I see this information but I'm sure many more can be discovered by the amazing open source community. I'll also try to explore the datasets in the future to see what insights I can find. I'm already looking forward to it.

# The Data
* [history](https://nba-stats-draft.s3.amazonaws.com/history.tgz)
* [combine-spot-up](https://nba-stats-draft.s3.amazonaws.com/spotup.tgz)
* [combine-non-stationary](https://nba-stats-draft.s3.amazonaws.com/nonstationary.tgz)
* [combine-strength-agility](https://nba-stats-draft.s3.amazonaws.com/strengthagility.tgz)
* [combine-anthro](https://nba-stats-draft.s3.amazonaws.com/antro.tgz)


## The app

### Why Swift
First of all, here's the [code](https://github.com/theccalderon/draft-combine-scrapper). Now, please don't judge, the code is far from perfect since it's my first interaction with Swift. I usually pick problems like these, where I'm comfortable with the workflow, to try to use new programming languages. In my experience, doing the same thing in a different way helps to develop technique and abstraction. The positive outcome of that exercise is very interesting.

The Repo has the instructions on how to use the app. You can check them out and reach out to me if you have any questions.

