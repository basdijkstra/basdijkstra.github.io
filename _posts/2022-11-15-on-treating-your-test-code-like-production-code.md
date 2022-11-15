---
title: On treating your test code like production code
layout: post
permalink: /on-treating-your-test-code-like-production-code/
categories:
  - general test automation
tags:
  - development
  - good practices
  - career
---
> "You should treat your test code like you treat your production code"

Hardly a week goes by without someone saying this. In a talk, in a blog post, on LinkedIn, wherever.

And I agree. You _should_ treat your test code like your production code. The problem is, just saying you should doesn't make it happen magically, overnight. Unfortunately, the times when I see the person uttering this statement actually following it up with actionable tips on _how_ to treat your test code like production code are much, much fewer and further between.

So, in this blog post, I'd like to share a few measures that I think every team that actually wishes to treat their test code as a first class citizen should take. Some of them are more 'technical' in nature, some of them focus more on the way the code is written and maintained, but all of them are important, if you'd ask me.

### Version control
This, to me, is a no-brainer. If your tests aren't under version control, they don't exist. Unless you store your tests in Git, or any other version control system you might want to use, there's no way you can effectively work on them together with your fellow team members, and there's no way to make the next point even possible in the first place.

Also, unless you 'like to live dangerously' and you're OK with the risk of losing all of your precious work when your laptop, or whichever machine it is you use to write and run your tests, dies on you, there's no excuse for not using version control.

### Run your tests as part of a build pipeline
Aside from any exploration of your system you do when you write your tests, they are worthless until they are run. And you probably want to run your tests often, ideally on every susstantial change made by your developers. So, why not automate the process of running your tests whenever a developer commits a new piece of code to version control? Or whenever you want to create a build that's ready to deploy into production?

Unless you automate the process of running your tests, you're likely to forget it. So, make those tests part of your pipeline and use them to ensure that the code your team wrote still behaves according to the expectations you codified into your tests. Each. And. Every. Time.

### Use static code analysis and linters
To ensure that the test code your team writes is styled in a consistent manner, I can't recommend using static code analysis and linting tools enough. There's nothing as frustrating as Johnny using camel case naming for variables, and Anna using snake casing in the same project. The only thing that's even less fun is hearing them argue about it all the time. To make sure that everybody follows the same styling guidelines, linting tools are a must.

Also, wouldn't it be great if you had a tool that could help you prevent all kinds of nasty exceptions (null pointers, anyone?) and other erroneous behaviour _while you write your code_? That's where static code analysis comes into play.

While using static code analysis and linting tools might feel painful at first, because of all the errors and warnings you will probably get when you first run them on an existing (test) codebase, I recommend you stick with them nonetheless. You don't need to fix them all, but if you diligently apply the ['boy scout' rule](https://matheus.ro/2017/12/11/clean-code-boy-scout-rule/){:target="_blank"}, over time, your code will become cleaner, making it easier to read and easier to maintain.

There's a plethora of tools that can do static code analysis and linting for you, and some tools do both. I'm quite partial to [StyleCop](https://github.com/DotNetAnalyzers/StyleCopAnalyzers){:target="_blank"} myself, and I use that to keep the [RestAssured.Net](https://github.com/basdijkstra/rest-assured-net){:target="_blank"} code as clean, clear and consistent as I can.

### Test your test code
As testers, we care about quality. Although we are not the keepers of quality, we do what we can to make sure the product we create as a team meets the quality expectations of our users and other stakeholders. Unfortunately, we don't always extend that to the tests that we write. Throughout my career, I've seen and written plenty of tests that were clearly designed using the 'it compiles and shows a green checkmark, what more do you want?' philosophy.

However, tests, too, can suffer from a lack of quality. They can produce false positives, which are annoying but at least make themselves known, but even worse, they can produce false negatives, letting unintended side effects and even blatant bugs slip by unnoticed.

This is why you should test your tests and make sure that the information they produce is valuable and reliable, both when your test passes and when it fails. There are even [tools that can help you do this](https://www.youtube.com/watch?v=iSDJ6iWWvcs){:target="_blank"}, so why aren't you testing your tests yet?

### Apply object-oriented programming principles
In the last 16 years, I've become quite adept at spotting what automation has been written by people who know how to program, as opposed to people who merely learned the API of a tool or library. The difference: structure. While the former produces code that is well-structured and therefore easy to read and easy to maintain, the latter often doesn't go beyond writing tests that procedurally list all the hundreds of different little steps that make up the test. Click here. Type there. Check this box. Uncheck that box.

It does take a while for many people to grow from the latter to the former. And that's understandable. Nobody can learn how to write well-structured code in a day or two. But if you'd ask me where to start, I always come back to learning the [principles of object-oriented programming](/the-four-pillars-of-object-oriented-programming-part-1-encapsulation/). In my opinion, there's nothing that improves your code as quickly as understanding these principles and knowing where and how to apply them. Your future self, and your coworkers dealing with the code you left behind as you moved on, will be forever grateful.

### Write tests together
You've probably heard about pair / mob / ensemble programming. Guess what? You can do that when you're writing tests, too. Actually, you _should_ do that when writing tests, too. There's no better way to write better tests that are better written than having another pair (or a few other pairs) of eyes on them. Plus, you get knowledge transfer for free. What's not to love?

### See and treat yourself as a developer, and your code as a developer would
My final recommendation is maybe the most important one, in my opinion, and all recommendations I've made so far can (sort of) be summarized in this single piece of advice.

_If you're writing pieces of code that verify other pieces of code, that makes you a developer_

There, I've said it.

With this new way of looking at yourself and your work, however, comes great responsibility. If you are a developer (again, which you are), you should behave like one, preferably a good one. This means you should start treating your tests as they deserve to be treated: as code. And yes, that's even true [when you're working with low code tools](https://www.ontestautomation.com/on-codeless-automation-or-rather-on-abstraction-layers/).

I'm sure that the above tips will help you make some decent steps in the right direction.

Now go and treat your test code like your production code!