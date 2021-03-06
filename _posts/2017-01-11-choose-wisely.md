---
id: 1745
title: Choose wisely
date: 2017-01-11T08:00:06+01:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=1745
permalink: /choose-wisely/
categories:
  - General test automation
tags:
  - API
  - integration testing
  - test automation pyramid
---
In <a href="https://techbeacon.com/apis-automated-testing-go-integrated-best-both-worlds" target="_blank">a recent article</a> that was published on TechBeacon, I argued that writing tests at the API level often hits the sweet spot between speed of execution, stability (both in terms of execution and maintenance required) and test coverage. What I didn&#8217;t write about in this article is what motivated me to write the article in the first place, so I thought it might be a good idea to dedicate a blog post to the reason behind the piece.

There really was only a single reason for me to suggest the topic to the people at TechBeacon: because I see things go wrong too often when people start creating automated tests. I&#8217;m currently working with a number of clients in two separate projects, and what a lot of them seem to have in common is that they revert instantly to end-to-end tests (often using a tool like Selenium or Protractor) to create the checks that go beyond the scope of unit tests.

As an example, I&#8217;m working on a project where we are going to create automated checks for a web shop that sells electronic cigarettes and related accessories in the United States. There are several product categories involved, several customer age groups to be considered (some products can be purchased if you&#8217;re over 18, some over 21, some are fit for all ages, etc.), and, this being the US, fifty different states, each with their own rules and regulations. In short, there&#8217;s a massive amount of possible combinations (I didn&#8217;t do the math yet, but it&#8217;s easily in the hundreds). Also, due to the strict US regulations, and more importantly the fines associated with violating these rules, they want all relevant combinations included in the automated test. 

Fair enough, but the problem started when they suggested we write an automated end-to-end test case for each of the possible combinations. That means creating an order for every combination of product group, age group and state, and every order involves filling out three or four separate forms and some additional more straightforward web page navigation. In other words, this would result in a test that would be slow to execute (we&#8217;re talking hours here) and possibly quite hard to maintain as well.

Instead, I used <a href="https://www.telerik.com/download/fiddler" target="_blank">Fiddler</a> to analyze what exactly it was that the web application did in order to determine if a customer could order a given product. Lo and behold.. it simply called an API that exposed the business logic used to make this decision. So, instead of creating hundreds of UI-driven test cases, I suggested to create API-level tests that would verify the business logic configuration, and add a couple of end-to-end tests to verify that a user can indeed place an order successfully, as well as receive an error message in case he or she tries to order a product that&#8217;s not available for a specific reason.

We&#8217;re still working on this, but I think this case illustrates my point fairly well: it often pays off big time to look beyond the user interface when you&#8217;re creating automated tests for web applications:

  * Only use end-to-end tests to verify whether a user of your web application can perform certain sequences of actions (such as ordering and paying for a product in your web shop).
  * See (ask!) whether business logic hidden behind the user interface can be accessed, and therefore tested, at a lower (API or unit) level, thereby increasing both stability and speed of execution.

For those of you familiar with the test automation pyramid, this might sound an awful lot like a stock example of the application of this model. And it is. However, in light of a couple of recent blog posts I read (<a href="https://www.linkedin.com/pulse/test-pyramid-heresy-john-ferguson-smart" target="_blank">this one</a> from John Ferguson Smart being a prime example) I think it might not be such a good idea to relate everything to this pyramid anymore. Instead, I agree that what it comes down to (as John says) is to get clear WHAT it is that you&#8217;re trying to test and then write tests on the right level. If that leads to an ice cream cone, so be it. If only because I like ice cream..

This slightly off-topic remark about the test automation pyramid notwithstanding, I think the above case illustrates the key point I&#8217;m trying to get across fairly well. <a href="https://www.linkedin.com/pulse/test-automation-start-why-bas-dijkstra" target="_blank">As I&#8217;ve said before</a>, creating the most effective automated tests comes down to:

  * First, determining _why_ you want to automate those tests in the first place. Although that&#8217;s not really the subject of this blog post, it IS the first question that should be asked. In the example in this post, the _why_ is simple: because the risk and impact of fines imposed in case of the sale of items to groups of people that should not be allowed to is high enough to warrant thorough testing.
  * Then, deciding _what_ to test. In this case, it&#8217;s the business logic that determines whether or not a customer is allowed to purchase a given item, based on state of residence, product ID and date of birth.
  * Finally, we get to the topic of this blog post, the question of _how_ to test a specific application or component. In this case, the business logic that&#8217;s the subject of our tests is exposed at the API level, so it makes sense to write tests at that level too. I for one don&#8217;t feel like writing scenarios for hundreds of UI-level tests, let alone run, monitor and maintain them..

I&#8217;m sure there are a lot of situations in your own daily work where reconsidering the approach taken in your automated tests might prove to be beneficial. It doesn&#8217;t have to be a shift from UI to API (although that&#8217;s the situation I most often encounter), it could also be <a href="http://www.ontestautomation.com/an-approach-to-test-your-user-interface-more-efficiently/" target="_blank">writing unit tests instead of end-to-end user interface-driven tests</a>. Or maybe in some cases replacing a large number of irrelevant unit tests with a smaller number of more powerful API-level integration tests. Again, as John explained in his LinkedIn post, you&#8217;re not required to end up with an actual pyramid, as long as you end up with what&#8217;s right for your situation. That could be a pyramid. But it could also not be a pyramid. Choose (and automate) wisely.