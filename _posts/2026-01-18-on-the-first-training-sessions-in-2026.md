---
title: On the first training sessions in 2026
layout: post
permalink: /on-the-first-training-sessions-in-2026/
categories:
  - Training
tags:
  - BDD
  - contract testing
  - Playwright
---
Earlier this week, I ran my first two [training](/training/) sessions of 2026. Running these sessions reminded me (once again) that training really is what I enjoy the most and what I do best. Working with a group of engineers, introducing them to new techniques, patterns, principles and tools, and exploring and discussing how these could help them improve their automation, testing and development efforts to get closer to the end goal of [valuable feedback, fast](/training/valuable-feedback-fast/) is a very rewarding thing to do.

On Tuesday, I facilitated a full-day course covering the 'textbook' Behaviour-Driven Development process. In this course, we took a change to an existing feature for [the world's least safe online bank](https://parabank.parasoft.com){:target="_blank"} from idea to working to automated acceptance tests. Along the way, we discussed and practiced Example Mapping, reviewed and wrote Gherkin scenarios, and took a quick look at the tools that support BDD, in this case [Cucumber](https://cucumber.io/docs){:target="_blank"}.

As a course topic, BDD is a bit of an outlier for me. Most of the workshops I run cover topics that are related to test automation, yet in this workshop we cover the BDD process and methodology as a whole, and not _just_ talk about Cucumber and Gherkin. Tuesday reminded me how fun it can be to talk about and practice BDD, though, so I do hope I get to run this workshop a couple more times in 2026. I've got at least one more planned, so that's a start.

The next day, I ran a full-day workshop on [contract testing](/training/contract-testing/) with Pact in Java. I quite recently redesigned this workshop, because I felt that the way I taught it before didn't really do justice to the complex topic that is contract testing. Before, I often tried to cram it into a half-day session, which is just too short, and therefore had to limit myself mostly to writing and running consumer-driven contract generation tests on the consumer side and verify contracts at the provider side. If there was time left, we talked a little bit about [challenges of consumer-driven contract testing and how bidirectional contract testing tries to address those challenges](/approaches-to-contract-testing/), but that was about it.

In the new format, I'm taking more time, using more and smaller steps, to take people through the flow of both consumer-driven and bidirectional contract testing. There's some coding involved, of course, but not an awful lot. The biggest change, I think, is that I now focus on the process flows rather than just the tools. For example, we now use a Pact Broker from the very start, and we use tools like [can-i-deploy](https://docs.pact.io/pact_broker/can_i_deploy){:target="_blank"} throughout the workshop, too. From the initial feedback I gathered, this helps people a lot in understanding what contract testing really is, how it works and how it tries to answer the question of

> "Are all individual components and services able to communicate with one another?"

So, in short, 2026 is off to a good start when it comes to my training business. As my goal for the year is to grow that training business revenue with 20% compared to 2025, [while staying away from LinkedIn and social media in general](/time-for-another-linkedin-break/), I'm happy to see that next to the two I delivered this week, I have three more days of training scheduled for January, all three on [Playwright](/training/workshop-playwright/). That's a big improvement over last year, when my training sessions didn't really start until early March. Needless to say, I hope that this trend will continue throughout the year. I'll keep you updated.