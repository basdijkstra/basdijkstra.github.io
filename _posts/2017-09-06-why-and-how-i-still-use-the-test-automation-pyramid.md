---
id: 1980
title: Why and how I still use the test automation pyramid
date: 2017-09-06T10:00:42+02:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=1980
permalink: /why-and-how-i-still-use-the-test-automation-pyramid/
categories:
  - General test automation
tags:
  - education
  - test automation pyramid
---
Last week, while delivering part one of a two-evening API testing course, I found myself explaining the benefits of writing automated tests at the API level using the test automation pyramid. That in itself probably isn&#8217;t too noteworthy, but what immediately struck me as odd is that I found myself apologizing to the participants that I used a model that has received so many criticism as the pyramid.

Odd, because

  1. Half of the participants hadn&#8217;t even heard of the test automation pyramid before
  2. The pyramid, as a model, to me is still a very useful way for me to explain a number of concepts and good practices related to test automation.

#1 is a problem that should be tackled by <a href="http://www.ontestautomation.com/why-i-think-automation-education-is-broken-and-what-ill-try-and-do-about-it/" target="_blank">better education around software testing and test automation</a>, I think, but that&#8217;s not what I wanted to talk about in this blog post. No, what I would like to show is that, at least to me, the test automation pyramid is still a valuable model when explaining and teaching test automation, as long as it&#8217;s used in the right context.

<a href="http://www.ontestautomation.com/?attachment_id=1981" rel="attachment wp-att-1981"><img src="http://www.ontestautomation.com/wp-content/uploads/2017/09/test_automation_pyramid.png" alt="The version of the test automation pyramid I tend to use in my talks" width="726" height="550" class="aligncenter size-full wp-image-1981" srcset="https://www.ontestautomation.com/wp-content/uploads/2017/09/test_automation_pyramid.png 726w, https://www.ontestautomation.com/wp-content/uploads/2017/09/test_automation_pyramid-300x227.png 300w" sizes="(max-width: 726px) 100vw, 726px" /></a>

The basis of what makes the pyramid a useful concept to me is the following distinction:

> It is a model, not a guideline.

A guideline is something that&#8217;s (claiming to be) correct, under certain circumstances. A model, as the statistician George Box said, is always wrong, but some models are useful. To me, this applies perfectly to the test automation pyramid:

**There&#8217;s more to automation than meets the UI**  
The test automation pyramid, as a model, helps me explain to less experienced engineers that there&#8217;s more to test automation than end-to-end tests (those often driven through the user interface). I explain this often using examples from real life projects, where we chose to do a couple of end-to-end tests to verify that customers could complete a specific sequence of actions, combined with a more extensive set of API tests to verify business logic at a lower level, and why this was a much more effective approach than testing everything through the UI.

**Unit testing is the foundation**  
The pyramid, as a model, perfectly supports my belief that a solid unit testing strategy is the basis for any successful, significantly-sized test automation effort. Anything that can be covered in unit tests should not have to be covered again in higher level tests, i.e., at the integration/API or even at the end-to-end level.

**E2E and UI tests are two different concepts**  
The pyramid, as a model, helps me explain the difference between end-to-end tests, where the application as a whole is exercised from top (often the UI) to bottom (often a database), and user interface tests. The latter may be end-to-end tests, but unbeknownst to surprisingly many people you can write unit tests for your user interface just as well.There&#8217;s a reason the top layer of the pyramid that I use (together with many others) says &#8216;E2E&#8217;, not &#8216;UI&#8217;&#8230;

**Don&#8217;t try to enforce ratios between test automation scope levels**  
The pyramid, when used as a guideline, can lead to less than optimal test automation decisions. This mainly applies to the _ratio_ between the number of tests in each of the E2E, integration and unit categories. Even though well though through automation suites will naturally steer towards a ratio of more unit tests than integration tests and more integration tests than E2E tests, it should never be forced to do so. I&#8217;ve even seen some people, which unfortunately were the ones in charge, make decisions on what and how to automate based on ratios. Some even went as far as saying &#8216;X % of our automated tests HAVE TO be unit tests&#8217;. Personally, I&#8217;d rather go for the ratio that delivers in terms of effectiveness and time needed to write and maintain the tests instead.

**Test automation is only part of the testing story**  
&#8216;My&#8217; version of the test automation pyramid (or at least the version I use in my presentations) prominently features what I call exploratory testing. This helps remind me to tell those that are listening that there&#8217;s more to testing than automation. I usually call this part of the testing story &#8216;exploratory testing&#8217;, because this is the part where humans explore and evaluate the application under test to inform themselves and others about aspects of its quality. This is what&#8217;s often referred to as &#8216;manual testing&#8217;, but I don&#8217;t like that term.

As you can see, to me, the test automation pyramid is still a very valuable model (and still a useless guideline) when it comes to me explaining my thoughts on automation, despite all the criticism it has received over the years. I hope I never find myself apologizing for using it again in the future..