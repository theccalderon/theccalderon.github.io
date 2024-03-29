---
layout: post
title:  "Using puppeteer to save the bar!!!"
tags: [puppeteer, javascript]
---

# Automating Google Form submission using puppeteer

In this post, I'll demonstrate how to use puppeteer to fill and submit a Google Form to save The Commodore in DC!.

To see how to use it check [https://github.com/theccalderon/saving-the-bar]()

# Index:
1. [Inspiration](#inspiration)
2. [Usage](#usage)
3. [Implementation](#implementation)

## Inspiration<a name="inspiration"></a>

You guys know how difficult is to find YOUR bar right??? And if you happen to live in DC, you know   how expensive the drinks are... I've been looking for MY bar since I moved to DC with no luck, until I found The Commodore DC. It's super close to my place, it's a small space usually not packed, the staff is great (shoutout to my boy Nate, best rum and coke in town low key) and more importantly, their house beer is 3 dollars every hour every day!!!
Anyway, I recently found out the bar is closing because they didn't come to terms with the landlord. Hoping to save the bar, the regulars and the staff put together this [petition](http://tinyurl.com/savethecommodore). 
Since I don't know how many people will sign the petition and I'm not a fan of uncertainty, I decided to take matters into my own hands.

I decided to use [puppeteer](https://pptr.dev/) which is a library that allows you to automate web browser actions using NodeJS to submit several petitions myself ;).

## Usage<a name="usage"></a>

1. `git clone git@github.com:theccalderon/saving-the-bar.git`
2. `npm install`
3. `node saving-the-bar.js`

By running `node saving-the-bar.js`, 25 petitions will get submitted using different names and emails (the amount can be modified in the code). How??? Check the sections below for the explanation.

## Implementation<a name="implementation"></a>

I won't go deep into how to use puppeteer since they have great examples in their documentation. I'm going to try to outline and explain the main parts of the code.

Our program should do the following (pseudo-code):
```
got to URL
fill name input
click on Submit
```

It's clear we need a way to generate random and credible names. I just googled random names and found this incredible website that also offers an API to get multiple names, phone numbers, emails.... [https://uinames.com/](https://uinames.com/)

I decided to use XPath to select the input and submit elements. You can copy the XPath of an element following the next steps using Google Chrome:
1. Navigate to the URL
2. Open Developer tools (`Cmd+option+i`)
3. Select the desired element
4. Right click on it, Copy, Copy XPath

So we now know how to select the elements and we have the information to be submitted hence, we only need the following code (check the github page for complete source code):

```javascript
const request = require('request');
async function getNames(url)
{
  //returns a premise since we want to await for it
  return new Promise((resolve, reject) => {
    request({ url, method: 'GET' }, (error, response, body) => {
      if (error) return reject(error)
      return resolve({ body, response })
    })
  });
}
```

Here we are getting the list names using the `request` module.

```javascript
const browser = await puppeteer.launch();
const page = await browser.newPage();
//navigating to the form
await page.goto('https://docs.google.com/forms/d/e/1FAIpQLSfnnuYM7vjrEzZ1QelNuKsBumUQIRPipQLHUuXuIWvVMk-bvA/viewform');
//selecting the name input using XPath
const [inputName] = await page.$x("//*[@id=\"mG61Hd\"]/div/div/div[2]/div[1]/div/div[2]/div/div[1]/div/div[1]/input")
//setting the value for the input name, firstName and surname come from a GET request to uinames
await page.evaluate((element, value) => element.value = value, inputName, firstName+" "+surname);
//selecting the button using XPath
const [button] = await page.$x("//*[@id=\"mG61Hd\"]/div/div/div[3]/div[1]/div/div/span/span");
//clicking on the button
await button.click();

```

The code above shows how to select the input, fill it, then select the button and clicking on it. Check the comments to understand what's going on.

I'll update this post to let you guys know if my attempt to save the bar succeded!!!
