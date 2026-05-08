---
title: Refactoring the RestAssured.Net code with Claude Code
layout: post
permalink: /refactoring-the-rest-assured-net-code-with-claude-code/
categories:
  - Artificial Intelligence
tags:
  - RestAssured.Net
  - refactoring
  - Claude Code
---
As some of you might know, the workshops and training courses I run, the talks I do and the blog posts I write tend to focus on fundamental sofware testing, software development and test automation skills, rather than [focusing on the latest technology and trends](/some-perspective-on-testing-trends/). I just don't 'do' trends very well. I do, however, read a lot of what others think about and write about with regards to these technologies, as I am an independent consultant and therefore I simply cannot afford _not_ to stay on top of what is happening in tech.

Until now, I have pretty much avoided spending a lot of time using AI tools myself, other than using ChatGPT to help me build a custom training plan for my endurance cycling training. That is, until I heard more and more folks talking about [Claude Code](https://code.claude.com/docs/en/overview){:target="_blank"}, and how good they thought it was, especially with the new Opus 4.6 model. That triggered me to find out for myself if it was truly useful, and this blog post is probably the first of a few where I document my thoughts and findings.

Of course, I could start with building (or 'vibe coding', as the cool kids say) something from scratch, but I don't think that is a particularly good way to find out what AI can really do for me. After all, I am not in the business of building new things that didn't exist before, I'm in the business of helping people perform existing and important tasks, in my case, testing and automation, in a better and more efficient way.

## The goal
I've been working on [RestAssured.Net](https://github.com/basdijkstra/rest-assured-net){:target="_blank"} for some 4 years now, and over time, the number of features has grown, as has the code base itself. As a result of tacking on more and more features, some of the classes in the project have grown to be very large, way too large, even, and those classes typically also have many different responsibilities.

I have been meaning to refactor them and extract different pieces of logic into their own classes to make the code easier to understand and to maintain, but in all honesty, I sometimes can't see the forest for the trees anymore. So, it would be great if Claude Code could help me do that.

## The guardrails
One thing I've learned from my limited use of AI and from a lot of reading about other people's experience with AI is to have strict guardrails in place before you're letting an AI agent loose on your codebase. Since I don't have a lot of experience with AI under my belt yet, I reckon it is a good idea to take small steps and remain in control of the process. I mean, _I_ am responsible for the RestAssured.Net codebase, so I want to know what happens to it, and make sure I understand everything that is happening to it.

So, as a first guardrail, I'm not letting Claude Code touch the tests. The [acceptance tests for RestAssured.Net](https://github.com/basdijkstra/rest-assured-net/tree/main/RestAssured.Net.Tests){:target="_blank"} act as a safety net for me when I fix bugs and add new features, and I write them with intent and [take good care of them](/improving-the-tests-for-rest-assured-net-with-mutation-testing-and-stryker-net/). As the task I set out to do is a 'pure' refactoring task, that is, I only want to change the code structure, not its behaviour, the tests should remain intact to prove that the refactoring was successful.

Second, I am doing a thorough review of every change that is made by Claude before I bring it under version control. My goal with this experiment, and with using AI in general, is not to outsource my thinking (thank you, [Fiona Charles](https://www.linkedin.com/in/fionacharles/){:target="_blank"}), but rather to enhance my capabilities. As I said earlier, ultimately, I am the one responsible for the code and the changes made to it, not Claude. This is also why I don't offload running the tests and bringing the code under version control to Claude.

Third and final, I'm going to take small steps. I have seen plenty of (horror) stories of people letting LLMs go for hours writing code, without intermediate scrutinizing of the changes and suggestions made, with results that ranged from the mildly amusing to the outright horrifying. I don't claim that this code base is as important as, for example, the code in an online banking platform, but it has grown to a decent user base of the years. I don't want to let those people down. And who knows, some of them might use the library to test those online banking platforms, so it's my responsibility to only publish versions of a product that I feel is fit for that job. There's no place there for code I don't understand and that might give people a false sense of security.

## The first prompt
After purchasing a Pro subscription to Claude Code, setting it up and having it initialize [the CLAUDE.md file](https://github.com/basdijkstra/rest-assured-net/blob/main/CLAUDE.md){:target="_blank"} for the project, I first asked Claude to analyze the `ExecutableRequest` class (a class that's been bothering me for a while because of its size and complexity) and suggest me improvements. I did that with this specific prompt:

> The ExecutableRequest class is quite long, with a number of different responsibilities. Analyze it and suggest improvements to the code structure without changing the behaviour. List your top 5 recommendations, together with impact on code quality and reasons for your prioritization.

I don't want Claude to make changes yet, I want it to suggest me changes, and also, and more importantly perhaps, tell me _why_ it thinks these improvements make sense. This should give me the information I need to make an informed decision on whether to proceed with the suggestion.

Opus 4.6 is slower than many other models out there, but the output is supposed to be much better. And indeed, after some deliberation, Claude produced a list of suggestions for improvement that at first glance, made a lot of sense. It's #1 recommendation was to extract the logic to create a request body into a separate class called `RequestBodyFactory`. Not necessarily perfect as a class name, but as I couldn't think of anything better at that moment, I asked Claude to proceed with doing the actual refactoring.

And so it did, and I have to say, it did not disappoint. It created the new class without problems, moved the logic in there, changed the `ExecutableRequest` class to use the methods in the new `RequestBodyFactory` class, and it even followed all the styling and formatting [requirements set by StyleCop](https://github.com/basdijkstra/rest-assured-net/blob/main/CLAUDE.md#code-style){:target="_blank"}. That last point is very important, because I set the styling rules to level 'nuclear', as in, even the slightest infraction will make the code fail to compile.

The ultimate test of the work done by Claude, though, was running the tests, and those all passed, too. Which makes sense, as I asked for a change of the code structure, without changing the behaviour. [My tests](https://github.com/basdijkstra/rest-assured-net/tree/main/RestAssured.Net.Tests){:target="_blank"} test the library for behaviour, not implementation, so this is exactly what I expected.

The only thing left for me to do was to review the changes made by Claude before committing them to version control. As I said at the start of this post, ultimately it is me who bears responsibility for the code, so I want to be able to read and understand it, even when I didn't write it myself.

Overall, the changes made by Claude looked pretty good, but there was one thing I didn't like. The newly created `Create()` method in the `RequestBodyFactory` class had a _lot_ of arguments (9, if I remember correctly). So, naturally, I asked Claude if it could further improve that. It came back with a suggestion to group several properties related to request body settings in a custom `RequestBodySettings` type and then pass that object in as an argument. As I thought this did improve the readability of the code, I asked Claude to proceed with the change. Again, code compiled, tests passed, all good.

After that, I saw no further reasons to change or improve the work done by Claude, so I thought the code was safe to [commit](https://github.com/basdijkstra/rest-assured-net/commit/ceb79e644902984361303da6e15137f858f459cb) and push. My build pipeline then took care of verifying that all tests run and pass on all .NET versions that RestAssured.Net supports. Again, no issues here.

## So, what did I learn?
Did this experiment teach me a lot of new things? Well, not necessarily. What it did, though, was reinforce my initial thoughts on what constitutes prudent use of AI in software development and software testing. It also showed me that Claude is both a really powerful and a user-friendly tool, at least when used on a codebase and for a task of this, admittedly small, size.

In short:
* Software like Claude is a great tool for refactoring and code improvement tasks like the one described in this blog post
* Having solid guardrails in place (linters, tests, review before commit) is essential if you want to retain control of the software that is being written by AI
* At least initially, I would lean towards having AI support you in writing product code, and leave writing tests and reviewing the results to human beings

With regards to that last point, I know that not everybody will agree with this. I have seen a lot of examples of AI writing tests, and of AI taking care of your code reviews. Personally, I'm not ready yet to hand over that kind of control to a piece of software that I do not fully understand or trust. At least, not without keeping a human being (myself, for example) in the loop. If you decide otherwise, that's fine with me, as long as you can handle the responsibility, and are ready to deal with the potential fallout, that comes along with it...

In the meantime, I will continue improving the RestAssured.Net code using Claude and other tools, as there's a lot of room for improvement left. And I think I'll stick to keeping the current guardrails in place, too. It might be slightly slower, but it will be a lot safer, too.