---
title: Are we solving the right problems in test automation? Part 1
layout: post
permalink: /are-we-solving-the-right-problems-part-1/
categories:
  - General test automation
tags:
  - problems
---
2021 marks my 15-year anniversary in the testing and test automation field. Fifteen years in which I made a lot of mistakes, learned a lot, tried to improve the quality of the work I did and (more recently) also started to teach others how to take their first or their next steps in our interesting field of software testing and automation.

The end of a year and the start of a new one is traditionally a good moment to reflect on the past and to look forward to what the future is going to bring, and this year's festive season for me was no different. And not only have I been reflecting on the past year (more on that in [this post](/2020-a-year-in-review/)), but also more and more on my entire career so far, as well as on the way our field and craft has developed.

And the longer I thought about it, the more a single, specific question came to mind:

> Are we solving the right problems in test automation?

There's no doubt that, when done well (and that is a big 'when') test automation can be incredibly valuable. There's also no doubt that the demand of our customers for 'quality at speed', and the ever shorter release cycles that go with this demand, require development teams to significantly invest in test automation, much more so than was the case 15 years ago, when I started my career in test automation.

Back then, a lot of time was typically spent on the automation of regression tests, with the primary goal being to 'automate away the boring stuff'. Often, that lead to incredibly complex, long-winded automation that more often than not did not deliver enough value to warrant the investments done to create the automation. Or maybe that was only the case in the projects I was involved in...

Today, the general view on test automation, and the expectations that teams and organizations have of automation, has improved, at least that's the case for a lot of clients I've had the pleasure of working with recently. Rather than 'automating away all of the regression testing' and 'replacing those pesky, expensive testers', we've come to adopt more realistic goals and expectations for test automation, like 'using tools to support our testing activities' and 'shortening the feedback loop'.

Generally speaking, I think that a lot of folks today have a healthy and reasonable view of what test automation can and cannot do for you. People like [Michael Bolton](https://www.linkedin.com/in/michael-bolton-08847/){:target="_blank"}, [Richard Bradshaw](https://www.linkedin.com/in/friendlytester/){:target="_blank"}, [Alan Page](https://www.linkedin.com/in/a-l-a-n/){:target="_blank"}, [Angie Jones](https://www.linkedin.com/in/angiejones/){:target="_blank"} and many, many others (too many to list here!) have done excellent work in improving the view on and expectations of test automation that now lives in our community, and beyond it, too. That's one big problem addressed, I think. Sure, there still are people that expect automation to be a magical wand that you can wave to solve all testing and quality problems, instantly, and I don't think that that breed of people is ever going to go extinct. However, at least we have plenty of materials in the form of articles, blogs, talks and more to try and help them change their perspective these days.

Where I do feel we are still lacking, and often quite severely, is in the way test automation is *implemented*. Saying that automation, when done well, will be of value is one thing, but what defines 'done well'? How do you 'do' automation well? That's where I think we still have a long way to go, and that's where I'd like to spend more time and effort working on in the coming year(s).

This is not about answering questions like 'what programming language should I use for my automation?' or 'what does a good Page Object look like?'. Those are important questions, too, at some point, but only after other and I believe more fundamental, problems have been properly addressed. Also, there's plenty of good content out there, too, that can help you answer these questions. You'd only have to go through blog aggregators like [Testing Curator](http://blog.testingcurator.com/){:target="_blank"} or online training platforms like [Test Automation University](https://testautomationu.applitools.com/){:target="_blank"} to find a wide range of content focused on learning more about and answering this practical, hands on category of questions.

But somewhere between the high (board room?) level on test automation I talked about earlier, and the practical implementation questions we have just discussed, exists a space where another type of problems exist, and those are the problems I don't think are addressed enough. And if they are addressed, the way in which they are is too often lacking.

To illustrate what I mean, in this blog post and in a couple of upcoming ones, I want to take a look at some of the challenges that I believe are common in test automation these days, how certain people perceive that problem and what they think the solution is, and what I think the actual problem really is, instead. In follow-up posts that I hope to write later this year, I'll try to give practical, hands on advice to help teams and organizations address these problems in the way I think they should be.

For today, let's start by taking a look at the first of these problems that I think are not yet solved well enough in our field.

**Challenge**: *How do we close the existing gap between the current number of people skilled in automation and the number of skilled people required?*

**Perceived solution**: *We need to create more tools that make it easier for people to start implementing test automation.*

Not a lot of weeks go by where I'm not introduced to a new tool or framework, either indirectly through a blog post or LinkedIn status update that I read, or more directly by either the creator of a new tool or (if they've been around a little longer or have more budget) by the marketing department representing the creator of that tool. When I ask them what problem they're trying to solve with their tool, and how that differs from the thousands of tools that are already available today, they typically tell me that their tool is easier to get started with, easier to learn, you don't need to learn how to code, and so on. And while you can't find fault with their goal of making the automation space a better place, this type of tool (or framework) will often have you start running into troubles when you're trying to use them at scale, especially when they're adopted and used by people less experienced in software development and automation.

At first sight, often, all seems to go well. It looks like indeed, it is easier to get started with these tools, and yes, you can create your first tests without having to write any code or even think about applying proper software development patterns. But once your test suite grows, issues such as the following will often start to occur:

* Because it is so easy to start creating test suites, even for people with less experience in automation, there's no need at first to think about reusability and a modular design. This won't be a problem right away, but at some point, you're starting to paint yourself into a corner, because you've ended up with a very large test suite that takes ages to maintain.

* When tests start failing (and at some point, they will), identifying what caused the failure and, more importantly, how to fix it, is a difficult task. Often, the solution is then to simply disable the tests, effectively throwing away all the time and effort spent creating these tests earlier.

* It turns out that the proprietary format in which the tool stores your test suite does not play nice with version control, because everything is lumped together in a single 'project file'.

And I could think of a couple more...

These issues, sadly, are often not addressed in the marketing content that is used to promote these new tools and frameworks, presumably because that makes using these tools sound difficult and 'not cool', (or maybe because nobody used them long enough to actually run into these issues, but I don't want to sound too cynical...). However, these are very important questions to have answered before you start using a new tool, but I'm not so sure that they are asked often enough.

My suggestion to start solving this challenge is therefore to not only keep _saying_ that test automation is software development (an adage I hear a lot and fully agree with), but also to start _acting_ on it more often.

Instead of developing and marketing more tools and frameworks that claim to 'make automation easier', why don't we recognize that test automation requires software development skills and act accordingly, either by training automation engineers to become more proficient in general software development (and not just in knowing how to use a wider range of features of a single tool), or by finding a way to get developers more involved with test automation. They are the ones who already have the development skills, after all..

By the way, this doesn't mean that I think all test automation should be created directly in code (although it is by far my personal preference these days). There are some great tools out there that provide 'low code' abstraction layers on top of the code, most notably [Robot Framework](https://robotframework.org/){:target="_blank"}, but there are others, too.

My point is that even if you use tools from this category, you still need to think like a developer (i.e., have development skills) if you want to create tests that will scale and be readable, maintainable, easy to collaborate on and that can be successfully integrated in version control and CI/CD pipelines.

Failing to realize this and act on it, you'll likely at some point end up with a large test suite that is hard to maintain, fails often and can only be (sort of) operated by those that have been working with it for a long time. It's only a matter of time before test automation efforts come to a grinding halt, and more often than not, the tool is blamed for the failures, where the actual fault lies with the way it has been implemented..

I think it is due time that we should stop being wooed (and fooled) by test automation tools claiming you don't need to think like a developer to use them, and that we start acting on the 'software development' part of 'test automation is software development'.