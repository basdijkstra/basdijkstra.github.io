---
title: The UI automation fixation
layout: post
permalink: /the-ui-automation-fixation/
categories:
  - Test automation
tags:
  - tools
  - user interface automation
---
_This blog post was published earlier in my (now defunct) weekly newsletter on March 7, 2023._

Today, I wanted to talk about an observation I keep coming back to when I talk to fellow testers, be it in one of my training courses, a mentoring session or somewhere else. Or, maybe rather than an observation, it's something that's been bugging me for a long time, both as a test automation professional and as a trainer. It basically comes down to these questions popping up time and again:

Me, as a professional: _Why do so many people start their journey in test automation with UI automation?_

And me, as a trainer: _Why do so many test automation training courses start with UI automation?_

(By the way, when I refer to 'UI automation' I mean the type of E2E or full stack automation often performed using tools like Selenium, Cypress or Playwright. I'm not referring to unit tests on the UI layer, for example)

Don't get me wrong, UI automation is an important component of an overall automation strategy, and it deserves time and attention. A rock stable suite of UI tests that check important flows through your application is invaluable in demonstrating that end users can continue to perform business critical operations. I love UI automation when done well. And that's where the problem is: the 'when done well' part.

You see, I think that UI automation is, by a large margin, the hardest form of automation to learn, to master and to do well enough to actually deliver value. This is because these tests traverse all layers and components of your system, and the more moving parts you have, the larger the risk that 'something' behaves differently from what you expected.

This challenge has become even bigger in recent years with the JavaScript-heavy web applications using frameworks like Angular, React and Vue. They can create wonderful user experiences for human beings, with animations, slide-out menus, spinners indicating waiting times and what more, but these only make writing reliable UI automation more difficult compared to when we only worked with static HTML pages.

And that's OK, because no matter how you look at it, the graphical user interface is an interface, heck, typically the only interface, in an application that is specifically created to be consumed and interacted with by human beings. So it's no wonder that teams spend plenty of effort creating a pleasant user experience and an attractive UI.

But from an automation standpoint, again, this is where things get difficult. All these bells and widgets that make life (or at least using the application) better for human beings greatly complicate writing reliable UI automation. More complex interactions (drag and drop, anyone?). More intricate waiting strategies. And the list continues.

Part of me understands why people, especially those new to automation, start with UI automation. It's often the only way they've interacted with a system, so it's the only interface they know.

But considering the fact that, again, it's also by far the hardest form of automation there is, isn't it time to start doing things differently? To pick a different approach to learning automation?

Wouldn't starting with other types of tests make more sense? Compared to UI automation, writing lower level tests, for example at the API or even the code level, is a lot easier, once you know how to read, interpret and connect to these interfaces (yes, your code is an interface, too). Tests against the API or the code level tend to be a lot smaller, making them faster and cheaper to write and to execute.

And, speaking of broadening our horizons, wouldn't it be a good idea to let go of the fixation on 'automating test cases' altogether, and start looking for additional ways in which tools can help and support our testing efforts. Because to me, that's the true definition of test automation.

Scripts to generate test data. To read, parse and filter logs for interesting entries. To fuzz data for quick input validation / sanitization testing. Set up and use a mock to simulate hard to replicate behaviour for an API dependency. All things that aren't necessarily the first things people may think of when they think about test automation, but these are some examples of situations where smart use of tools can bring value quickly.

There are several folks in the testing and automation community who've been working on and writing about this for a while, but I don't think the message has been spread far enough, as there's still a lot of (again, too much, if you'd ask me) focus on UI automation to replicate what used to be done by human beings.

I'm sure I'll dive deeper into this in future blog posts, but as always, I'm curious to hear what you think, too. What was your starting point when you first entered the test automation field? Does your team or company focus too much on UI automation, too? And do you share the views of UI automation being the hardest form of automation there is in the first place?

I'd love to hear from you.