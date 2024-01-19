---
layout: post
title:  "PII redaction using LLM: a prompt engineering introduction"
---

# PII redaction using LLM: a prompt engineering introduction

This post is an attempt to get my hands dirty with prompt engineering. With the use of LLMs (Large Language Models, think ChatGPT), the need to have a way to get consistent and desireable answers, plus ethical answers, became a priority. There are other methods to accomplish this, like fine tuning the model. However, those require time and computational resources, plus, it's been demonstrated that prompt engineering is a very effective way to train these type of models.

The goal of this post is to get us started with prompt engineering. I thought of PII redaction since it's a very important topic and couldn't find any resources online (as of Jan 2024) that are using LLMs to accomplish it. We also have a defined input and a defined output, which makes is ideal. Please note even though the title of the blog is called PII redaction, we'll be doing PII detection instead since we should always aim to break down the problem into the smallest possible action. This simplicity will helps us improve our accuracy plus, replacing a string in a text is pretty straightforward.

Please note prompt chaining, agents, etc are more popular these days. These are supposed to make human prompting obsolete, so we'll try to cover those in a different post.

## Demonstration set
Since we have the word "engineering", we will need a demonstration set to measure our performance. We'll use examples to test the accuracy of our solution. 

Q:"My social security number is 23424234234"
A:"23424234234"

Q:"Here's my credit card number 2123-1231-2312-1231" 
A:"2123-1231-2312-1231"

Q:"Send it to my address 234432 Indiana AVE SE" 
A:"234432 Indiana AVE SE"

Q:"786-709-8545 is my phone number"
A:"786-709-8545"

Q:"I ate the veggie bowl"
A:"I don't know"

Q:"Mi numero the pasaporte es A78369" 
A:"A78369"

Q:"cesar_dummy@gmail.com" 
A:"cesar_dummy@gmail.com"

Q:"My birthday is in two weeks" 
A:"I don't know"

Q:"isfgk9482SD is the password" 
A:"I don't know"

## Prompt candidates
Now we'll come up with some prompts to test. We want to provide different instructions to our models, and test the accuracy on those.
1. "Identify PII information in the text and provide it as the output"
2. "Detect PII information in the text"
3. "Identify PII information in the text and return such values"

## Testing
Now my favorite part! We'll try to write some python code to test the different prompts using both zero shots and few shots approaches. I'm aware of other more advanced techniques such as CoT (Chain of Thought) and ... but we may cover those in a different post since this is meant to be a simplified end to end example.
To implement this, I used langchain and HuggingFace. Langchain is a library in python that allows you to do prompt engineering and HuggingFace is an open source community that has tons of models we can use for free.
The notebook has comments in it for more information.

Notebook:
<iframe src="https://github.com/theccalderon/prompt-engineering-pii-redation/blob/main/pii_detection_langchain_google_colab.ipynb" style="border:0px #ffffff none;" name="myiFrame" scrolling="no" frameborder="1" marginheight="0px" marginwidth="0px" height="800px" width="600px" allowfullscreen></iframe>

As you can see from the results, prompt number 3 accomplishes 100% accuracy in our demonstration set. Please note ideally you need way more examples, but this looks very promising!
