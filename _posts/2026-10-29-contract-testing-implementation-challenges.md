---
title: Contract testing implementation challenges
layout: post
permalink: /contract-testing-implementation-challenges/
categories:
  - Contract testing
tags:
  - Pact
  - CDCT
---
_This post was previously published through my [newsletter](/newsletter/) on October 19, 2026. From time to time, I will republish newsletter issues on my blog here if I think people (and search engines) might benefit from it. If you want to read everything I've written once it is posted, I recommend signing up for the newsletter._

For the last 5-6 years or so, I've been talking about, teaching and helping teams implement contract testing on a regular basis. To make that more concrete: at the moment, I'm having ongoing conversations with four different potential clients about helping them in some way with their contract testing implementation, and so far, I've run [my contract testing workshop](/training/contract-testing/) four times this year.

It probably doesn't come as a surprise to you when I say that I really like [the concept of contract testing](/an-introduction-to-contract-testing-part-2-introducing-contract-testing/) as a means of addressing integration testing challenges in distributed software systems. However, no matter if I'm speaking, teaching or implementing contract testing, certain challenges almost always come up at some point, and I want to highlight some of those today.

### Understanding the concept of contract testing
First of all, it often takes people a while to understand the concept of contract testing, especially when they're used to what I call the 'traditional' approach to integration testing: deploying the systems and services in scope to a shared testing environment and then testing them 'as a whole'. Of course, the more distributed your system and your development process is, and the higher the deployment frequency, the more complex and the slower this integration testing approach is going to be.

The premise behind contract testing is to break that integration testing puzzle down into small chunks of individual consumer-provider pairs, and then test the integration between those two in an asynchronous manner. By asynchronous, I mean that there is no need to deploy consumer and provider into a shared environment; both parties perform their part of the process in their own, local development environment. This means you can detect potential integration issues before you deploy, instead of stumbling onto them after you do so.

This makes contract testing (or contract-based integration testing, to use its proper name) a significantly different approach to integration testing compared to the more 'traditional' approach. In my experience, it usually takes people time to wrap their head around how the process actually works, which steps you should take and in which order, and what you (do not yet) know after each step. It often takes going through several contract testing cycles before people start to 'get it'. That's not their fault, that is to be expected, since the contract-based approach to integration testing is, again, quite different from what they've been doing for so long.

To better cater to this, I redesigned my contract testing workshop a year and a half or so ago. It is now a full-day workshop (half a day is simply too short), and I take much smaller steps and repeat them more often. From what I see, this has led to a better understanding of the concepts with my audience, and that's the whole point.

### Contract testing === Pact
I've talked to quite a few teams that had heard of contract testing somewhere, decided that that was the way out of their integration testing problems, and then immediately started writing contract tests with [Pact](https://docs.pact.io){:target="_blank"}. Understandable, because Pact is a very useful, very powerful and very popular tool, plus, it's open source, which makes it easy for teams to get started.

Often, they skipped a step in the process. Pact is very much a consumer-driven contract testing tool by nature, and while consumer-driven contract testing is the richest and most powerful of [the different approaches to contract testing](/approaches-to-contract-testing/), it is not necessarily the best fit for every team and every context. It's a classic example of using the wrong tool for the problem at hand, because they didn't take enough time looking at the problem and the available solutions.

That's not the problem of Pact, even though it is often framed that way ('Pact didn't solve my problems, therefore Pact is not a good tool'). That's a 'we didn't fully understand the problem and the context' problem.

### Contract testing removes the need for communication
Another challenge that I often see when I talk about or help teams with contract testing is the fact that teams don't talk to each other. This then leads to integration issues, because team A expects something from team B that team B doesn't supply. Contract testing is not the solution to these problems. It can't be - tools alone do not solve problems, let alone communication problems.

What contract testing _does_ do is make potential integration issues visible early on in the development process, often earlier than (again) more 'traditional' approaches to integration testing. _That's_ where the value of contract testing can be found. It is not a means to replace communication between teams. In fact, and this is especially true for consumer-driven contract testing, contract testing cannot succeed without good communication between consumer and provider teams.

### Contract testing replaces all other types of integration testing
This one comes up less often than it used to, but sometimes, I still hear people saying that when they will have contract-based integration tests in place, they don't need other types of integration tests anymore - including E2E tests. That's simply not true.

While contract testing is a great way to find out earlier about the impact of a change in a specific consumer or provider further downstream, in many cases, I wouldn't rely on these tests alone before moving to production.

Because of its asynchronous nature, contract-based integration testing only verifies whether a consumer and a provider speak the same language - can a consumer understand the message returned by a provider in a specific situation? It does not verify that the request or the response message reaches the other party in the first place, for example. In other words, it doesn't test connectivity between a consumer and a provider.

And because integration testing is broken down into individual consumer-provider pairs, you don't see the entire system working 'as a whole'. This means you might want to keep a few end-to-end tests in place, even when you've got good contract testing coverage across the board.

The above is not a complete list of challenges with contract testing, but the ones I mentioned here are those that come up most often.