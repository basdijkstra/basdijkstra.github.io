---
title: Should testers write unit tests?
layout: post
permalink: /should-testers-write-unit-tests/
categories:
  - Test automation
tags:
  - unit tests
  - skills
---
_This post was previously published through my [newsletter](/newsletter/) on May 25, 2026. From time to time, I will republish newsletter issues on my blog here if I think people (and search engines) might benefit from it. If you want to read everything I've written once it is posted, I recommend signing up for the newsletter._

A few months ago, I had the pleasure of delivering a keynote at an internal developer conference for one of the largest banks in the Netherlands. While I still don't really see myself as a keynote speaker - I enjoy doing practical, hands-on sessions much more - I had a great time talking about challenges of E2E testing, breaking down E2E tests and [the test automation quadrant model](/the-test-automation-quadrant/) that I use in my thinking, speaking and teaching about test automation these days.

![bas_keynote](/images/blog/bas_keynote.jpg "Bas on stage at the ABN AMRO developer conference in May 2026")

However, the keynote or the contents of it are not what I want to talk about this week. Instead, it was a question that came up during the Q&A after the talk that triggered me to write this post. That question was

> "Do you think that testers should be writing unit tests?"

It's not the first time I heard that question. In fact, I've seen and heard whether or not testers should be involved in unit testing being discussed regularly in the past, with arguments for and against the various standpoints. However, I was under the impression that we, collectively, had found some sort of answer to the question and moved past this point by now. Guess I was mistaken.

I tried to give the person asking the question an answer as well as I could, but given that there is quite a bit to unpack around the topic, I'm not sure if I gave them the entire story. So, that's what I'll try and do here. Who knows they might even read it...

So, should testers be writing unit tests? Well, my answer is either a 'yes' or a 'no', depending on how you interpret the question.

Before we explore these various interpretations, what is a unit test anyway? Well, by now, I don't really know anymore, as there are so many definitions floating around, some of them contradicting each other. This is one of the reasons I came up with the test automation quadrant model as an alternative to the well-known automation pyramid model, but again, that's not the topic of this post.

For the sake of the argument, let's define a unit test as a test that verifies a very small piece of behaviour of our product, without relying on external interfaces like APIs, databases or file systems, for example.

With that definition in place, let's look at two different interpretations of the question of 'should testers be writing unit tests?'.

### Interpretation 1: Testers should be responsible for unit testing
Well, no, I don't think they should, no matter how good of a tester they are, and no matter what their coding skills are. Writing unit tests is an activity that should be performed in support of and lockstep with software development.

Often, especially in practices like test-driven development, tests are written first, and they drive the design and development of the product. Leaving the writing of these tests to testers, especially if it is done after the product code itself is written, is both inefficient and a potential source of problems.

Inefficient, because the product has already been written, yet we only know whether its behaviour matches expectations once the tests are written and run. Also, because there's a handoff happening between 'development' and 'testing', which takes up valuable time as the developer will switch to a different task while the tester writes the unit tests.

If there's a problem with the product that is discovered during unit testing, the developer needs to make a context switch back to the original task, and the more context switching you do during the day, the less efficiently you will work and the less you will get done.

A potential source of problems, because when the developer only focuses on shipping a potentially working product, they will likely not spend too much time thinking about what to test for, or how to make the product (the code, in this case) easily testable in the first place.

Also, the handoff from 'development' to 'test' I described before leaves open room for different interpretations of what the software should do, leading to potential bugs slipping through and the resulting back-and-forth discussions after the fact.

So, no, I don't think we should leave unit testing to testers. I still sometimes hear about developer who don't want to or do not know how to write (decent) unit tests, and I think that's a problem that needs to be fixed at the source, instead of trying to patch it up by someone else writing the unit tests for the already-created product.

### Interpretation 2: testers should be involved in unit testing
If we interpret the question this way, I think the answer should be a resounding 'yes', testers should be involved in unit testing. I mean, there's the word 'testing' in the name, why shouldn't we involve the people who specialize in testing in the process?

What that involvement looks like, exactly, depends on the context, of course. Again, 'being involved' and 'being responsible' are two entirely different things. While I believe that writing unit tests is a development activity, there are a couple of ways in which testers can add value, too:

* They can review the unit tests to learn about what has been covered already, so that they do not repeat that testing later on
* They can review the unit tests to identify what has not yet been covered, and either give that back as feedback to a developer, or add the missing tests themselves
* They can suggest and use techniques like mutation testing to test the tests and find out whether the tests that were written before are actually able to catch meaningful problems

I'm sure there are a few more benefits, but these alone should, in my opinion, be enough for any team to not exclude testers from the process of writing unit tests from here on. And yes, that will require some additional skills from both testers and developers. 

This is where the power of collaboration comes in. I'm a big fan of pair programming and testing, and I've seen a lot of good things come from testers and developers pairing up to write, review and improve unit tests. The developer improves their testing skills, the tester learns more about both the product they're testing and the development process, and in the end, both the entire team and the product itself reaps the benefits.

That's a long answer to a simple question, and I'm sure there are some nuances that I didn't yet unpack, but generally speaking, these are my views on the question of whether testers should be writing unit tests.

Oh, and before you ask 'but what about integration / end-to-end / performance / security / ... tests'? That's easy. Simply replace 'unit tests' and 'unit testing' with 'integration / end-to-end / performance / security / ... tests' and '... testing', and you'll have my answer.

I've always found it a little strange that unit tests have traditionally been seen as 'different' from other types of tests, as if they're some kind of special artefact that only developers know how to write. If that's what you think, too, let me let you in on a little secret: they aren't special.

Unit tests are simply tests that test a small piece of the behaviour of the product that we write, written against and invoking a specific interface of the product: the source code. Other types of tests do exactly the same thing: verify parts of our product behaviour by invoking one or more specific interfaces (APIs, the UI, a database, a queue, ...). They just have a different scope, and with that, they verify behaviour at a different scope. That's all.

This, again, is the reason where I think the traditional test automation pyramid model lacks: I really don't care that much about what is a unit / integration / end-to-end test. All I really care about is [tests that produce valuable information about the state and the behaviour of our product in as efficient a way as possible](/training/valuable-feedback-fast/).

Unit tests really aren't any different.