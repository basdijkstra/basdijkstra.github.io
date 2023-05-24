---
title: On 'best' practices
layout: post
permalink: /on-best-practices/
categories:
  - Test automation
tags:
  - learning
  - software development
---
_This blog post was published earlier in my (now defunct) weekly newsletter on March 28, 2023._

In this blog post, I want to spend a bit of time discussing one of my (apparently many...) pet peeves. This time, it's one that keeps coming up regularly in emails and LinkedIn DMs I get, but also in my training courses. The discussion often goes something like this:

Them: _"I want to learn how to do <insert specific tool or technology here> well"_ (this is good!)

Me: _"What have you been doing in the past, what worked and what didn't?"_

Them: _"We've tried implementing it before but it got messy"_ or _"We hired some experts to do this before but it got messy"_ (not uncommon)

Me: _"How can I help you to do better?"_

Them: _"We need to learn the best practices around <insert specific tool or technology here>"_

Me (actually): _"So, I can teach you / tell you about some common practices, but ..."_

Me (in my head): _"Please no, not this again"_

Needless to say, I struggle with the term 'best practice' and with people asking for them. I'm finding myself becoming the archetypical 'consultant' in response, saying 'it depends' far more often than I really want to...

Jokes and stereotypes aside, though, one of the things I've learned over the years in this business is that there are very few universal truths in testing and automation. Yet that's what I think something worthy of the term 'best practice' should be: something that can be applied to some sort of benefit all the time.

However, for most of the things that people call 'best practice', there are always situations where those practices aren't actually the best. So much of whether or not a certain technique, tool or approach works depends on context that calling it 'best' is just a lie.

Sure, there are quite a few things that are generally considered to be useful, e.g., making your tests as small as you need, but not smaller, but these are typically pretty abstract observations and not tangible enough by themselves to be actionable.

An example: the Page Object pattern isn't a 'best practice'. It's a very useful pattern, and it is generally very helpful in separating the 'what' from the 'how' in your UI automation, but that doesn't mean that you can always apply it without further thought. Sometimes, a different pattern, like the Screenplay pattern, simply works better. Other times, it might be the other way around.

Now, 'separating the what from the how' is already much more universally applicable as a piece of good advice, but that's an example of a pretty abstract observation, as it doesn't tell people how to do that exactly. Plus, there might still be situations where you don't even need to go through the effort of doing so, for example if your automation code is expected to be short-lived only.

So, whenever I get people asking for 'the best practice' on something, I typically respond with an explanation that involves talking about context. This involves a lot of follow-up questions, such as

* What do you want to achieve?
* Who's going to do the work?
* What information are you looking for when you perform tests X, Y or Z?

and much, much more. Sure, that sometimes disappoints people, as we tend to look for bite-sized answers that can be applied everywhere. Unfortunately, that's not how testing, automation or the world in general works. There are very few universal 'rights' or 'wrongs', and I typically am very cautious of people saying 'you should always do this' or 'that is the best option out there'.

To wrap things up, here's a recommendation: instead of asking 'what's the best practice on this?', ask 'what would work well here?'. I think that's a much healthier way to approach a problem or challenge.

How about you?