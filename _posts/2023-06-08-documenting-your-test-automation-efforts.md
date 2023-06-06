---
title: Documenting your test automation efforts
layout: post
permalink: /documenting-your-test-automation-efforts/
categories:
  - Test automation
tags:
  - general test automation
  - documentation
---
_This blog post was published earlier in my (now defunct) weekly newsletter on April 12, 2023._

In this blog post, I want to address a question that was sent to me by Paul Seaman on LinkedIn, He asked me whether I wanted to share my thoughts on documenting automated test cases as a means of keeping an overview of what the automation actually does.

The short answer to that question is: I'm on the fence about it.

As that is not a very useful answer, plus, it would make for an extremely short article, let's unpack that answer further.

In general, I'm not a big fan of writing lots of documentation. I can't imagine anybody really is, but since I can't speak for others, I'll stick to speaking for myself here: I don't like writing lots of documentation. I've been tasked with writing test plans, both master test plans for an entire project and more specific test plans for subprojects or releases, and I despise it.

Why? Because the moment you hit send / move from writing the plan to actual testing, it turns out that all the assumptions you made were wrong and preconditions described in the plans aren't met. So you go update the plan, try again, and again, and again, ... And at some point, you give up, and the test plan you spent weeks (sometimes even months) writing is shelved, never to be read or updated again.

I've witnessed something (sort of) similar happening with a lot of documentation around automation. Engineers spend lots of time writing, running and maintaining automation, and at some point, someone who isn't directly involved in creating the automation, but who has an interest in what's going on, or who might just be in charge of the budget, starts asking questions like

> "What things are we covering with our automated tests?"

And it makes sense to want to know what your tests do. But writing (and maintaining) documentation for your tests isn't the ideal solution, if you'd ask me. Why?

**Because your tests ARE the documentation.**

Ideally, your tests are self-documenting, meaning that just by looking at them, you know what they do and what behaviour of your application they verify. That doesn't mean that you know exactly how those tests do that, but that's not the point of self-documenting tests, anyway. On the contrary, I think it's a good idea to try and maintain a clear separation between what your tests are verifying and how they perform the steps required for that verification. Doing this tends to have a positive effect on:

* readability - when you read the tests, you aren't distracted by implementation details of your tests, and
* maintainability - chances are high that you can reuse many of the lower-level building blocks, be it methods and classes for code-based tests or other forms of building blocks in low-code solutions

Here's a test for your tests: have someone that has a good knowledge of the application that is tested, but who hasn't been working on the implementation of your tests have a look at the tests you're using. Can they tell what your tests are doing? How much explanation do they need before they see it? If you find yourself having to explain a lot of things, your tests probably aren't self-documenting well enough.

_So, what about Cucumber / SpecFlow / Behave / insert your favourite BDD library here?_

Sure, those are great tools (I'm a big fan of SpecFlow, for example), but using them comes at a cost. Unless the documentation, or rather the specifications, are created by the whole team, the cost of creating and maintaining the Gherkin layer on top of your tests often isn't worth it, and you're probably better off rewriting your test to be more self-documenting instead.

[Here's a blog post](https://www.ontestautomation.com/do-you-really-need-that-cucumber-with-your-selenium/) I wrote about this a while ago, diving deeper into this specific challenge and giving some tips on creating readable tests without using Gherkin.

So, in short, my advice would be to make your tests as self-documenting as possible. Again, your tests are the documentation.

Should you decide to write documentation for your tests anyway, then at least make sure that there's an active connection or link between the documentation or your tests.

That means that, once a test fails, this should be made visible in your documentation in some way, for example by marking that section of your documentation in red, adding a note, attaching a screenshot, whatever, as long as it's clear to readers of the documentation that something went wrong.

In other words, create living documentation for your tests. The aforementioned Cucumber, SpecFlow, Behave and other BDD tools are great at this, but there are other ways to achieve an active link, too.

If there's no active link between your documentation and your tests, chances are much higher that your documentation will become outdated as soon as the software changes. See your documentation as another abstraction layer of your tests, and like all abstraction layers, they should be kept in sync with what is happening 'under the hood'.

In code, this is automatic, but with documentation without an active link to your tests, you need to be very diligent and disciplined in doing this yourself.

Oh, and as a final tip, always keep asking yourself and your team: why are we doing this? Why are we documenting? Are the people we're documenting for really reading what we're creating?

Or is our documentation going the way of the master test plan?