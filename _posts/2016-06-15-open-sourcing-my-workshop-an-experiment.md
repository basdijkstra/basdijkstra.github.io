---
id: 1490
title: 'Open sourcing my workshop: an experiment'
date: 2016-06-15T08:00:31+02:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=1490
permalink: /open-sourcing-my-workshop-an-experiment/
categories:
  - API testing
  - Workshops
tags:
  - rest-assured
  - workshop
---
_This is an experiment I&#8217;ve been thinking about for some time now. I have absolutely no idea how it will turn out, but hey, there&#8217;s only one way to find out!_

**What is an open source workshop?**  
I have recently created a workshop and <a href="http://www.ontestautomation.com/lessons-learned-from-delivering-a-test-automation-workshop/" target="_blank">delivered this</a> with reasonable success to an audience of Dutch testers. I have received and processed the feedback I got that day, and now it&#8217;s time for the next step: I&#8217;m making it open source. This means that I&#8217;m giving everybody full access to the slides, the exercises and their solutions and the notes I have used in the workshop. I&#8217;m hoping this leads to:

  * Feedback on how to further improve the contents and the presentation of the workshop
  * More people delivering this workshop and spreading the knowledge it contains

**What is the workshop about and who is it for?**  
The original version of this workshop is an introduction to RESTful web services and how to write tests for these web services using <a href="http://rest-assured.io" target="_blank">REST Assured</a>. It is intended for testers with some basic Java programming skills and an interest in web service and API testing. However, as you start to modify the workshop to your personal preferences, both the contents and the target audience may of course change.

A simplified outline of the workshop looks like this:

  1. An introduction to RESTful web services and their use in modern IT applications
  2. An introduction to REST Assured as a tool to write tests for these services
  3. Setup of REST Assured and where to find documentation
  4. Introduction of the <a href="http://ergast.com/mrd/" target="_blank">application under test</a>
  5. Basic REST Assured features, Hamcrest and GPath
  6. Parameterization of tests
  7. Writing tests for secure web services
  8. Reuse: sharing variables between tests

The workshop comes with a couple of small (this workshop was originally designed to be delivered in 3 hours) sets of exercises and their solutions.

**Prerequisites and installation instructions**  
As stated before, the workshop participants should have an interest in test automation, some basic Java (or any other object oriented programming language) knowledge as well as a grasp of the contents of web services.

The exercises are contained within a Maven project. As REST Assured is a Java library, in order to get things working you need a recent JDK and an IDE with support for Maven. Since TestNG is used as a test runner in the examples, your IDE should support this as well (see the <a href="http://testng.org/doc/index.html" target="_blank">TestNG web site</a> for instructions), but feel free to rewrite the exercises to use your test framework of choice. I just happen to like TestNG.

**So where can I find all these goodies?**  
That&#8217;s an easy one: on <a href="https://github.com/basdijkstra/rest-assured-workshop/" target="_blank">my GitHub repo</a>.

**How can I contribute?**  
I hope that by now you&#8217;re at least curious and maybe even enthusiastic about trying out this workshop and maybe even thinking of contributing to it. You can do so in various ways:

  * By giving me constructive feedback on either the concept of an open source workshop, the contents of this particular workshop, or both.
  * By spreading the word. I&#8217;d love for you to let your friends, colleagues or anyone else in your network know that this workshop exists. The more feedback I get, the better this workshop becomes.
  * By actually delivering the workshop yourself and letting me know how it turned out. That&#8217;s a great form of feedback!

**Interesting, but I have some questions&#8230;**  
Sure! Just submit a comment to this post or send me an email at bas@ontestautomation.com. Especially if you&#8217;re planning to deliver the workshop, but feel you are stuck in some way. Also, please don&#8217;t forget to share the raving reviews you got (or stories on why and where you got booed off the stage..)! Remember that this is an experiment for me too, and if it turns out to be a successful one I definitely will create and publish more of these workshops in the future.

And on a final note, if you&#8217;re interested in having this workshop delivered at your organization, but you don&#8217;t feel comfortable doing it yourself, please let me know as well. I am happy to discuss the options available and work something out.