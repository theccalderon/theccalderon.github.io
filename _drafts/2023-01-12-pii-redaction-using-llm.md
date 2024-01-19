---
layout: post
title:  "PII redaction using LLM: a prompt engineering introduction"
---

# PII redaction using LLM: a prompt engineering introduction

This post is an attempt to get my hands dirty with prompt engineering. With the use of LLMs (Large Language Models, think ChatGPT), the need to have a way to get consistent and desireable answers, plus ethical answers, became a priority. There are other methods to accomplish this, like fine tuning the model. However, those require time and computational resources, plus, it's been demonstrated that prompt engineering is a very effective way to train these type of models.

The goal of this post is to get us started with prompt engineering. I thought of PII redaction since it's a very important topic, but we also have a defined input and a defined output, which makes is ideal. We'll be going over the following:

## Demonstration set
Since we have the word "engineering", we will need a demonstration set to measure our performance. We'll use examples to test the accuracy of our solution. 

Q: My social security number is 23424234234
A: 23424234234

Q: Here's my credit card number 2123-1231-2312-1231
A: 2123-1231-2312-1231

Q: Send it to my address 1221 Massachusetts AVE NW
A: 1221 Massachusetts AVE NW

Q: 786-709-8545 is my phone number
A: 786-709-8545

Q: I ate the veggie bowl
A: None

If you noticed, we are not redacting the PII, but rather extracting it from the text. This is because we should always aim to break down the problem into the smallest possible action. This simplicity will helps us improve our accuracy plus, replacing a string we already know in a text is pretty straightforward.
TODO: more examples or explain we need more examples.

## Prompt candidates
Now we'll come up with some prompts to test. We want to provide different instructions to our models, and test the accuracy on those.
"Identify PII information in the text and provide it as the output"
"Detect PII information in the text"
"Identify PII information in the text and return such values"

## Testing
Now my favorite part! We'll try to write a python script that tests the different prompts using both zero shot and few shots approaches. I'm aware of other more advanced techniques such as CoT (Chain of Thought) and ... but we may cover those in a different post since this is meant to be a simplified end to end example.


