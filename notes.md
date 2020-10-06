# 06 October 2020

All right, I have not done this in a while. 

I have created a couple skills about 2 years ago, but I`m rusty. 

I`ll go back to the basics: a Hello World skill. 

There is support for developing in Python, JavaScript and Java.

Lets go with Python. 

This is not my first rodeo, so I`ll skip a few explanations.

 
I'll start by reading the docs of the [Alexa Skills Kit SDK for Python](https://developer.amazon.com/en-US/docs/alexa/alexa-skills-kit-sdk-for-python/overview.html).

## Set up virtual environment

I`ll [Set up the SDK  for Pythonin a virtual environment](https://developer.amazon.com/en-US/docs/alexa/alexa-skills-kit-sdk-for-python/set-up-the-sdk.html) (Option 1 in the tutorial)

```{bash}
$ virtualenv alexa_learning_env
$ source skill_env/bin/activate
$ pip3 install ask-sdk
```

## Developing first skill

Now the tutorial for [developing my first skill](https://developer.amazon.com/en-US/docs/alexa/alexa-skills-kit-sdk-for-python/develop-your-first-skill.html).

I need accounts, okay, got it. 

I`ll create a subdir for the hello world and:

- Create an empty hello_world.py file