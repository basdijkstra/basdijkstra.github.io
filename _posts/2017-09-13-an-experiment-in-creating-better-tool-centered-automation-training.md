---
id: 1998
title: An experiment in creating better tool-centered automation training
date: 2017-09-13T10:00:06+02:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=1998
permalink: /an-experiment-in-creating-better-tool-centered-automation-training/
categories:
  - Education
tags:
  - api testing
  - craftsmanship
  - training
---
Last week, I delivered the second part of a two-evening API testing training at my former employer. They contacted me a while ago to see if I could help them in offering test automation training for their employees, as well as for their clients and other contacts. When I was still working with them, I used to deliver automation training as well, and it felt really great to be asked back, even though I have left them almost three years ago now.

But that&#8217;s not what this post is about.

This API testing training was in some ways an experiment I have wanted to conduct for a while now. I see a lot of individuals and organizations offering automation training, and most of it is specific to one single tool. In itself, this isn&#8217;t inherently a bad thing, but I&#8217;ve got one big problem with a lot of these tool-centered courses: instead of teaching you how to create a sound automation approach around one or more tools, they simply go through the most important features of a single tool and teach the participants some (sometimes useful) tricks. I know. I&#8217;ve delivered those courses in the past as well. If you&#8217;re a tool vendor / creator, I can understand why you would want to do that. But I think there&#8217;s more to good automation education than teaching you all the ins and outs of a specific tool. Let&#8217;s call them the 3 C&#8217;s:

  * **Context** &#8211; A tool is often only useful in a specific context. This context includes the skills of the people that will use the tool, the development and delivery process that the tool is to be made part of, and much more. Without context, it&#8217;s very hard to decide if a specific tool is the right one for the job.
  * **Competition** &#8211; For nearly all tools out there, there&#8217;s at least one competitor on the market (but often much more) that can be used to complete the same task. It is therefore essential for good training to introduce more than one option, have the participants get some hands on experience with all of them and let them decide what would work best for their tasks and team.
  * **Cutting the crap** &#8211; Tool-specific training might give the idea that the tool the participants are being trained in is the best thing since sliced bread. Which in turn leads people to try and automate anything and everything with a single tool. Which in turn all too often leads to crap. In other words: what&#8217;s the point in knowing all different types of waits available in Selenium if you don&#8217;t know how to decide what is a good scenario to automate using Selenium in the first place?

So, instead of delivering my API testing training around REST Assured alone (which I&#8217;ve done a number of times in the past), I decided to introduce three different tools to the participants: <a href="http://rest-assured.io/" target="_blank">REST Assured</a>, the open source version of <a href="https://www.soapui.org/open-source.html" target="_blank">SmartBear SoapUI</a> and <a href="https://www.parasoft.com/product/soatest/" target="_blank">Parasoft SOAtest</a>. After an introduction into what constitutes API testing, why it is useful and what you can test using APIs, I let the participants create a number of basic API tests with each of these tools (pretty much the same tests three times over), so they could experience firsthand how the features provided by each of the tools compare. Moreover, since I chose tools that are at opposite ends of the API test tool spectrum (REST Assured is a Java library for RESTful APIs, SOAtest is a commercially licensed enterprise-grade tool that supports a wide variety of protocols and message types, with SoapUI somewhere in between), participants get a much broader view of API testing than they&#8217;d get by learning REST Assured alone.

The feedback I received afterwards confirmed what I hoped to achieve with my experiment: all the attendees thought it was great to see more than a single tool, and since I gave them pointers to material for further exploration, they could decide for themselves in which direction their further education will take them.

I recently launched <a href="http://www.ontestautomation.com/training/building-great-end-to-end-tests-with-selenium-and-cucumber-specflow/" target="_blank">another course</a> in which I try to do something similar, although in another fashion: instead of teaching people how to use Selenium WebDriver (i.e., teaching them the API and some useful Selenium-only tricks), I explain what types of tests should be created using Selenium, and I teach them how I would approach creating readable, maintainable and reliable tests with Selenium, Cucumber/SpecFlow, JUnit/NUnit and ExtentReports. Again: providing context and cutting the crap (you can argue about whether or not I&#8217;m covering &#8216;competition&#8217; in this one) instead of teaching people all of the methods and features of a single tool.

I hope to deliver this type of automation training much more often in the future, and I&#8217;d love to see other automation training providers follow suit. For those of you who&#8217;d like some more details on the training objectives and subjects covered in this API testing training, please <a href="http://www.ontestautomation.com/training/introduction-to-api-test-automation/" target="_blank">click here</a>.

As always, I&#8217;d love to hear your thoughts. What to you constitutes good automation training?