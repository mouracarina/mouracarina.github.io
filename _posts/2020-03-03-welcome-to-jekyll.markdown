---
layout: post
title:  "fds Post 1"
date:   2020-03-04 19:09:28 +0000
categories: article
preview: "Test article preview"
related_image: /images/lamp-david-unsplash.jpg
---

---

Your team is tasked to develop the latest lamp in the market. Your mission is to test the lamp works as intended. This should be a pretty straightforward test approach.

Keep in mind that the goal is to focus only on functional tests. The idea of this exercise is to demonstrate parameter manipulation, to ensure you have the correct scenarios. Also, all your test cases will be automated.

## First lamp's iteration SMALL CHANGE

For the first iteration of this project, the ligth is only controlled by being plugged in to the outlet or not.
Being more technical, you have one available `parameter` to manipulate - cord status - and two possible `outcomes` to verify - light status.

> **Parameter:** 
> Usually a variable of your system that is possible to control. It influences the outcome.
> 
> **Outcome:** 
> This is the behavior of your product. Your product can support multiple behaviors, being SUCCESS and FAILURE the most common ones.

Writing your tests down, would look something like this:

| Test Number | Test case                                                    | Parameter           | Steps                  | Outcome      |
| ------------------------------------------------------------ | ------------------- | ---------------------- | ------------ |
| TC#1 | Verify that the light is on when the cord is plugged in      | Cord plugged in     | Look at the light bulb | Light is on  |
| TC#2 | Verify that the light is off when the cord is not plugged in | Cord not plugged in | Look at the light bulb | Light is off |

You are able to automate your tests and your team is ready to continue this product's evolution.

## Second lamp's iteration

Your team decides to add a switch, so now the light will be controlled by it as well. To write the new test cases you have the same set of possible outcomes, but you have an additional parameter to manipulate - switch status. 

This is still pretty easy to set up as well. You have two parameters which you have to combine. A combination table will provide all test scenarios:

|                                      | Switch on           | Switch off          |
| ------------------------------------ | ------------------- | ------------------- |
| **Cord pugged in**                   | [TC#1] Light is on  | [TC#3] Light is off |
| **Cord not plugged in**              | [TC#2] Light is off | [TC#4] Light is off |

With this your product is ready and tested. 

## Lamp deployment to production

You deploy this product to production expecting a great success, but you start receiving complaints that the switch doesn't work correctly. Clients claim that they are forced to unplug the cord in order to turn the light off.
But according to your tests this doesn't make any sense. Your team can't understand how this is happening, given the tests are all running smoothly. 

So you give it a try manually to replicate the bug. You turn the switch on and off. For your amaze, you are able to replicate the reported bug: *Regardless of the switch status, the light is always on.*
This validation confirms that a bug does exist, so the automated tests could have an implementation issue.
This is a really tricky one since the tests should be your source of truth. 

After a lot of debugging, you find out that in order to set up the testing environment you have to set up all sorts of things that where not considered as parameters for your scenarios' analysis:
- is the lamp in a table or on the ground?
- are there multiple lamps plugged in the same socket?
- is the voltage high for that room?
- is the light bulb new?
- etc

You get the drill. These are the things you didn't develop in your team directly and thus are not identified as paramenters for your testing scenarios.  But they might influence your test conditions and therefor their results.
For instance, since you are focused on functional testing, you do not need to test that your lamp won't burn when the voltage is too high. But if you don't guarantee that you have the right voltage set up, then you might be seeing a `side effect outcome`. 

> **Side effect outcome:** 
> When your test case behaves as your expected, with the correct outcome, but not based on the parameters that you setup. This can happen when you have multiple parameters to setup or some hidden preconditions.
> The most common cases where this happens is when you intent your feature to fail or be blocked. 

So, as it turns out, some tests had a damaged light bulb, so they would always have the same outcome - light off - regardless of the parameters. This is why these tests where doing exactly what you wanted to, but for a different reason then the one you wanted to verify. The side effect outcome was hiding the fact that the switch was actually not doing anything. 
It was actually a `hidden precondition` that affected your test result and not your intended parameter.

> **Hidden precondition:** 
> When setting up a test case, specially in automation, it is common to forget all the necessary preconditions for it to run properly. Hidden preconditions are usually details that your team didn't develop directly or where already part of the system

This is where your test case definition went wrong. When you initially designed your tests, you knew exactly your intention with each one of the scenarios, but the details of the test setup were not clearly defined, leaving room for unintended side effect outcomes. 

## The solution

But how could you have prevented this from happening? Of course now you know that a couple of preconditions are necessary to run our tests, but are these all the variable that can affect our tests' outcomes?

The answer to this is a strategy that I have been applying and sharing with my colleges as a good practice when writing automated tests, or even when running tests manually. 
I think when you are running manual tests this strategy is more intuitive and therefor these situations are not so common. But because you do it intuitively, you usually miss this step when you are automating tests.

For this reason, lets go through what the journey would be if you were running all these tests manually:

First you would try out the so called `happy path`. This is normally identified pretty easily, being it the sole purpose of your product. In the lamp's case, this is the lamp had the light on.

> **Happy path:** 
> This is a scenario that is usually the one defined on your user story. It refers to the successful outcome of your product, in which you would need every valid parameter and precondition to be correctly setup.


Having identified the happy path, you could prepare the setup (parameters and preconditions). All the other scenarios would be run sequencially after the happy path, and you would be changing the parameter one by one, taking record of the outcomes of each change. And here is where manual tests differ so much from automated tests, in a way that manually we are able to prevent side effect outcomes naturally. **Manual tests are sequential and automated tests are independent from each other.** 

Because manually our starting point is usually the happy path, you inevitably guarantee that all necessary preconditions are met, even without needing to know all preconditions prior. This is the detail that will ensure that when you change a single parameter the outcome is influenced only because of that particular change and not caused by something else.


But the solution here is not to make the automated tests sequential, since these are independent for several reasons. It is difficult to determine the order in which they will run. You shouldn't need to run all your test suite just to validate one test case. If one test fails you don't want it to affect other tests... The list is long.

So the good practice here comes in the test case implementation phase. When implementing your test cases, start by implementing it so the outcome is the happy path. Only after you verify its result, would you change the parameters according to the test case's intention. This should also be taken into consideration when writing down the test cases to automate, being aware that you should write each test case based on the happy path while changing only the parameter that you intend to validate. Here is how this would look like for the lamp project:

##### For the happy path: Verify that the light is on when the cord is plugged in and the switch is on
This is in fact the happy path itself, so this test would be directly implemented.

##### For the other scenarios: Verify that the light is off when the cord is plugged in and the switch is off
For this one you could make a copy from the previous test case and then change the test case assertions according to the test case definition. In this case, the light is expected to be off.
At this point your test fails because you haven't yep changed the parameters and so the light is still on. Change the switch status to off and your test is now green.

## A more realistic software example

Well this is great but I'm not really testing lamps in my day-to-day job, nor nothing so simple as that. So this is fun and all, but difficult to understand the real usage. If you are working as a software tester, chances are you are more likely to be building something else than a lamp. An e-commerce site can help you understand better how this could happen.

An e-commerce site can have elements like listing the products, promotions, checkout, etc... So your team now was promoted from building lamps to develop the checkout of the lamp's website. A well-deserved promotion. But will you be able to apply your previous knowledge here and avoid side effect outcomes? 

First things first, the checkout's main goal is to allow the purchase of the lamp. For this to be successful, the client must have money available for that same purchase. 
This means that the possible outcomes are the successful purchase or the blocked purchase. The parameters are the user's account balance that can be enough money or not. 

Than lets apply our happy path based strategy for the test automation.

##### For the happy path: Verify that the checkout is successful when the user has a valid account balance
You setup the account's balance and you run your test but the checkout is not successful. Turns out you also need to define the user's currency, as this affects the price of the lamps. It is quite an easy adjustment of the values and your test is now green.

##### For the other scenarios: Verify that the checkout fails when the user has a valid account balance
Once again, copy the previous test. After that, change the test case assertion, the account balance to be bellow the lamp's price (you will not worry with boundary analysis for this exercise's purpose)
Now your test fails, so change the checkout status to fail and your test is now green.
If you hadn't based yourselves in the happy path you would be getting the same result but because of a different currency.

Here is an example more close to a real life problem. As you could see, with the strategy you don't need to know all of your products preconditions or even other features. Your site could even have shipping restrictions for certain countries that would prevent the checkout indirectly. You are protected from all these "details" once your starting-point is the happy path. 

This has prevented us from building tests that wouldn't be adding the necessary functional coverage, because 
