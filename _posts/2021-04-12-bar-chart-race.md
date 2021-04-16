---
layout: post
title:  "Easy way to create one of those cool Bar Chart Race visualizations"
---

# Creating a Bar Chart Visualization

I'm pretty sure everybody's seen those cool bar charts that are animated through time where you can see each bar "compete" against each other. See an example here:

<iframe src="https://master.d1hxlr6otct52k.amplifyapp.com/" style="border:0px #ffffff none;" name="myiFrame" scrolling="no" frameborder="1" marginheight="0px" marginwidth="0px" height="800px" width="600px" allowfullscreen></iframe>

This can be useful to tell a story. However, most of the times these are created using a tool (Tableau, Flourish...). The purpose of this post is to show you how to create them with just a little bit of JS and HTML.

## TL;TR
1. Go to https://observablehq.com/@d3/bar-chart-race-explained, fork the notebook and build your visualization
2. Download code as per [Enter "Download Code"](#download)
3. Copy your dataset into the root directory
4. Replace downloaded `index.html` with [this file](https://github.com/theccalderon/bar-chart-race-observable/blob/master/index.html)
5. Modify line 43 of the new `index.html` and use the relative path to your dataset (csv file)
6. Modify line 21 and 22 of the new `index.html` and update your `Title` and `Caption`
7. Deploy the contents to a local HTTP server or hosting service.

# Index:
1. [Observable](#observable)
2. [Enter https://observablehq.com/@d3/bar-chart-race-explained](#notebook)
3. [Embedding the Bar Chart Race visualization](#embedding)
4. [Enter "Download Code"](#download)
5. [Live version](#live)


## Observable<a name="observable"></a>

I've said before I'm a big fan of Jupyter Notebooks and tools like [nbdev](https://github.com/fastai/nbdev) which allows you to develop a Python Library using nothing more than Jupyter Notebooks. So you can imagine my excitement when I discovered [Observable](https://observablehq.com/) where you can pretty much create any visualization you can think of, in a Notebook!!!

## Enter [https://observablehq.com/@d3/bar-chart-race-explained](https://observablehq.com/@d3/bar-chart-race-explained)<a name="notebook"></a>

If you follow [this link](https://observablehq.com/@d3/bar-chart-race-explained) it takes you to an awesome Notebook where @mbostock takes you step by step on how to create a Bar Chart Race.
Since the purpose of this post is not so much to explain how D3.js works bur rather how to quickly create the visualization and use for your own purposes, I won't go into the details. However, you'd only need the data for the race in a CSV with columns date (in YYYY-MM-DD format), name, value and optionally category (which if present determines color).

## Embedding the Bar Chart Race visualization<a name="embedding"></a>

After you have your visualization ready, you can just select the cell, go to to the 3-dotted menu on the top right corner of the Notebook and copy the iFrame.

**But** sometimes the data you're trying to show it's not for the public and the notebooks need to be in order for you to be able to embed the cells as iFrames.

## Enter "Download Code"<a name="download"></a>

Observable also allows you to download the code of the Notebook. This way you can customize and use it as you like. Ideally, you get the graph to the point where you're in love with how it looks and then you download the code. using this approach you don't need to share your notebook so you don't lose confidentiality.

After that all you have to do is to replace the contents of your `index.html` file with the following [https://github.com/theccalderon/bar-chart-race-observable/blob/master/index.html](https://github.com/theccalderon/bar-chart-race-observable/blob/master/index.html) which I copied and modified from [this repo](https://github.com/observablehq/examples/tree/main/standalone).
Once the file has been replaced, you need to modify line 43 of `index.html`:
```js
main.redefine("data", d3.csv("./category-brands.csv", d3.autoType));
```
with the relative path to your dataset.

You should update lines 21 and 22 with your title and captions for the chart.

Once you do that, you can serve the contents using any HTTP server. [This repo](https://github.com/theccalderon/bar-chart-race-observable) has a docker-compose file that you can use to run an nginx container as following (you need to have Docker installed):
```bash
docker-compose -f docker-compose.dev.yml up
```

### Live version: <a name="live"></a>
[https://master.d1hxlr6otct52k.amplifyapp.com/](https://master.d1hxlr6otct52k.amplifyapp.com/)