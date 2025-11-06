---
layout: post
title:  "Automating the updates to my datasets using kafka, spark and airflow."
tags: [data engineering, datasets, airflow, kafka, spark, streaming]
---

## Implementing a data pipeline to perform automatic updates to the shot chart datasets.

Finally, I have some time and will to post again. 

![](https://media2.giphy.com/media/v1.Y2lkPTc5MGI3NjExZXh3d2p1czVjNXllYjR0cmp1Z3V3cmR4NTJtZDYyamxxd2ZxNTV3eiZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/3oEjHZKRgiZXYmVVbq/giphy.gif)

In all seriousness, I find it very hard these days to carve time to dedicate to personal projects. However, I was finally able to finish the implementation of a data pipeline to autmatically update the shot chart datasets used by [shot_chart](https://github.com/theccalderon/shot_chart) which I will intend to document on this post.

### Motivation
There are two main things that prompted me to build this pipeline: I needed a way to automatically update the charts near realtime and I also wanted to experiment with some of the industry standards in Data Engineer. Therefore, I decided to build the pipeline using technologies like airflow, apache kafka and spark.
Before you ask, of course this is overkill in this scenario. These tools are designed to deal with petabytes of data, but you now know why I chose to use them. It's merely a learning exercise.

### Requirements
At a high level, these pipeline needs to:
1. Run this [scraper](https://github.com/theccalderon/shotChart) on schedule
2. Merge the data to an existing dataset.

### Technologies and their purpose:
As I mentioned earlier, I used the following tools:
1. Airflow: to orchestrate the ETL jobs.
2. Apache Kafka: for data streaming and integration
3. Apache Spark: to apply transformations (feature engineering) to the dataset.


### Pipeline diagram:

### Github repo:
Here's the Github repo which contains a README that goes over setup and how to run the pipeline:<br>
[updating-datasets-data-engineering](https://github.com/theccalderon/updating-datasets-data-engineering)

### Pipeline in action:
![alt text](<Adobe Express - merged.gif>)

### Next steps:
Now that the datasets are up to date near realtime, there are plenty of new applications we can develop. We can try to feed these data to some models to predict outcomes, analyze win probability etc. More to come on this.