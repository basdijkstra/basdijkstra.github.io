---
id: 335
title: 'Best practice: use UI-driven automated testing only when necessary'
date: 2014-04-08T08:47:41+02:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=335
permalink: /best-practice-use-ui-driven-automated-testing-only-when-necessary/
categories:
  - Best practices
tags:
  - best practice
  - test automation
  - user interface
---
Question: what do you think of when I ask &#8216;what does test automation look like&#8217;? Chances are high that you think of a tool that replays user interaction with an application using the graphical user interface. This user interaction is either captured through recording functionality in the tool that is used and subsequently replayed, or it is programmed by a test automation engineer, or a mixture of both approaches is used.

Traditionally, these tools use HTML object attributes to uniquely identify and manipulate objects on the screen. Recently, a number of tools have emerged that use image recognition to look for and manipulate screen objects. Object recognition approach notwithstanding, all of these tools use the user interface to interact with the application under test.

This approach to automated testing is one of the most popular ones out there. For starters, it looks good in demos and sales pitches. More importantly though, it most closely represents how a manual test engineer or an end user would interact with the application under test. However, there&#8217;s a fundamental problem attached to UI-based automated testing. Too often, **the investment just isn&#8217;t outweighed by the profits**. Test automation engineers spend hours upon hours on crafting and maintaining wonderful frameworks and intricate scripts, with no one evaluating the ROI for these efforts.

Now, I don&#8217;t say that UI test automation shouldn&#8217;t be done. It definitely has a place within the overall spectrum of test automation, and I have seen it used with great results in various places. However, I do feel it is overused in a lot of projects. Most of the times, the test automation team or their managers fell into one of the **pitfalls of UI test automation**:

  * All test scripts are automated using a UI-based test automation approach, even those test cases that aren&#8217;t really about the UI.
  * Test automation engineers try to automate every test script available, and then some more. It might be due to their own poor judgment or to the wishes / demands from management to automate all test scripts, but bottom line is that not all test scripts should be automated just because it can be done.
  * The test automation approach is suboptimal with regards to maintainability. Several best practices exist to ensure maximum maintainability for automated UI test scripts, including use of object maps and keyword-driven test frameworks. If these are not applied or if they are applied incorrectly, more time might be required for automated test script maintenance.

Therefore, I&#8217;d recommend the use of UI-based test automation only when either (or both) of the following is true:

  1. The test script actually contains validations and/or verifications on the user interface level, or
  2. There is no alternative interface available for interacting with the application.

With regards to the second point, alternative interfaces could include interfaces on web service or on database level. Using these to drive your automated tests remove some of the major drawbacks of UI-based test automation, such as the maintenance burden due to changes in UI object properties and UI synchronization issues.

**Case study**  
I have used the principles outlined above with good results in a project a couple of years ago In this project, I was responsible for setting up an automated test suite for an application built on the Cordys (now OpenText) Business Process Management suite. This application could be decomposed into four tiers: the user interface, a BPM tier, a web service tier and a database tier.

At first, I started out building automated tests on the user interface level, as this BPMS was still new to me and this was the obvious starting point. I soon realized that automating tests on the UI level was going to be very hard work as the user interface was very dynamic with a lot of active content (lots of Javascript and Xforms). If I was to deliver a complete set of automated test scripts, I would either have to invest a lot of time on maintenance or find some other way to achieve the desired results.

Luckily, by digging deeper into the Cordys BPMS and reading lots of material, I found out that it has a very powerful web service API. This API can be used not only to drive your application, but to query and even configure the BPMS itself as well. For instance, using this web service API, you can:

  * Create new instances of the BPM models used in the application under test,
  * Send triggers and messages to it, thus making the BPM instance change state and go to a subsequent state,
  * Verify whether the new state of the BPM instance matches the expected state, and so on..

Most of this could be done using predefined web service operations, so the risk of these interfaces changing during the course of the project was small to none. Using this API, I was able to set up an automated test for 80-90% of the application logic, as the user interface was nothing more than a user-friendly way to send messages to process instances display information about the current state and the next action(s) to take. Result!

[<img src="http://www.ontestautomation.com/wp-content/uploads/2014/04/from_to.png" alt="Use UI testing only when the user interface is actually tested" width="1278" height="361" class="aligncenter size-full wp-image-339" srcset="https://www.ontestautomation.com/wp-content/uploads/2014/04/from_to.png 1278w, https://www.ontestautomation.com/wp-content/uploads/2014/04/from_to-300x84.png 300w, https://www.ontestautomation.com/wp-content/uploads/2014/04/from_to-1024x289.png 1024w" sizes="(max-width: 1278px) 100vw, 1278px" />](http://www.ontestautomation.com/wp-content/uploads/2014/04/from_to.png)

Even better, in later projects where Cordys was used, I have been able to reuse most of the automated testing approach and the framework I used to set up and execute automated tests. Maximum reusability achieved and minimum maintenance required, all through a change of perspective on the automated testing approach.

Have you experienced similar results, simply by a shift of test automation perspective? Let me know.