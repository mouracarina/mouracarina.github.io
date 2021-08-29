---
layout: post
title:  "Why does boundary testing work?"
date:   2021-07-18 13:00:00 +0000
categories: article
preview: "You have probably heard about boundary testing already, but have you understood why does it work? Boundary testing consists of picking the extreme values of the ranges of your system and testing only those values."
related_image: /images/Why_does_boundary_testing_work/Boundary_cover.jpg
---

This technique allows you to reduce the number of validations needed without the need to validate each possible value, whilst guaranteeing the system works as intended. It is applied when the system under testing has different behaviours depending on a certain parameter (usually a number).

> The **boundary testing technique** consists of picking the extreme values of the ranges that were set and testing only those values

> **Boundary testing technique** can be applied to products that have different behaviours set in ranges. Meaning that it would have a behaviour_A from x to y and another behaviour_B from y to z.

![](/images/Why_does_boundary_testing_work/FECAC405-C5C3-4335-9A32-2F0DCF9DCAFF.jpeg#inTextImage)

## How it's applied

1. **Discovery phase**
   1. Determine the various behaviours → `A, B`
   2. Determine the precise limits of each behaviour → `A = [0, 7]`; `B = [8, 20]`
   3. Determine the expected outcome outside the limits of all behaviours → `null = ]-∞, -1]`; `null = [21, +∞[$`
2.  **Test design**
    1.  Write 2 tests per behaviour. One for the first possible number on the range and the other for the last possible number on the range → `A.tests = 1, 7`; `B.tests = 8, 20`
    2.  Write 1 test per out of the limits behaviours → `null.tests = -1, 21`

## Pick your prize example

Let’s use an example to go through this, as it will be easier to visualise. Let's imagine that you work at a fair and have bought some gifts to offer. There are lollipops, mugs, hats and T-shirts. You've set up a computer in which the customers can generate a random number and they'll get awarded:

- a **lollipop** if the number is **less than 10**
- a **mug** if it’s **at least 10**
- a **hat** if it’s **at least 20**
- a **T-shirt** if it’s **at least 30**
- numbers are randomly generated from 0 to 40

![](/images/Why_does_boundary_testing_work/F0C01141-998F-445C-BB8C-26CD351800FB.jpeg#inTextImage)

Behind the scenes, this is what ends up being implemented for the award system. This is just some pseudocode that will help explain the impacts of the tests. It is purposefully incorrect, so let's see if the tests will be able to spot the bugs.   

```jsx
if (insertedNumber > 30)
	award = "T-shirt";
else if (insertedNumber > 20) 
	award = "Hat";
else if (insertedNumber > 10) 
	award = "Mug";
else if (insertedNumber <= 10) 
	award = "Lollipop";
```

We'll try different testing approaches to determine what each one is capable of validating.

## The cherry-pick testing technique

Let's start with a simple approach in which we just pick a random number from each of the prizes (each range). We can choose these for example:

- 4 - lollipop
- 15 - mug
- 23 - hat
- 38 - shirt

By running these tests we can confirm that in each case the customer received the intended gift. So we were able to verify, with only 4 test cases, the 4 possible behaviours or possible prizes.

![](/images/Why_does_boundary_testing_work/IMG_0192.jpg#inTextImage)

Now let's try to take a sneak peek into the implementation. You can see in the illustration below how the awards are being generated, based on the implementation, and also where the tests are hitting.

![](/images/Why_does_boundary_testing_work/IMG_0193.jpg#inTextImage)

All looks good with the tests, but let's try changing the implementation to something very different and see the impacts on the tests. On the next example we've changed the limits used for prize awarding:

```jsx
if (insertedNumber > 25)
	award = "T-shirt";
else if (insertedNumber > 22) 
	award = "Hat";
else if (insertedNumber > 9) 
	award = "Mug";
else if (insertedNumber <= 9) 
	award = "Lollipop";
```

When running the tests again we can verify that all have the same result as before, despite the implementation being so different.

![](/images/Why_does_boundary_testing_work/IMG_0194.jpg#inTextImage)

As you can see, our tests produce the same results and none of them fails. Yet, the implementation is completely different and now, for example, we offer **hats for a range between 9 and 22** instead of the desired requirement to offer **hats for a range between 20 and 30**.

Our tests had full coverage of the 4 behaviours, even though they are not able to validate that the ranges have the correct limit values. This is where this technique falls short. If you only test one random value in each interval you have to understand what you are validating and what you are missing out on. Simulating a change on the implementation and checking if your tests still stand and alert for that change, by failing, is a nice exercise to run.

And to this, a new proposal arises: test all the possible values.

# The "test all the things" testing

With this technique, we would be focused on covering all the possible inputs, rather than just focusing on the possible outcomes. Since we are testing all the inputs, this is the only way to guarantee how the ranges are set up... While this is true, it is not a very efficient way to test and spend resources.

For this particular example you could just run one test per number from 0 to 40, a total of 41 test cases would not be that many. But what if this exercise had the values multiplied by 100 each?

- a **lollipop** if the number is **less than 1000**
- a **mug** if it’s **at least 1000**
- a **hat** if it’s **at least 2000**
- a **T-shirt** if it’s **at least 3000**
- numbers are randomly generated from 0 to 4000

Well, you could write a script to run each scenario, but even with automation, you would be consuming computer resources and execution time. That time will eventually add up if you implement this approach to all the different behaviours in your system.

The other question with automation in these cases is how would you implement your automation script? You could either test the values one by one or use a simple "if logic". If you specify a list with all of the numbers to test, you might accidentally skip a number or miss-type. It is not a very effective way to test and it is error-prone, as you can see exemplified below:

```jsx
var hatValues = {2000, 2001, 20002, 2003, ... }
```

So to avoid writing all of the possible numbers, you could decide to create a script with some logic:

```jsx
if (insertedNumber > 3000)
	assetThat(award = "T-shirt");
else if (insertedNumber > 2000) 
	assetThat(award = "Hat");
else if (insertedNumber > 1000) 
	assetThat(award = "Mug");
else if (insertedNumber < 1000) 
	assetThat(award = "Lollipop");
```

Can you spot the problem with the tests that we wrote above? It almost seems like our tests need testing of their own. This is the risk with writing test scripts that contain *loop logic*, is that they become unreliable and with the same uncertain behaviour as the system itself. When creating test scripts it is always advisable that you write them in very explicit ways, with specific values. Something more like this:

```jsx
test tshirtAwardFor35() {
	insertedNumber = 35;
	assetThat(award = "T-shirt");
}
test hatAwardFor25() {
	insertedNumber = 25;
	assetThat(award = "Hat");
}
```

Because this is the recommended way to write your validations, the "test all the things technique" works if you have very small ranges of values but does not scale to larger ranges.

Since choosing random numbers or testing all of them is not the answer, that is where the boundary testing approach comes to the rescue. To apply it, we first need to go back to the requirements.

## Questions, questions, questions...

The first thing to do as a tester is to question the requirements. I know this is true for all cases, but especially when dealing with ranges and numbers there is a tendency to have misunderstandings. The goal of this exercise is to have the limits defined in a clear and very verbose way so that we know we are choosing the expected boundary values when designing the tests.

Let's revisit the initial requirements:

- a **lollipop** if the number is **less than 10**
- a **mug** if it’s **at least 10**
- a **hat** if it’s **at least 20**
- a **T-shirt** if it’s **at least 30**
- numbers are randomly generated from 0 to 40

We can ask something like "is the 30 included or not on the T-shirt prize?”, but even this question can be ambiguous and I've seen it generate confusion still. To get to the precise requirement you should ask within the specific context, guiding the response to something very specific. This is how it would look like for this exercise:

- If a customer gets a 40, will it still win the T-shirt? → `Yes`
- If a customer gets a 30, will it win the hat or the T-shirt? → `The T-shirt`
- Does that mean that the number 29 wins the hat? → `Yes`
- If the number is 20, what will the prize be? → `The hat`
- Does that mean that the number 19 wins the mug? → `Yes`
- If the number is 10, what will the prize be? → `The mug`
- Does that mean that the number 9 wins the lollipop? → `Yes`
- If the number is 0, will it still award the lollipop? → `Yes`
- Can a customer get a number higher than 40? What happens if they’ve got 41? Will we display an error, and if so, which one? → `No need for an error. Just award no prize`
- Can a customer get a number lower than 0? What happens if they’ve got -1? Will we display an error, and if so, which one? → `No need for an error. Just award no prize`

The most important thing you need to confirm with the product is **where does a range start and where does it end**. You also need to know **what happens outside of these ranges**, where there would be no gifts.

These answers will allow you to build something like this:

![](/images/Why_does_boundary_testing_work/IMG_0195.jpg#inTextImage)

This is a very exhaustive way to represent what is happening for each number. This is just a support method that you will not need to draw each time, once you get a hold on the technique, but it helps with understanding where the technique comes from. It specifies all the possible values. Each value has one behaviour, and never more than one, even if the behaviour is to "do nothing", or in this case "no gift".

With the questions all done, you can rest assured that your intervals are correctly defined and we can move on to applying the boundary testing technique.

## The boundary testing technique

It is now very easy for us to highlight the boundaries of each range (in blue) and also the boundaries that are outside of any range (in red).

![](/images/Why_does_boundary_testing_work/IMG_0196.jpg#inTextImage)

For each section/award, I’ve written two tests and only two. These tests will use the highlighted values in blue, being these the lowest and the highest possible number for each award. I've also added tests for the out of the edge numbers highlighted in red. These will be the highest number of the last range plus 1 (41) and the lowest number of the first range minus 1 (-1).

![](/images/Why_does_boundary_testing_work/IMG_0197.jpg#inTextImage)

We have a total of 10 tests, 2 per range and 2 additional ones for outside of all ranges. The list can be viewed below.

![/](/images/Why_does_boundary_testing_work/IMG_0199.jpg#inTextImage)

But the important thing now is to understand why does this work. Why am I able to validate this feature with only 10 tests and they have to have these exact values?

# Why does the boundary testing technique work?

This only works because we understand how it is being implemented. We know that the implementation uses intervals and *if logics* that were set up with something like `if (insertedNumber > 25)`. If you look closely, this means that the only place where we could have issues is on the number 25 or even the operands `>` or `≥` , since these were defined manually. All the other numbers of the range are being determined by the processor's logic.

Even though I'm mentioning that we should create tests based on the implementation, this does not mean that we are doing *white box testing*, not quite, because we don't need to know the values that were used. With this approach, we are just bringing awareness to the test designed. We are concerned about the implemented algorithm but not with the specific values. This allows us to have tests that target the weakest spots of the system, rather than testing every value. We can consider it as more of a *grey testing technique* since you are very much interested in the methods used to implement the logic and you will base your tests on those methods, but you are not interested in seeing the code for yourself and analyse it.

If this implementation had been done differently, let's say a list of values like this `if (insertedNumber = {1,2,3,4,5,6,7,8,9})`, we would not have been successful when using boundary testing. If we had just a list of values, the implementation could be missing a number or have a mistyped. This means that the boundary technique wouldn’t apply because all of the values were set up manually. In this case, it would be necessary to test each value, since we had no way to infer that *"since 10 and 19 award a mug, then it is certain that 11, 12, ..., 18 also offer a mug"*. This is why it is important to understand how the implementation was done to assess correctly what testing technique to use.

As we did before, now it's time to check our tests and confirm if they would stand some implementation changes.

## The ultimate confirmation

We'll start with the initial implementation

```jsx
if (insertedNumber > 30)
	award = "T-shirt";
else if (insertedNumber > 20) 
	award = "Hat";
else if (insertedNumber > 10) 
	award = "Mug";
else if (insertedNumber <= 10) 
	award = "Lollipop";
```

And let's pick up our tests and run through them.

![](/images/Why_does_boundary_testing_work/IMG_0199.jpg#inTextImage)

Here are the results.

![](/images/Why_does_boundary_testing_work/IMG_0200.jpg#inTextImage)

As you can see by this illustration above, some of our tests are failing. Remember that we have confirmed the requirements in the meantime, so the initial implementation might no longer fit these requirements.

Let's take the Hat's tests for example. We were expecting both 20 and 29 to award the Hat, but the 20 was awarded the Mug. Looking into the code it becomes clear that the issue here is that it is verifying if the number is HIGHER than 20 - `if (insertedNumber > 20)` -, so only 21 or higher will offer the Hat. We can easily fix this, as well as the other offers, by changing `>` by `>=`, as presented below.

```jsx
if (insertedNumber >= 30)
	award = "T-shirt";
else if (insertedNumber >= 20) 
	award = "Hat";
else if (insertedNumber >= 10) 
	award = "Mug";
else if (insertedNumber < 10) 
	award = "Lollipop";
```

Let's run the tests again.

![/](/images/Why_does_boundary_testing_work/IMG_0201.jpg#inTextImage)

Now we can see that the "gifts" tests are all ok, but we still have the "no gifts" tests failing. This is also a very common mistake, not specifying the behaviours outside of the valid limits. To fix this one, the implementation will need a new set of validations as presented below.

```jsx
if (insertedNumber > 40)
	award = null;
else if (insertedNumber >= 30)
	award = "T-shirt";
else if (insertedNumber >= 20) 
	award = "Hat";
else if (insertedNumber >= 10) 
	award = "Mug";
else if (insertedNumber < 10) 
	award = "Lollipop";
else if (insertedNumber < 0)
	award = null;
```

Let' run the tests again.

![](/images/Why_does_boundary_testing_work/IMG_0202.jpg#inTextImage)

Finally we have all the tests ok. It was clear to see that this technique did in fact get to the point were it was relevant to test.
At first, we had the tests finding an error that we had on the definition of the limits. This was caused by a simple error in the implementation, mistaking `>` with `>=`. This, which seems like a detail, can change your product's behaviour drastically.
After that, we were able to identify that our product was always offering gifts. The implementation had not taken into account that there were 2 "hidden" ranges, the ones with no gifts to offer.

Just for fun, we can change the implementation with the same conditions that we did on the cherry pick example and see the impact of our tests.

```jsx
if (insertedNumber > 25)
	award = "T-shirt";
else if (insertedNumber > 22) 
	award = "Hat";
else if (insertedNumber > 9) 
	award = "Mug";
else if (insertedNumber <= 9) 
	award = "Lollipop";
```

With this last example, we can see that implementation changes do impact the tests.

![/](/images/Why_does_boundary_testing_work/IMG_0203.jpg#inTextImage)

Be aware that not all tests will fail though, and it is expected. This is also why we need more than one test per range or per limit, as they complement each other and mean nothing when run alone. If you would only test one of the values, say number 30, the test would still be ok. The number 30 is still awarding the Shirt, but this does not prove that the T-shirt range starts at 30. To prove that, you would need to confirm that 29 is not offering the T-shirt. This is the only way you can confirm where the limit is, thus, only with those two tests would we be able to confirm if the limit between Mug and T-shirt was correctly defined.

# Final consideration

The boundary testing technique seems very simple but it's not that straightforward. It is also very prone to mistakes since it depends highly on defining the correct limits and testing the exact numbers. But it is a strong technique that allows you to test with certainty without the need to go through all the possible scenarios.

I'll leave you with this question, now that you've seen how it works: **If you have tested the numbers 30 and 40 and this award you a T-shirt, would you expect 31, 35 or 39 not to?** Given that the implementation is going to be using ranges, testing the other numbers adds no value to your tests. Thus, the boundary testing technique focuses on the values that make a difference when testing (boundary values) and ignores redundant validations (other values inside the ranges).

> **It is not about the number of tests that you perform, but their intention.**

## Use it to complement your test coverage

You may have noticed that many tests are missing in this exercise. For example, we should have tested a very big number like *99999999999*, a letter *B*, etc. The boundary testing technique guarantees only that the rewards' mechanism is based on the expected values, but you will most likely need to validate much more than that if you are trying to assess if your product is ready for deployment.

Remember to use the boundary testing technique when it is suitable for the implementation and pair it with other testing techniques to guarantee full coverage of all of your system's behaviours.