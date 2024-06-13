---
title: On (more) meaningful automation
layout: post
permalink: /on-more-meaningful-automation/
categories:
  - Test automation
tags:
  - learning
  - software development
---
_This blog post was published earlier in my (now defunct) weekly newsletter on March 21, 2023._

In this blog post, I wanted to spend some time diving deeper into a question sent to me by Saifuddin Raj, one of the subscribers to my (now defunct) newsletter. They asked me to explore the concept of 'meaningful automation' in a little more detail, and give some pointers on how to make your automation meaningful. I hope this blog post answers that question.

To properly discuss what 'meaningful' entails, we first need a definition to work with. I'm not claiming that this is the definitive definition of 'meaningful automation', it's my definition, or at least the one I'll base my thoughts on in this newsletter. So, let's start with this:

> Meaningful automation is automation that is written to retrieve valuable information about the quality of our product (when we're writing checks), or to help uncover this information in a more efficient way (when we're using automation in forms other than writing checks).

Again, it's probably not perfect as a definition (but when is a definition ever perfect?), but I think it's a good enough start. Let's unpack the definition and look at meaningfulness in automation in a little more detail.

When I say that tests should retrieve valuable information, I'm looking to write tests that do two things.

#### Tests provide me with information about the quality of the product that is important to someone
This follows Jerry Weinberg's definition of quality being 'value to some person'. The tests I write should check something that is meaningful and important to someone. This can be the end user (does this widget still work?) or the development team (how are we doing on code quality?), for example.

The test should provide valuable information when it passes, e.g., 'hey, the properties of our widget that we're checking haven't changed since the last commit', as well as when it fails, e.g., 'hey, someone introduced some lines of code that do not meet our agreed upon quality standards'.

#### Tests provide information in a meaningful way
In other words, they present human-readable confirmation in case a test passes, as well as actionable feedback when a test fails. Human-readable confirmation can be as simple as using descriptive test names and descriptions, or as detailed as providing audit logs, including screenshots, for every click and keystroke in a user interface-driven test.

Actionable feedback means that when a test fails, the reason it fails should be immediately clear to a human being, and ideally they should know how to address the issue without having to dig too much deeper. This might mean catching thrown exceptions and providing a human-readable error message instead. This might mean adding screenshots whenever a click or keystroke fails. This might mean lots of other things, but in general, make sure that the information provided by a failing test makes the life of those who read and have to act on it as easy as possible.

But how do we determine whether we need to write a test in the first place? A beautifully written test that provides actionable information that no one really needs is sad, and just a waste of time and resources.

Over the years, I've learned to ask myself and others tasked with writing tests and with deciding which tests to write a couple of quick questions that help determine separate the wheat from the chaff. The first question I ask when I need to decide whether to write a test is

> "Do we really need the information provided by this test?"

Again, writing tests is only meaningful when the information they provide, on pass and on fail, is valuable to someone. Tests take up time, space and resources, and these are always in limited supply. Choose which tests to write wisely, and please don't forget to take future maintenance effort into account as well as you can. I know, predicting the future is hard, but be sensible about this.

By the way, if you find yourself, and your team, to answer 'yes' to this question too easily, you might want to turn the question around and ask yourself

> "What might happen if we did not have the information provided by this test?"

instead. This might help you think even more critically about whether to add a test and the resources they bring with them. Again, take development, execution and maintenance into account. Oh, and if you're in doubt about whether to add an existing test to a deployment pipeline, here's a slight variation on the same questions that might be helpful:

> "Do we really need the information provided by this test before we deploy?"

and

> "What might happen if we did not have the information provided by this test before we deploy?"

So, think hard about the automation that you write, and think harder about whether you need it in the first place. A lot of time and effort has been spent writing, running and maintaining automation that was created 'just because we could'.

Also, don't be tricked into writing automation simply because your manager or someone claiming they've got more seniority says you should. Sure, they might have a point, but please ask them to explain why they think adding a test, or even an entire category of tests, would be a meaningful thing to do. Their answer to that question should speak volumes about whether they actually have a clue. Don't be afraid to challenge them.

Finally, don't be tricked into writing tests simply to attain certain predefined coverage levels, or because your 'Definition of Done' claims that a test should be written. These are typically quantitative metrics, which often have little or no relation to the qualitative aspect of a test, which is what you should be much more interested in. They say 'quality over quantity' for a reason...

So, to wrap things up, I hope this gives you some insights into how I think about meaningful automation and how to achieve it. I hope the questions and advice I've mentioned in here help you take a step, however small it might be, towards more meaningful automation.