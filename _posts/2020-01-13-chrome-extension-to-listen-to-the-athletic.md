---
layout: post
title:  "Listening to The Athletic!"
---

# Building a Chrome Extension to convert an article published in the Athletic to speech

In this post I'll be introducing `theAReader`, a chrome extension that allows you to convert an article to speech so you can listen to it instead of reading it. The extension uses Amazon Polly for the text to speech part and the audio is delivered using Amazon SNS via email.

See [https://github.com/theccalderon/the-athletic-texh-to-speech](https://github.com/theccalderon/the-athletic-texh-to-speech) for instructions on how to install it.

# Index:
1. [Motivation](#motivation)
2. [Usage](#usage)
3. [Audio clip](#audio)
4. [Implementation](#implementation)

## Motivation<a name="motivation"></a>

If you guys are fans of good journalism and sports, you are probably subscribed to [The Athletic](https://theathletic.com) already but if you're not, well, you should. As people say, they currently have all the "cool kids" in the business, starting with John Hollinger, David Alridge, Seth Palthrow and so many more. I personally am an avid reader of their work and sometimes find myself lacking the time or even the will to go through multiple pages on my computer. The fact is that I hate reading on any screen, it is uncomfortable and I can't even concentrate.
I know I'm not the only one out there with this problem so I thought I'd try to create "something" that converts the content of a web page to audio and see if I would like that better. I decided to create a Chrome Extension since The Athletic is subscription based therefore, you have to be logged in to access the full content. After testing multiple tech to speech services I decided to go with Amazon Polly just because I liked how it sounds (doesn't completely sound like a robot) and it's pretty easy to use.

As usual, if you're just here to use the tool, please go to the [Usage](#usage) section. Otherwise, stick around and see how it was implemented ;).

## Usage <a name="usage"></a>

### Pre-requisites
1. [AWS account](https://aws.amazon.com/free/)
2. [Amazon Cognito Identity pool](https://docs.aws.amazon.com/sdk-for-javascript/v2/developer-guide/getting-started-browser.html)
3. [AWS SNS topic](https://docs.aws.amazon.com/sns/latest/dg/sns-tutorial-create-topic.html)
4. [Amazon S3 bucket](https://docs.aws.amazon.com/quickstarts/latest/s3backup/step-1-create-bucket.html)

### Installation
1. `git clone https://github.com/theccalderon/the-athletic-texh-to-speech.git`
2. replace the values of `AWS.config.region`(`popup.js:19`), `IdentityPoolId`(`popup.js:20`), `snsTopicArn`(`popup.js:21`) and `s3_bucket`(`popup.js:22`) with the values created in the Pre-requisites section.
3. Follow [https://developer.chrome.com/extensions/getstarted](https://developer.chrome.com/extensions/getstarted) to load the extension into your Google Chrome.

### Using it

After loading the extension into your web browser, you'd see a new icon like the one below:
![Image](/assets/extension-icon.png)

You can simply click on it and input your email for the first time to subscribe to the SNS topic where the audio file will be delivered and then click on synthesize.
After that you will receive a notification via email when the task is completed and you can download your audio using the `aws s3 cp` command (see [https://docs.aws.amazon.com/cli/latest/userguide/cli-services-s3-commands.html](https://docs.aws.amazon.com/cli/latest/userguide/cli-services-s3-commands.html) for details on how to use this command.).

![Gif](/assets/ezgif.com-video-to-gif.gif)

## Audio Clip <a name="audio"></a>

I'm including a short clip of [https://theathletic.com/1515619/2020/01/09/hollingers-nba-playmaker-tiers-ranking-the-leagues-top-quarterbacks/](https://theathletic.com/1515619/2020/01/09/hollingers-nba-playmaker-tiers-ranking-the-leagues-top-quarterbacks/).

[Audio Example](https://theareader.s3.amazonaws.com/hollinger-nba-quarterback.mov)

## Implementation <a name="implementation"></a>

I'll try to point out the most important code snippets and the ones I think are more relevant to other applications you might want to develop.

The basic idea behind the extensions would be to somehow retrieve the content from the web page you are currently seeing and send it to Polly to be converted to speech.

### How to get the current content

In order to get the content of the actual tab and not the content of the extension, we need to use the [content scripts](https://developer.chrome.com/extensions/content_scripts).

#### sending a message to the content script
```javascript
chrome.tabs.query({active: true, currentWindow: true}, function(tabs) {
      chrome.tabs.sendMessage(tabs[0].id, {action: "getBody"}, function(response) {
        .
        .
        .
        .
      }
  });
});
```

The code above sends a `getBody` message to the content script and receives its response.

#### content script receives the message 
```javascript
chrome.runtime.onMessage.addListener( function(request, sender, sendResponse) {
  console.log("something happening from the extension");
  if (request.action == "getBody")
  {
    //retrieving the content of the current tab
    var text = document.body.innerText || {};
    sendResponse({text: text});
  }
});
```

This is the code in `contentScript.js` which receives the `getBody` message, retrieves the content of the current tab and sends it back to our extension.

### Sending the content to Polly

```javascript
//receiving the text from the content script
var text = response.text;
var speechParams = {
    OutputFormat: "mp3",
    OutputS3BucketName: config_data.amazon.s3_bucket,
    Engine: "neural",
    Text: "",
    TextType: "text",
    VoiceId: "Joey",
    SnsTopicArn: snsTopicArn
};
// Create the Polly service object and presigner object
var polly = new AWS.Polly({apiVersion: '2016-06-10'});
// trimming the text we want to convert to speech
text = text.split("• • •")[1]
text = text.split("What did you think of this story?")[0]
speechParams.Text = text;
// sending the text to Polly
polly.startSpeechSynthesisTask(speechParams, function(error, data){
  if (error)
  {
    console.log(error.stack);
    return; 
  }
  console.log(data.SynthesisTask.OutputUri)
  document.getElementById('result').innerHTML = data.SynthesisTask.OutputUri;
});
```

This code receives the text from the content script, formats it and sends it to Polly. Once the synthesis task is done, you will receive an email with the uri to the audio file.
It's worth noting that Polly is a paid as you go service so you might want to check the [price](https://aws.amazon.com/polly/pricing/) before using the extension.

### Other things to point out.

In order to receive an email when the synthesis task is done, we first need to subscribe to the SNS topic. I also check if the user has subscribed to this topic before so you don't have to do it again and set a local variable with the email inputted by the user.

#### subscribing to SNS

```javascript
function subscribeToSNS(email)
  {
    // subscribe and blah
    var sns = new AWS.SNS();
    var params = {
      Protocol: 'email', /* required */
      TopicArn: snsTopicArn, /* required */
      Endpoint: email
    };
    sns.subscribe(params, function(err, data){
      if (err)
      {
        console.log(err.stack)
        return;
      }
      console.log("subscribed")
    })
  }
```

#### checking if the email(endpoint) has already been subscribed to the topic

```javascript
  var sns = new AWS.SNS();
  var params = {
  TopicArn: snsTopicArn, /* required */
  };
  sns.listSubscriptionsByTopic(params, function(err, data) {
    if (err) 
    {
      console.log(err, err.stack); // an error occurred
      document.getElementById('result').innerHTML = err.stack;
      document.getElementById('result').style.display = "block";
    }
    else
    { 
      console.log(data);           // successful response
      let email = document.getElementById('textEntry').value;
      // set the email variable so the user doesn't have to input it again.
      chrome.storage.local.set({email: email}, function() {
        console.log('email is ' + email);
      });
      // checking the subscribers of that topic
      for (let index = 0; index < data.Subscriptions.length; index++) {
        const element = data.Subscriptions[index];
        if (element.Endpoint == document.getElementById('textEntry').value)
        {
          // already subscribed
          .
          .
          . 
        }
      }
    }
  });
```