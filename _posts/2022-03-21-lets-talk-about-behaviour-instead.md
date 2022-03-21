---
title: Let's talk about behaviour, instead
layout: post
permalink: /lets-talk-about-behaviour-instead/
categories:
  - software testing
tags:
  - positive and negative
  - functional and nonfunctional
  - general testing
---
A while ago I wrote [a short post on LinkedIn](https://www.linkedin.com/feed/update/urn:li:activity:6855394465612386304/){:target="_blank"} on the use of categorizing test cases as 'positive' or 'negative' in software testing, and why I thought it wasn't a really useful way to look at and talk about tests, testing and test cases. In this blog post, I'd like to further unpack my thoughts on this subject and talk about another false dichotomy I think we might want to do without in software testing.

If you missed the LinkedIn post I'm referring to (understandable, given the inherently fleeting nature of content posted to social media), here are some examples I used to try and make a point:

* When you want to know how your web shop behaves at checkout when a 15 year old tries to buy alcohol, that's not 'running a negative test case', that's observing behaviour (of your web shop).
* When you want to know how your form field behaves when you enter 'banana' into a date field in your form, that's not 'running a negative test case', that's observing behaviour (of your form field).
* When you want to know how your API responds when you send a GET request for a nonexistent resource, that's not 'running a negative test case', that's observing behaviour (of your API).
* When you want to know how your code responds when you invoke a method in a way that might (should) throw an exception, that's not 'running a negative testcase', that's observing behaviour (of your method or class).

In short, even though Wikipedia seems to insist that there _is_ such a thing as '[negative testing](https://en.wikipedia.org/wiki/Negative_testing){:target="_blank"}', I don't think it is particularly useful to think about testing in terms of 'positive' or 'negative'.

Why not?

Because as soon as we start categorizing tests as either 'positive' or 'negative', there will be a(n often subconscious) bias to focus on the 'positive' test cases, as those demonstrate that our software produces an outcome that is desirable from an end user perspective.

An order that is completed, required data that are returned, a batch process that is triggered, all things that contribute to some business process getting a step closer to completion.

And that's what brings in the money: software that supports completing business processes. So, demonstrating through tests that our software is able to do that is a good thing. But how about demonstrating that the software does not do things we do not want it to do?

Don't we want to be sure that our web shop cannot sell alcohol to 15 year olds? Don't we want to be sure that our system does not accept 'banana' for a date? Don't we want to be sure that our API returns an HTTP 404 when we try to retrieve data for a nonexistent resource? Don't we want to see that our code throws the expected exception when we invoke a method with specific parameter values?

These parts of the overall behaviour of our application are what makes it robust, and even OK from a legal perspective in the first example.

I'd say the above tests can be just as, and often even more, important than what we typically refer to as the 'positive' tests. To prevent overseeing, forgetting or even skipping them, I think it is wise to stop trying to categorize tests as either 'positive' or 'negative', and see them all as a part of the overall behaviour of an application.

This is exactly why I think techniques like [Example Mapping](https://cucumber.io/blog/bdd/example-mapping-introduction/){:target="_blank"} and [Feature Mapping](https://johnfergusonsmart.com/feature-mapping-a-lightweight-requirements-discovery-practice-for-agile-teams/){:target="_blank"} typically used as part of a Behaviour-Driven Development approach to creating software, or [exploratory testing](https://www.goodreads.com/book/show/15980494-explore-it){:target="_blank"} in general are so valuable: they facilitate the opportunity for software testers and development teams to look beyond 'what the product should do', and think more closely about 'what the product should prevent the user from doing'.

Or, as I sometimes put it: it helps development teams go into '[Dee Dee mode](https://www.youtube.com/watch?v=RHfZdWcCIWc){:target="_blank"}' and identify and discuss _all_ types of behaviour, not just the part that is desirable from an end user perspective.

### About (non-)functional testing
Next to 'positive' and 'negative' test cases, there's another often used distinction in software testing that we might want to rethink: that of categorizing tests as either 'functional' or 'nonfunctional'. Typically, the former is used when we are talking about whether or not a piece of software is capable of performing an action that supports or brings us a step closer to completing a business process or outcome:

* Can we place one or more items into our shopping cart and proceed to checkout to have these items ordered and delivered?
* Can we enter an email address into the 'email' field of our order checkout form?
* Does our API return the data we are looking for when we send it a valid request?
* Does our method return an object of the right type, with the right properties, when we invoke it with / inject a specific type of input?

_Note that in light of what we discussed earlier in this blog post, the above examples could be classified as 'positive' 'functional' test cases. I could just as easily have included 'negative' 'functional' examples (apologies for the many air quotes...)._

However, the fact that our software can perform these actions _in itself_ is not enough to make for a high quality product. We also want our software to perform well, to be secure, to be intuitive to work with, to be accessible to people with specific disabilities, and so on. All these 'other' types of tests are typically grouped under the label of 'nonfunctional' tests.

And for some reason, these tests then often end up (sometimes much) lower on the priority list of the development team: where a lot of time and effort is spent to determining whether or not our software is able to support a business process or outcome, these 'nonfunctional' characteristics of our software are often underrepresented in our testing activities.

But aren't performance, security, accessibility and other 'nonfunctional' characteristics simply other aspects of the overall behaviour of our software? And if so, why is it that these aspects are so often underrepresented in our testing strategy and approach? Are they just not that important?

If you look at all the news articles and social media updates about websites being unavailable around Black Friday, or the latest data leak where sensitive personal data is exposed, I beg to differ.

Then why is it that we still spend relative little time on and attention to these parts of the desired behaviour of our software? A lot of teams have made an effort to properly embed 'functional' testing and automation into their software development lifecycle, for example by practicing BDD, using test-first approaches, making developers and testers pair on writing application and test code for functional tests, and so on.

Yet I don't see this happening nearly as often when it comes to the performance, security or accessibility aspects of the behaviour of our software..

Or to put this question in different words: when was the last time you discussed performance, security or accessibility as part of your refinement sessions?

Yes, I know, properly preparing and conducting tests that question the performance, security or accessibility of our software requires specific skills, including but definitely not limited to knowing how to wield dedicated performance, security or accessibility testing tools. But does that explain why 'nonfunctional' testing is so often an afterthought (if a thought at all) in our software testing and software development approaches?

I don't have a good answer to this problem yet, but I do think it would be a good first step to stop thinking about labeling tests as either 'functional' or 'nonfunctional', and start talking about the _behaviour_ of our software in a more holistic manner, going beyond verifying 'can the product do what it is supposed to do?'.

What do you think? What have you tried to ensure that the 'negative' and 'nonfunctional' aspects of the behaviour of your software do not go underrepresented in your testing?