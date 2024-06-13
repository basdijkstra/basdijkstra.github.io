---
title: Stop chasing after the latest tools
layout: post
permalink: /stop-chasing-after-the-latest-tools/
categories:
  - Test automation
tags:
  - tools
  - learning
---
_This blog post was published earlier in my (now defunct) weekly newsletter on February 28, 2023._

In this blog post, I'd like to dive a little deeper into what might just be a pet peeve of mine, but what I believe is a pretty fundamental problem in the thought patterns of a lot of automation engineers. Or at least of those I see posting their thoughts on LinkedIn and in blogs.

As soon as a new test automation tool is released, there's an influx of people creating some basic examples and writing down their experience in blog posts. That's fine. I, too, like experimenting with and demonstrating new tools. It keeps me up to date on what's new in the test automation world, and I hope that others can learn from what I've been trying. That's not where the problem is.

What I do find problematic is that many of these articles start downplaying other tools and recommending that you should stop using existing tool X in favour of new tool Y. You've probably seen these articles, too:

_"Why you should stop using Selenium and start using Cypress"_

_"10 reasons to migrate your UI tests to Playwright"_

_"If you're still writing your automation in Java, your mother was a hamster and your father smelt of elderberries"_

This type of content, clearly written by people chasing relentlessly after the latest tools and touting every tool they've used for a couple of weeks annoys me. Especially when it's written in a "my tool is better than your tool" fashion. Sure, it'll probably generate clicks and comments, but does it really add a lot of long-lasting value to those who read it? I highly doubt it.

First of all, tools come and go. What is the hot and happening new tool right now might be next year's legacy. I've seen so many tools and techniques, both commercially licensed as well as open source, being announced as a 'game changer' or 'essential', only to severely quiet down or even disappear entirely over time. You could call it experience, you could call it being cynical, but I've stopped paying too much attention to these 'game changers'. And yes, that includes tools using ‘AI’.

Second of all, most 'replacements' that claim they should replace existing tools do the same, often only in a slightly different way. Some examples:

* UI automation tools allow to you wait for and find elements on a screen, interact with them and inspect and verify their properties
* API testing tools allow you to construct and send requests and capture, inspect and process responses
* API mocking tools allow you to listen for incoming requests, process them and decide on the response to be returned

That's it. It often really isn't much more complicated than this. Sure, new tools may bring some utilities that make them a little easier to use, but rarely do they bring the radical change that their marketing department, or people from the testing community that seem to act as their marketing department, promises.

Most new tools do the same things as the tools I used when I started out in testing some 16 years ago. Using them has become easier, sometimes because the tools have improved, often because I've gotten better / more experienced at using them.

This is also once more an argument in favour of learning principles and patterns, not (just) tools and tricks. If you understand the fundamental concepts of what a tool does and how it should be used, it's relatively straightforward to learn how to use a new one.

So, my advice would be to pick a tool that works for you and you should be fine. Of course, it's a good idea to first check if there's a healthy amount of support available, either from the tool vendor or from the wider testing and development community, but other than that, you can't really go wrong.

Almost always, when there's a problem with automation, it's not the tool that's the problem, it's the way it is used...

What are your thoughts on this?