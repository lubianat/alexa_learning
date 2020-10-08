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

It goes on explaing that I can use _classes_ or _decorators_ for the skill, but I rhould choose one style and stick to it. 

I still do not have a favorite, so I`ll test both

### Option 1: Implementation using handler classes

Add some code to `hello_world.py`:

```
from ask_sdk_core.skill_builder import SkillBuilder

sb = SkillBuilder()

```

Two methods:

-`can_handle` : Checks if class is going go reply to tne user
-`handle` : Actually provides the speech output


Building upon it, first import libs:

```
from ask_sdk_core.dispatch_components import AbstractRequestHandler
from ask_sdk_core.utils import is_request_type, is_intent_name
from ask_sdk_core.handler_input import HandlerInput
from ask_sdk_model import Response
from ask_sdk_model.ui import SimpleCard
```

And create a `LaunchRequestHandler`.

This class is called when the skill is invoked via Alexa.

```
class LaunchRequestHandler(AbstractRequestHandler):
    def can_handle(self, handler_input):
        # type: (HandlerInput) -> bool
        return is_request_type("LaunchRequest")(handler_input)

    def handle(self, handler_input):
        # type: (HandlerInput) -> Response
        speech_text = "Welcome to the Alexa Skills Kit, you can say hello!"

        handler_input.response_builder.speak(speech_text).set_card(
            SimpleCard("Hello World", speech_text)).set_should_end_session(
            False)
        return handler_input.response_builder.response
```

Cool. Now the `HelloWorldIntentHandler`, which detects a "Hello World Intent" and
replies with "Hello World". 

What is a HelloWorldIntent? Well, that will depend on the speech-to-intent modelling.
Later we`ll see more about that.



```

class HelloWorldIntentHandler(AbstractRequestHandler):
    def can_handle(self, handler_input):
        # type: (HandlerInput) -> bool
        return is_intent_name("HelloWorldIntent")(handler_input)

    def handle(self, handler_input):
        # type: (HandlerInput) -> Response
        speech_text = "Hello World"

        handler_input.response_builder.speak(speech_text).set_card(
            SimpleCard("Hello World", speech_text)).set_should_end_session(
            True)
        return handler_input.response_builder.response

```


Okay, now let`s handle other user requests.

- Request for help with `HelpIntentHandler`:

```
class HelpIntentHandler(AbstractRequestHandler):
    def can_handle(self, handler_input):
        # type: (HandlerInput) -> bool
        return is_intent_name("AMAZON.HelpIntent")(handler_input)

    def handle(self, handler_input):
        # type: (HandlerInput) -> Response
        speech_text = "You can say hello to me!"

        handler_input.response_builder.speak(speech_text).ask(speech_text).set_card(
            SimpleCard("Hello World", speech_text))
        return handler_input.response_builder.response
```

- Request to cancel command with `CancelAndStopIntentHandler`:

```
class CancelAndStopIntentHandler(AbstractRequestHandler):
    def can_handle(self, handler_input):
        # type: (HandlerInput) -> bool
        return is_intent_name("AMAZON.CancelIntent")(handler_input)
                or is_intent_name("AMAZON.StopIntent")(handler_input)

    def handle(self, handler_input):
        # type: (HandlerInput) -> Response
        speech_text = "Goodbye!"

        handler_input.response_builder.speak(speech_text).set_card(
            SimpleCard("Hello World", speech_text)).set_should_end_session(True)
        return handler_input.response_builder.response

```

- Request to end session command with `SessionEndedRequestHandler`:

```
class SessionEndedRequestHandler(AbstractRequestHandler):
    def can_handle(self, handler_input):
        # type: (HandlerInput) -> bool
        return is_request_type("SessionEndedRequest")(handler_input)

    def handle(self, handler_input):
        # type: (HandlerInput) -> Response
        # any cleanup logic goes here

        return handler_input.response_builder.response
```

- Catch exceptions with `AllExceptionHandler`:

```

from ask_sdk_core.dispatch_components import AbstractExceptionHandler

class AllExceptionHandler(AbstractExceptionHandler):

    def can_handle(self, handler_input, exception):
        # type: (HandlerInput, Exception) -> bool
        return True

    def handle(self, handler_input, exception):
        # type: (HandlerInput, Exception) -> Response
        # Log the exception in CloudWatch Logs
        print(exception)

        speech = "Sorry, I didn't get it. Can you please say it again!!"
        handler_input.response_builder.speak(speech).ask(speech)
        return handler_input.response_builder.response

```

To finish for the day, let`s add Lambda handlers, as I'll host the code on AWS Lambda. 

This makes the interface of the Python code with the other bits of the skill

```
sb.add_request_handler(LaunchRequestHandler())
sb.add_request_handler(HelloWorldIntentHandler())
sb.add_request_handler(HelpIntentHandler())
sb.add_request_handler(CancelAndStopIntentHandler())
sb.add_request_handler(SessionEndedRequestHandler())

sb.add_exception_handler(AllExceptionHandler())

handler = sb.lambda_handler()
```

I`ll __skip the Option 2 for now__! After I complete the tutorial, I'll come back to study the different architectures.

Next part: [Preparing your code for AWS Lambda](https://developer.amazon.com/en-US/docs/alexa/alexa-skills-kit-sdk-for-python/develop-your-first-skill.html#preparing-your-code-for-aws-lambda)! 

# 07 October 2020

## Setting Lambda

The tutorial redirects to [a Lambda-specific tutorial](https://developer.amazon.com/en-US/docs/alexa/custom-skills/host-a-custom-skill-as-an-aws-lambda-function.html)

There are many things to set. 

For Python, the tutorial mentions that for Python, you can use a template provided in the AWS Serverless App Repository to create your function. This automatically creates the resources you need for the function to work with Alexa:

 - The Lambda function itself, with starting sample code from the corresponding GitHub repository. You can use this to experiment with the sample functionality, or replace it with your own code.
- The SDK dependencies. This eliminates the need to upload the dependencies (such as node_modules) yourself.
- The permissions
- The role for the function. This defines the AWS resources the function can access.


The tutorial then provides a link to a [sample Python skill](alexa-skills-kit-python36-factskill).

- Select a region. There is a South America (Sao Paulo)	region, so I`ll use that

Then I modify in __Application settings__:
- Application name:  hello-world-skill
- SkillDescription: Backend logic for the basic hello world skill built using the ASK SDK for Python 3.6.
- SkillFunctionName: ask-python36-hello

And click on _deploy_.  In this case, you are deploying a function as part of an _application_. 

If you go to the _deployment_ tab you can see  the status of creation, which goes from _Create in progress_ to _Create complete_

In the _Overview_ tab, there is a _Resources_ field. Click on your function and there it is.

### Setting VS Code to use lambda

I can edit there on lambda, but I don`t want it. I want to edit on VS Code. 

Luckly, there is [a tutorial for that](https://docs.aws.amazon.com/toolkit-for-vscode/latest/userguide/welcome.html)

I`ll install the extension and call it a day. 

There is a vscode extension called AWS Toolkit. I just installed it.

I have to [set credentials now](https://docs.aws.amazon.com/toolkit-for-vscode/latest/userguide/establish-credentials.html), but not today. 

# 08 October 2020

## Setting credentials

### On AWS

- Create an Identity and Access Management (IAM) User with safe permissions

 - I go to [IAM home](https://console.aws.amazon.com/iam/home) and create user

 - I give the user Programmatic access, which "enables an access key ID and secret access key for the AWS API, CLI, SDK, and other development tools."

 OOps, I need to create a group. 
 
 - I click on "Create Group"

 - I specify the Lambda service with all actions and All resources 

 - I set the request condition for the IP of my computer

 - I click on Review Policy and create the group. 

Now again

 - I go to [IAM home](https://console.aws.amazon.com/iam/home) and create user

 - I give the user Programmatic access, which "enables an access key ID and secret access key for the AWS API, CLI, SDK, and other development tools."

 - I give up on the group and use the policy AWSCodeDeployRoleForLambda
 
 - I skip tags

 Now I have an access keys. Cool. 

### On VS code

- Open the Command Palette, search for AWS and choose AWS: Create Credentials Profile.

- I add the key and the secret

and back to the [tutorial](https://docs.aws.amazon.com/toolkit-for-vscode/latest/userguide/connect.html).

- Command Palette --> AWS: Connect to AWS. and choose a profile.

Now there is an AWS side bar. Nice. 

I add the São Paulo region.

I skip the part on [changing regions](https://docs.aws.amazon.com/toolkit-for-vscode/latest/userguide/setup-region.html)

The next part is [configuring the toolchain](https://docs.aws.amazon.com/toolkit-for-vscode/latest/userguide/setup-toolchain.html), but not today