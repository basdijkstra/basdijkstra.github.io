---
title: How I run exercises in my courses
layout: post
permalink: /how-i-run-exercises-in-my-courses/
categories:
  - Training
tags:
  - running exercises
  - learning
---
_This blog post was published earlier in [my weekly newsletter](/newsletter/) on February 21, 2023._

In this blog post, I'd like to dive a little deeper into the way I conduct exercises in my training courses, and why I run them the way I do. I think there are a couple of lessons in there that are valuable for everyone, even when you're not in [any of my courses](/training/).

While [I wrote about this on my blog a while ago](/exercises-from-my-courses-keeping-your-tests-simple/), that blog post was more of an example of what an exercise looks like. This newsletter issue focuses on the how, instead. The what is still very relevant, though, and the exercise described in the blog post I just linked to is still a part of courses I run.

#### Start with why and what
Before I dive into the meat of an exercise, I typically take a bit of time exploring the kind of problem we're trying to solve. I don't believe in teaching people tricks with tools, I want to make them (at least a little) better at solving real life problems with tools. Understanding the problem is a big step towards getting there.

I think this is especially important as in testing, automation and programming in general, there is often more than one way to solve a problem. Understanding the problem properly before trying to solve it helps in finding and assessing alternative solutions.

Here are some examples of exercise topics and the problem that's addressed:

* Learning how to write parameterized tests - solves the problem of having to copy and paste code, and the duplication and decreased readability that are the result of doing this, when you're looking to write multiple tests for the same logic with different input and expected output parameters.
* Learning object serialization - solves the problem of having to write complex and brittle code to create JSON or XML request payloads 'by hand' when writing API tests
* Learning the Page Object or the Screenplay pattern - solves the problem of your UI test code dealing with a lot of different things at the same time, decreasing both readability and maintainability

You get the idea :) Essentially, what I'm trying to do first is 'paint the bigger picture' and try and make people understand what the problem is and why it might be a good idea to solve it.

#### Show them a solution
After explaining the problem, I show them a solution that will solve that problem. I want to stress here, as I stress that in my courses, that it's always a solution. There are almost always more ways to solve that problem. Sometimes I even show alternative solutions, but typically wait with doing that until the exercise debrief so as not to confuse participants, especially those who are fairly new to the problem area.

I used to show this solution in slides, but these days I have mostly said goodbye to those slides in favour of live coding my solution. Why? Because I've learned that this makes the solution 'come alive' for people, plus it gives me a much better opportunity to talk people through the solution I chose: "now I do this, because... then we do that, because...", and so on.

Also, since I don't always get it perfectly right in the first go either, often because I forgot the exact syntax for something or mix up libraries or languages, it shows participants that I, too, make mistakes and don't know everything. This is often a confidence boost, again especially for less experienced people. The more experienced ones tend to know there's no way you can know and remember everything and that Google is your friend.

#### Give them the examples and a series of exercises
I then share the code for my solution with them, as well as the code for the examples if they exist, and give them a series of exercises to work on on their own. This is where they get to apply what they've learned. I typically supply the answer to the exercises beforehand, so they have something to peek at in case they're really stuck, as well as to compare their own solutions to.

When I sense that people are still hesitant, I often ask them if they want me to complete the first exercise 'live' while sharing my screen. They almost invariably say 'yes' to this, and I'm happy to do so. After that, they'll almost always be confident enough to complete the remainder of the exercises themselves.

I try and time the amount and complexity of the exercises so that they'll spend around 10-15 minutes on it, sometimes up to 20 if it's a particularly large exercise (like rewriting a procedural test to one that uses Page Objects, for example). If they're done much sooner, I know I need to increase the amount or the complexity of the exercises next time, if they all are still working after about 20 minutes, I know I need to do some more work to make it a little easier for them.

Also, where I can I try and add some 'bonus' exercises that go a little beyond what we've discussed. This gives people who complete the regular exercises more quickly some more challenging tasks to sink their teeth into. These typically require people to find the solution online themselves. This is almost always appreciated by both the 'faster' people (as it prevents them from getting bored), but also by the 'slower' people (as it gives them something to grow towards and look forward to).

#### Review and debrief
Finally, after I've seen that everybody has solved at least part of the exercises, and I feel like they've had enough time to work on them, I run a debrief where I ask someone to volunteer and talk me and the others through their solution. Most of the times, there's at least one person eager enough to volunteer, and after that, the rest will take care of itself. Sometimes, though, I have to pick someone, and that does the trick, too.

There's a couple of very interesting things that often happen during such a debrief:

* I get a much better idea about whether or not people understand the what and the why of the exercises when I hear them explain what they did to the rest of the group
* People learn not just from me, but from their peers as well, which sometimes even turns me from a trainer to mostly a facilitator, a change of role I'm more than happy to, you guessed it, facilitate
* Sometimes, people show solutions that teach me something new as well, which I love, and I make a habit of giving them credit for their creativity and comparing their solution to mine (again, what I present is a solution, not the solution)

A typical exercise cycle goes through all the steps described here, and depending on the complexity of the material and the level of experience of people, will take anywhere between 30 and 90 minutes. We'll repeat this a couple of times throughout the session, leading to what I think is often a good mix of instruction, hands-on exercises and discussion / debrief.

What would your ideal course / exercise structure look like? And if you've run training sessions or facilitated workshops before, what can I learn from you?