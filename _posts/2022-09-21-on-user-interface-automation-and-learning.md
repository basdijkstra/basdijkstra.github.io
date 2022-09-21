---
title: On user interface automation and learning
layout: post
permalink: /on-user-interface-automation-and-learning/
categories:
  - learning
tags:
  - user interface automation
  - career
---
>> "What would be the best way to start learning automation?"

That is a question I get asked quite often, and my answer to it is typically quite extensive. So extensive, I even wrote [a free short ebook](https://huddle.eurostarsoftwaretesting.com/resources/test-automation/a-test-automation-learning-path/){:target="_blank"} around it. I don't think there's necessarily a _best_ way to learn automation, there's one way I definitely do NOT recommend.

Unfortunately, it is the way a lot of people still choose when they're taking the first steps on their automation journey, although I'm not sure if it's actually a conscious decision. It's also the road I took when I first started out in automation back in 2006.

The first couple of projects I worked on could pretty much be summarized in a single sentence:

>> "We want you to automate our existing regression tests"

Now, I've written about [how I feel about automating regression tests](https://www.ontestautomation.com/on-ending-the-regression-automation-fixation/) a while ago, so I'm not going to repeat that here. I do want to zoom in, however, on something that is closely related to that post: the amount of time and effort that people invest into their user interface-driven automation. More specifically, I want to take a closer look at why this is often the starting point for people who are new to automation.

To some extent, it is understandable that people start their automation efforts with this specific type of automation. I can think of a number of reasons:

* It is what people know. As testers, we are used to interacting with application through the graphical user interface. Therefore, it is only logical to start our automation efforts there, right?
* As written in the blog post referenced above, teams often start their automation efforts with 'automation the regression tests'. Since these are typically written from an end user perspective, they reference the GUI a lot.
* Because a lot of automation is unfortunately still often written (way) after the application code, there's not always a lot of thought put into testability. As a result, the UI is often the _only_ interface available for automation.

However, GUI automation is challenging. In fact, it is, by far, the hardest form of automation there is.

A major reason for this, and one I don't see being discussed that often, is that the graphical user interface is **the only interface in an application that is explicitly written to be consumed by human beings**.

All other interfaces in an application (be it a database, an API or the code itself) is consumed by or interacted with by computer processes (a database client, an API client, other code, ...). The very fact that these interfaces are designed to be consumed by machines makes them inherently easier to automate.

By contrast, the graphical user interface is meant to be consumed by or interacted with by human beings. That's the sole reason of existence of a GUI: to make it easier, or even possible, for human beings to interact with systems.

However, machines are not human beings, and human beings are not machines. Trying to replicate how a human interprets and interacts with an interface by means of a machine (your automation) isn't easy, as we've all learned by now.

That's not the fault of the tools that are available. There are many, many good tools out there to support GUI automation, both in the open source space as well as commercially licensed tools.  No, it's the very nature of the interface and its target consumer audience that I feel makes it the most complex and therefore often the worst candidate for automation.

That doesn't mean that you shouldn't have GUI automation _at all_, but I think it does warrant being much more careful what to automate at this level, and it does make me wonder why so many of us start our automation learning journey there..

Another reason that makes UI automation the hardest form of automation out there is the fact that these tests are often what I refer to as 'full stack' tests, i.e., they exercise the entire stack in an application. Tests typically invoke the UI itself, APIs, business logic, databases, external systems, ... All these moving parts make UI-driven automation hard to set up and slow to execute.

To a large extent, this can be mitigated by mocking out parts of the system that do not play an integral role in providing the information that the test is supposed to uncover. I am a big fan, for example, of testing your user interface through unit tests.

Frameworks like React allow you to test UI logic and components in isolation from the rest of your code, for example. Alternatively, you can decide to mock the API (and everything behind it) that's called from your UI, and use tools like Selenium or Playwright to (sort of) test your UI in isolation, too.

This does require a certain level of testability, though, as well as some knowledge of and experience with mocking as an automation engineer, and these things aren't covered in most courses on UI automation, nor in most automation learning journeys.

This again leads to a lot of relatively inexperienced automation engineers frantically trying to automate everything in the hardest way there is: by writing full stack tests through the graphical user interface of an application. It is certainly the way I 'did' automation in the first years of my career, and it is the way I still see a lot of people approach automation when they enter the field.

Coming back to the question I mentioned at the start of this post, I'd like to advocate for a more holistic way of learning automation. UI automation can be (should be) part of it, but it definitely should not be the starting point, nor should it even be a very large part of an automation learning journey.

As I've written in the ebook I referenced at the beginning of this post, there's more, much more to becoming a skilled in automation

This includes, but is definitely not limited to

* becoming a better software tester. Recommendation: [the Rapid Software Testing courses](https://rapid-software-testing.com/){:target="_blank"}.
* becoming a decent programmer. Recommendation: practice, practice, practice, have your work reviewed by others, then practice some more.
* augmenting your learning by automation courses that go beyond learning tricks with specific tools. Recommendation: apart from [my own courses](/training/), have a look at courses like [Automation in Testing](https://automationintesting.com/){:target="_blank"} for example.

Your goal should not be to become the best or the most knowledgeable about a specific tool or technique. I think that's rather pointless. Your preferred tool or technique will not be useful in all situations, and tools and techniques come and go, anyway.

Instead, try and learn enough to be able to have meaningful conversations about software testing, automation, programming and testability. Once you get there, the rest will come. Not automatically, it will still be a lot of hard work and time invested, but the ability to understand what people talk about when they talk about these topics is, to me, the foundation to long-term success in this field.

Again, _some_ user interface automation might be part of that, but there's more to uncover. Much, much more.