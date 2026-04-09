---
title: My thoughts on 'self-healing' in test automation
layout: post
permalink: /my-thoughts-on-self-healing-in-test-automation/
categories:
  - Test automation
tags:
  - self-healing
  - graphical user interfaces
---
From what I've seen over the years, tests that exercise a system through the graphical user interface are disproportionally likely to fail for reasons other than an actual, genuine product failure. Call them false positives, call them flaky tests, call them whatever you want.

There are good reasons for this: graphical user interfaces are interfaces optimized for consumption by humans, not by machines or code. There is a lot of (often asynchronous) processing going on in your browser, all to create an application that looks good, is pleasant to work with, and generally provides a good end user experience. And remember, that end user is a human being, not a computer.

## The problem
The dynamic nature of a modern GUI often leads to test failures, even when the behaviour of the application remains unchanged. For example, if the text on a button that submits a form for a loan application used to be `Submit`, but changes to `Apply`, and you have Playwright tests that first locate and then click the button using

{% highlight typescript %}
await page.getByRole('button', { name: 'Submit' }).click();
{% endhighlight %}

your test is going to fail to locate the button, even when you're using `getByRole()`, a Playwright-recommended locator.

Another example: let's say you check that the table of accounts in an online banking platform is visible by using its HTML `id` attribute, like this:

{% highlight typescript %}
await expect(page.locator('#accounts')).toBeVisible();
{% endhighlight %}

When the value of this `id` attribute changes, for example because of a new version of the framework used to build the frontend is used, your test is going to fail to locate the table, and subsequently tell you the table is not visible, even when it is.

## Self-healing algorithms - the solution?
To avoid unwanted rework, many tools these days offer a 'self-healing' feature: whenever a test fails to locate an element, it will try and identify the element that you _intended_ to locate, often using a probabilistic algorithm that is sold to you as 'AI'.

What this means is that it consults a large collection of training data, finds similar occurrences of changes in UI layout, either from your own history of changes or from other applications, checks those against what it sees on the screen, and from there it selects the candidate element that is most likely to be the one that you intended to locate. These tools will also often report on the results of the changes they made in a test run, so that a human being can review these changes afterwards and approve or reject them.

The bigger the size of the training database, the higher the quality of the training data, and the higher the sophistication of the algorithm, the higher the probability that the tool identifies the correct element, e.g., the button with the `Apply` text that replaced the `Submit` text. Still, trying to find the 'right' element _is_ a probabilistic process, which means that mistakes will be made. That's fine, as long as you're aware of the risk, and you're willing to accept it.

## Why I think self-healing is a bad idea
So, would I recommend using self-healing frameworks as a solution to the challenge of often-changing graphical user interfaces and the test code maintenance that is the result thereof?

Well, no.

Sure, using these tools might reduce the time required to maintain your tests in the case of changing HTML and the need to update the corresponding element locators. And I don't even have a problem with the fact that the algorithms they use are probabilistic, which means they can make mistakes. I mean, there's a lot of fairly useless and downright bad human-written test code out there, too, so I don't think that problem is unique to LLM-powered test tools or LLM-generated test code.

No, the biggest problem I have with 'self-healing' test tools and frameworks is that they are, to me, nothing more than band-aids, hiding the real problem that is underneath.

That real problem, to me, is the fact that the people writing the tests weren't aware that the locators changed in the first place. Why did the text on the button change from `Submit` to `Apply`? Who committed that change in the product code? And why didn't they either change the corresponding test code, too, or informed someone in their team that the test might break because of this? Or, in case of the second example, why didn't we know that the framework update might lead to changing `id` values? Or, as is often the case, that the update happened in the first place?

_That's_ the problem that we need to address. We need to close the communication and collaboration gaps in our teams, instead of trying to patch them up with an algorithm. Test automation isn't the deliberate chase of green checkmarks, it's using tools to efficiently detect changes in the behaviour of our product. Self-healing, to me, feels like an attempt to [sweep these changes under the RUG](/stop-sweeping-your-failing-tests-under-the-rug/).

I don't know about you, but I'd rather address the real problem than applying a band-aid and hoping the problem will remain out of sight.