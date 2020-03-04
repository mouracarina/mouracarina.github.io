---
layout: post
title:  "Tiple Post 1"
date:   2020-03-04 19:09:28 +0000
categories: article
preview: "Test article preview"
related_image: /images/lamp-david-unsplash.jpg
---

So your team has just developed the latest lamp in the market. Your mission is to test that this lamp works as intended. This should be a pretty straightforward test approach.
You will not be focusing on tests other than functional at this point. The idea of this exercise is to demonstrate parameter manipulation to ensure we have the write scenario for our intended validations.

## First iteration

For the first iteration of this project, the lamp has no switch and is only controlled by the fact that it might be plugged in or not.
Being a little more technical, we have one available parameter to manipulate - **the cord status** - and two possible outcomes to verify - **the light status**.

Writing your tests down, would look something like this:

| Test case                                                    | Preconditions       | Steps                  | Expected     |
|--------------------------------------------------------------|---------------------|------------------------|--------------|
| Verify that the light is on when the cord is plugged in      | cord plugged in     | Look at the light bulb | Light is on  |
| Verify that the light is off when the cord is not plugged in | cord not plugged in | Look at the light bulb | Light is off |

## Second iteration

After this, your team decides to add a switch. So now the light status will be controlled by this switch. So to write the new test cases we have the same set of outcomes, but you will have two parameters to manipulate now, which are the cord and the switch status.

This is still pretty easy to set up, you have two parameter and you have to combine them. A combination table will give us all test scenarios, that will look something like this:

| Cord status / Switch status | on           | off          |
|-----------------------------|--------------|--------------|
| pugged in                   | Light is on  | Light is off |
| not plugged in              | Light is off | Light is off |

## Product deployment

You deploy this product to production and it's a great success. Well, almost... You start receiving complaints that the switch doesn't work correctly and that the clients are forced to unplug the cord in order to turn the light off.
But according to your tests this doesn't make any sense. Your team can't understand how this is happening when the tests are all running smoothly.

So you give it a try manually. You turn the switch on and off and you are able to replicate the reported but. That still doesn't explain why the tests are still green. But this validation confirms that a bug does exist, so the automated tests must be the source.

This is a really tricky one. Your tests should be your source of truth and reliable. 
But you find out that in order to set up the testing environment, you have to set up all sorts of things that where not considered as parameters for your scenarios' analysis. You can call these the **hidden preconditions**. These can be: 
- what is the room color
- is the cord made of conductive metal
- is the light bulb new and healthy
You get the drill. These are the things you didn't develop in your team directly and don't need to be considering for your testing scenarios, but they might influence your test results.

So as it turns out, for all the tests that where suppose to have the light off, a damaged light bulb was used. So the tests where doing exactly what we wanted to, the lights where off, but for a different reason then the one we wanted to verify. 


## section
