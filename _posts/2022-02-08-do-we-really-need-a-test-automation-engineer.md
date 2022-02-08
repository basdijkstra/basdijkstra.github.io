---
title: Do we really need a test automation engineer?
layout: post
permalink: /do-we-really-need-a-test-automation-engineer/
categories:
  - test automation
tags:
  - test automation engineer
  - teams
  - software development
---
_As part of a workshop engagement with Sweden-based consultancy company [tretton37](https://tretton37.com/){:target="_blank"}, I recently did a talk titled 'Do we really need a test automation engineer?', followed by a discussion and Q&A with their CTO, [Martin Mazur](https://se.linkedin.com/in/mazur){:target="_blank"}. You can watch the entire session [here](https://www.youtube.com/watch?v=B2o2YFq6C2Y){:target="_blank"}._

_For those of you that are more a reader than a video watcher, I turned this talk into a blog post. It's not a word-by-word transcription of the talk, but it does cover the most important points I've tried to make in the talk. By the way, I got this idea of talk-to-blog-post from [Maaret Pyhäjärvi](https://maaretp.com/){:target="_blank"}, so all credits for the concept go to her._

Before I joined [Inspired Testing](https://www.inspiredtesting.com/){:target="_blank"} as their Director of [Academy](https://www.inspiredtesting.com/academy){:target="_blank"}, I've been a test automation engineer and consultant for some 15 years. During these years, especially earlier in my career, I've been working on a lot of regression automation. Companies that wanted to save time on the execution of their regression tests relied on me (and others like me) to convert their test scripts, written down in Excel, Word, Confluence, or some other format, into automation that would replicate the actions and checks described in these test scripts.

Progress in these projects was often measured through the amount of test scripts we converted from 'manual' to 'automated'. Often, intricate ROI calculations were made, describing how often these automated scripts would have to be run until it was considered 'more efficient' (read: cheaper) to automate them as opposed to having them executed by humans.

The results? More often than not they were disappointing, and sometimes downright horrible. Early in the automation process, good progress was made, and everybody seemed to be happy with the automation efforts. A big reason for this was probably that we tended to start with the low-hanging fruit, i.e., the scripts that were relatively easy to automate (testing login forms, anybody?).

As time progressed, more scripts were added, often more complex in nature, and that's where and when things almost invariably started to turn south. More time was spent on keeping the existing tests running, trying to refactor the code to keep it somewhat reusable and maintainable, all leading to less time available to add new tests.

It's been a while since I've been a test automation engineer, but I still see this approach to automation a lot, both with the teams and organizations I've worked with as a consultant and trainer, but also when I read stories about automation online.

It took me a while (slow brain...) but over time I've come to the conclusion that this 'automate all the regression tests' approach to automation is a flawed one. Most of the time, it just doesn't work, and when it works, it does so in a pretty inefficient manner, which basically means it doesn't work, either. Not exactly what we're looking for when we apply automation to our testing efforts.

The reason why I think this approach doesn't work?

_Because these regression test scripts were written by humans, for humans._

By their nature, these regression test scripts have been written by humans, to be executed by humans each time a new version of the software is deployed, to see if there are no unintended side effects introduced by the latest changes. So far, so good. I've got no problem with regression testing at all. Making sure that software keeps working as intended is a really, really good idea.

However, trying to turn these scripts written by humans into automation later on is often very, very inefficient. I see two reasons for this inefficiency.

First, with this way of working, **automation happens (way) too late**. If you only start automating specific interactions with an application after the software has been written, sometimes months after the fact, it's unlikely that testability and automatability will be at the required level.

This means that you'll often need to 'make do' with what you've got in terms of hooks into the application, ways to set and control state, observability of application behaviour, etc. Efforts to refactor an application to increase testability are not often put high on the priority list, simply because it's really hard to express the business value of that. Your customers would rather see that latest feature added, and rightly so. 

Second, because they're written by humans, for humans, **regression test scripts often focus heavily on GUI-based interaction** with the application under test. Understandably so, because the GUI is the only interface of an application that is explicitly created for humans beings to consume. That unfortunately makes the GUI by far the hardest interface to write your automation against.

Lower-level interfaces (APIs, queues, databases, methods and classes in code, ....) are written to be consumed by other software components, which makes them much easier to automate (remember: automation is software). Simply translating what a human being does, as written down in a regression test script, into software that performs the same actions, is therefore bound to be very inefficient, often resulting in the automation equivalent of a [Rube Goldberg machine](https://en.wikipedia.org/wiki/Rube_Goldberg_machine){:target="_blank"}.

These insights are not new. I'm not claiming to be the one that suddenly sheds the light on the problems that many teams and organizations encounter on their automation journey. I've been actively involved with the software testing and automation community for a while now, and I've seen what I described above discussed many times over.

We know that writing tests as early as possible is a good idea. You only have to look at all the people advocating for TDD and BDD, which are two different manifestations of this idea.

We know that GUI-based automation is the hardest form of automation (it still puzzles me that that's where so many test automation engineers start their learning journey...). The increased demand for [API automation skills](https://www.inspiredtesting.com/academy/api-testing-course){:target="_blank"} is just one of the many indicators that the software testing and automation community sees this, too, and reacts to it.

I think there might be a bigger challenge that we need to address, though, and that's concerning the question of 'who?'. Who is responsible for creating the automation? And that's why I want to challenge the notion of the 'test automation engineer' (or SDET, or whatever it is called in your context).

Demand for test automation engineers has been growing steadily in recent years. I tend to joke that if you advertise yourself as a test automation engineer, it is really, really hard work to be without work in the current economy.

The typical approach for companies (including the one I work for at the moment of writing this) is to turn their 'manual testers' (I'm not going to discuss the correctness or wrongness of this term, that's been done [plenty](https://visible-quality.blogspot.com/2018/09/three-kinds-of-testing.html){:target="_blank"} of [times](https://www.developsense.com/blog/2017/11/the-end-of-manual-testing/){:target="_blank"}) into 'automation engineers'. Often, this is even formalized in some sort of career path, where becoming an 'automation engineer' is seen as a promotion for the 'manual tester'.

The problem here is that it is _not_ a promotion, it's taking on an additional task. The ability to write automation is a skill that complements your existing testing skills, not something that you now do instead of testing. Automation is meant to support testing, not replace it. Which means that, as a test automation engineer, you find yourself in a split. Now, you suddenly need to be both:

* **a good software tester** - to know what are the important things to test, to ask the right questions to identify those, to assess risks, to question the product and to know what can and cannot and what should and should not be automated, AND
* **a good software developer** - to be able to effectively translate that what can and should be automated into reliable, reusable, readable and maintainable code

I've seen very few engineers who were able to be both a good software tester AND a good software developer. What you too often end up with is someone who's both a mediocre software tester and a mediocre software developer. At best. Or worse, a person who feels they're 'above' doing 'manual testing', and now 'only do automation' (boy that's a lot of air quotes..).

Not a person you'd like to have on your team if you're striving for excellence, or at least for delivering high quality software at a decent pace in a sustainable manner.

And when you _do_ find that person that is highly skilled in both software testing and automation (i.e., software development), there's a real risk of a silo emerging in your team quickly, as all the testing and automation tasks will likely end up on the desk of that specialist. This isn't a desirable situation to be in as a team, nor as that highly skilled professional. [What happens if they leave? Or take a day (or a week or two) off](https://en.wikipedia.org/wiki/Bus_factor){:target="_blank"}?

All in all, I don't think that the increasing demand for test automation engineers, and the growing desire in the software testing community to become a test automation engineer, is necessarily the best direction we can take. So, what would I like to see happen instead?

What I'd like to see happening much more often is for teams to take on the task of automation as a team, instead of isolating test automation into a separate role. To see how developers and testers can work together to write, run and maintain automation, instead of identifying a separate person to work on this, as a separate step in (or worse, after) the development process. No matter if automation is done during development or after the fact, you will end up with a bottleneck this way.

The key to treating automation as a team effort is collaboration. Automation is both testing and development, so it makes sense to not have one person try and do both. Instead, work together on your tests.

This should preferably be done in a synchronous way, i.e., through pair or mob programming sessions. Why? Because synchronous collaboration is the best way to get immediate feedback on what you're doing. If that isn't possible or feasible in your team, asynchronous collaboration, for example through code reviews, is another way to work together on both features and tests, but for the best possible results I'd suggest you at least try out pairing / mobbing first.

This does require additional skills from both testers and developers, too. Developers will need to better understand what good software testing looks like, and why it is important. This is where testers can help, as they are the ones with the testing skills and experience. Testers, in turn, will need to be able to speak the language of the developer, too. They will need to understand what developers do and be able to engage in conversations about that.

This doesn't mean they need to be able to do all the development themselves, by the way. The way I see it is that testers need to be comfortable discussing 'what' developers do, but not necessarily 'how' developers do what they do. It might be beneficial if the tester knows a thing or two about the 'how' to make collaboration go smoother, but they definitely need to be comfortable discussing 'what' developers do to work together successfully.

So, wrapping this up, my main goal for this blog post was to write down some of my current thoughts on test automation engineers and why I think that trying to find one, or to become one, might not necessarily be the best way forward. These thoughts are based on my own experiences and past projects I've worked in, as well as what I'm seeing happening around me in the wider software testing world.

What do you think? Do you really need a test automation engineer?

_Final note: while reading up on this subject, I stumbled upon [this blog post](https://thefriendlytester.co.uk/2017/07/a-look-at-test-automation-and-test-automators){:target="_blank"} by Richard Bradshaw that he wrote in response to Tweets from [Alan Page](https://www.linkedin.com/in/a-l-a-n){:target="_blank"} a couple of years ago. Well worth a read, because even though it was written 4.5 years ago, there's a lot of truths that still hold in there, and because I think that while we've made some progress in the field, there's still plenty of room for improvement._