---
id: 2025
title: Why I think unit testing is the basis of any solid automation strategy
date: 2017-09-20T10:00:27+02:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=2025
permalink: /why-i-think-unit-testing-is-the-basis-of-any-solid-automation-strategy/
categories:
  - General test automation
tags:
  - test automation pyramid
  - unit testing
---
In <a href="https://www.ontestautomation.com/why-and-how-i-still-use-the-test-automation-pyramid/" target="_blank">a recent blog post</a> I talked about why and how I still use the test automation pyramid as a model to talk about different levels of test automation and how to combine them into an automation strategy that fits your needs. In this blog post I&#8217;d like to talk about the basis of the pyramid a little more: unit tests and unit testing. There&#8217;s a reason -or better, there are a number of reasons- why unit testing forms the basis of any solid automation strategy, and why it&#8217;s depicted as the broadest layer in the pyramid.

**Unit tests are fast**  
Even though end-to-end testing using tools like Selenium is the first thing a lot of people think about when they hear the term &#8216;test automation&#8217;, Selenium tests are actually the hardest and most time-intensive to write, run and maintain. Unit tests, on the other hand, can be written fast, both in absolute time it takes to write unit test code as well as relative to the progress of the software development process. A very good example of the latter is the practice of Test Driven Development (TDD), where tests are written before the actual production code is created.

Unit tests are also fast to run. Their run time is typically in the milliseconds range, where integration and end-to-end tests take seconds or even minutes, depending on your test and their scope. This means that a solid set of unit tests will give you feedback on _specific aspects_ of your application quality much faster than those other types of tests. I stressed &#8216;specific aspects&#8217;, because while unit tests can cover ground in relatively little time, there&#8217;s only so much they can do. As goes for automation as a whole.

**Unit tests require (and enforce) code testability**  
Any developer can tell you that the better structured code is, the easier it is to isolate specific classes and methods and write unit tests for them, mocking away all dependencies that method or class requires. This is referred to as highly testable code. I&#8217;ve worked in projects where people were stuck with badly testable code and have seen the consequences. I&#8217;ve facilitated two day test automation hackathon where the end goal was to write a single unit test and integrate it into the Continuous Integration pipeline. Writing the test took ten minutes. Untangling the existing code so that the unit test could be written? Two days MINUS ten minutes.

This is where practices like TDD _can_ help. When you&#8217;ve got your tests in place before the production code that lets the tests pass is written, the risk of that production code becoming untestable spaghetti code is far lower. And having testable code is a massive help with the next reason why unit testing should be the basis of your automation efforts.

**Unit tests prevent outside in test automation (hopefully)**  
If you&#8217;re code is testable, it means that it&#8217;s far easier to write unit tests for it. Which in turn means that the likelihood that unit tests are actually written increases as well. And where unit tests are written consistently and visibly, the risk that everything and its mother it tested through the user interface (a phenomenon I&#8217;ve seen referred to as &#8216;outside-in test automation&#8217;) is far less high. Just writing lots of unit tests is not enough, though, their scope, intent and coverage should be clear to the team as well (<a href="https://www.ontestautomation.com/on-crossing-the-bridge-into-unit-testing-land/" target="_blank">so, testers, get involved!</a>).

**Unit tests are a safety net for code refactoring**  
Let&#8217;s face it: your production code isn&#8217;t going to live unchanged forever (although I&#8217;ve heard about lines of COBOL that are busy defying this). Changes to the application, renewed libraries or insights, all of these will in time be reason to refactor your existing code to improve effectivity, readability, maintainability or just to keep things running. This is where a decent set of unit tests helps a lot, since they can be used as a safety net that can give you feedback about the consequences of your refactoring efforts on overall application functionality. And even more importantly, they do this _quickly_. Developers are humans, and will move on to different tasks if they need to wait hours for feedback. With unit tests, that feedback arrives in seconds, keeping them and you both focused and on the right track.

In the end, unit tests can, will and need not replace integration and end-to-end tests, of course. There&#8217;s a reason all of them are featured in the test automation pyramid. But when you&#8217;re trying to create or improve your test automation strategy, I&#8217;d advise you to start with the basis and get your unit testing in place.

By the way, for those of you reading this on the publication date, I&#8217;d like to mention that I&#8217;ll be co-hosting <a href="https://blog.testim.io/webinar-cracking-your-test-automation-code-your-path-to-cicd/" target="_blank">a webinar with the folks at Testim</a>, where I&#8217;ll be talking about the importance of unit testing, as well as much more with regards to test automation strategy. I hope to see you there! If you&#8217;re reading this at a later date, I&#8217;ll add a link to the recording as soon as it&#8217;s available.