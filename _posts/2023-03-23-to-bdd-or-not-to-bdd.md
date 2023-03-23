---
title: To BDD or not to BDD?
layout: post
permalink: /to-bdd-or-not-to-bdd/
categories:
  - Behaviour-driven development
tags:
  - principles and practices
  - development
---
_This blog post was published earlier in [my weekly newsletter](/newsletter/) on January 24, 2023._

Today's blog post is inspired by a [BDD training course](/training/behaviour-driven-development/) I've been running with a long-standing client of mine. I've been practicing and teaching BDD for a number of years now, and it's one of the more popular courses I've got on offer.

BDD, as a technique, has been around for ages, but a lot about it is still misunderstood. In this newsletter, I'd like to discuss and bust two of those misconceptions.

Quick fact: one of the things I do at the start of the course is to list some misconceptions people have around BDD, and explain why they are exactly that: misconceptions. It's a good way to set the stage for the rest of the course, I think.

#### Misconception #1: There's only one right way to do BDD
No, there isn't. While I believe there is such a thing as the 'textbook' BDD process, and we follow that throughout the course, it doesn't mean that you'll have to always follow the process we go through in the course to the letter. Far from it! Like many methodologies, you can apply BDD in many different ways, depending on your organizational and team structure, level of experience with BDD, cumulative skill set in the team, and more.

I like to think of BDD as a toolbox, from which you can take what works for you, in your specific context, and leave what isn't as useful. Trying to follow the textbook process to the letter might work for you, but chances are that it will feel unnatural, forced and 'just not right' for you. And that's understandable, because every team and every context is different. So, by all means, experiment with what works and what doesn't, don't be afraid to stop doing things that don't work. Experiment, learn and adjust, until you find the way to apply BDD that works for you.

One thing I find myself repeating over and over, though, is that the first stage of the process (the Discovery stage) is the most important. Even if you decide to abandon BDD completely as a process for your software development practice, you might want to keep facilitating the conversations that are central to the Discovery stage, to:

* Create a shared understanding of what the feature you're about to build is supposed to do
* Get input about expected behaviour from different angles - the idea behind inviting the 'three amigos'
* Use techniques such as [Specification by Example](https://en.wikipedia.org/wiki/Specification_by_example){:target="_blank"}, [Example Mapping](https://cucumber.io/blog/bdd/example-mapping-introduction/){:target="_blank"} or [Feature Mapping](https://johnfergusonsmart.com/feature-mapping-a-simpler-path-from-stories-to-executable-acceptance-criteria/){:target="_blank"} to structure the conversations and get to that shared understanding

You can forget all about writing expected behaviour down in the Gherkin Given-When-Then format, or creating automated acceptance tests from that, but the techniques presented in the Discovery stage will be valuable for almost any team, whatever their specification / refinement / design process looks like.

#### Misconception #2: BDD is all about automation
That last point leads me to the second misconception about BDD I'd like to cover here: the idea that BDD revolves around automation. I've seen many teams that claim they practice BDD when all they really do stick a Given-When-Then abstraction layer on top of their automation, using tools like Cucumber or SpecFlow. While turning your examples and specifications into automated acceptance tests is an important part of BDD, it's not the only part.

As I said earlier, BDD is first and foremost a technique to facilitate communication and create a shared understanding of what our software is supposed to do. By only applying the Automation part of the BDD process, you're skipping that part, and thereby skipping the opportunity for your team to get on the same page and reduce the risk of misinterpretation, miscommunication and wrong assumptions about software behaviour.

Also, simply sticking a Gherkin layer on top of your automation code often has little to no added value, especially not when you take into account the effort it takes to create and maintain it in the first place. In that case, the Gherkin layer, implemented using tools like Cucumber or SpecFlow, is simply another abstraction layer on top of your code. And as with all abstraction layers, this comes with tradeoffs, typically between ease of use (the abstraction layer makes it easier to read / write code) and flexibility (the abstraction layer removes options).

[I've written about abstraction layers before](/on-codeless-automation-or-rather-on-abstraction-layers/), and even though that post focused more on low code / 'no code' tools, Gherkin, when used solely in the Automation phase, is just another example of such an abstraction layer. And it's not the most useful or powerful one, either. In fact, I think almost all the 'test automation frameworks' that use Cucumber, SpecFlow or any other Gherkin-driven tool without being backed by the conversations that are centric to BDD will do better without this abstraction layer.

To put it in other words: Gherkin is a poor (read: horrible) choice when it comes to being used as a pseudo-programming language.

But, you might say, I want people to understand what my acceptance tests do!

My answer to that: there's probably much better ways to accomplish that than using Gherkin. Here are some tips:

* Verify that the test code is actually (going to be) read on a regular basis by people that are uncomfortable / unwilling to read code. More often than not, the actual need is way lower than people claim it is. You really think your manager is going to read through every test on a regular basis? I think they've got better things to do.
* Make use of the tools provided by your programming language / the API provided by your tool of choice to make things as readable as possible. You can come a long way without using Gherkin. Here are [some tips for UI-driven acceptance tests](/do-you-really-need-that-cucumber-with-your-selenium/). These are written in Java, but will apply to other programming languages just as well.
* Talk them through what your tests are doing. Much more effective, I think, than relying on reverse engineered Gherkin gibberish.

In short: unless you're also having the conversations that are fundamental to BDD, you might want to rethink sticking Gherkin on top of everything.

Do you have any BDD misconceptions or tips and tricks you'd like to share?