---
id: 1815
title: More troubles with test data
date: 2017-03-15T00:00:02+01:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=1815
permalink: /more-troubles-with-test-data/
categories:
  - General test automation
tags:
  - end-to-end
  - test data management
---
If managing test data in complex end-to-end automated test scenarios is an art, I&#8217;m certainly not an artist (yet). If it is a science, I&#8217;m still looking for the solution to the problem. At this moment, I&#8217;m not even sure which of the two it is, really..

**The project**  
Some time ago, I wrote <a href="http://www.ontestautomation.com/managing-test-data-in-end-to-end-test-automation/" target="_blank">a blog post on different strategies to manage test data in end-to-end test automation</a>. A couple of months down the road, and we&#8217;re still struggling. We are faced with the task of writing automated user interface-driven tests for a complex application. The user interface in itself isn&#8217;t that complex, and our tool of choice handles it decently. So far, so good.

As with all test automation projects I work on, I&#8217;d like to keep the end goal in mind. For now, running the automated end-to-end tests once every fortnight (at the end of a sprint) is good enough. I know, don&#8217;t ask, but the client is satisfied with that at the moment. Still, I&#8217;d like to create a test automation solution that can be run on demand. If that&#8217;s once every two weeks, all right. It should also be possible to run the test suite ten times per day, though. Shorten the feedback loops and all that.

**The test data challenge**  
The real challenge with this project, as with a number of other projects I&#8217;ve worked on in the past, is in ensuring that the test data required to successfully run the tests is present and in the right state at all times. There are a number of complicating factors that we need to deal (or live) with:

  * The data model is fairly complex, with a large number of data entities and relations between them. What makes it really tough is that there is nobody available that completely understands it. I don&#8217;t want to mess around assuming that the data model looks a certain way.
  * As of now, there is no on demand back-up restoration procedure. Database back-ups are made daily in the test environment, but restoring them is a manual task at the moment, blocking us from recreating a known test data state whenever we want to.
  * There is no API that makes it easy for us to inject and remove specific data entities. All we have is the user interface, which results in long setup times during test execution, and direct database access, which isn&#8217;t of real use since we don&#8217;t know the data model details.

**Our current solution**  
Since we haven&#8217;t figured out a proper way to manage test data for this project yet, we&#8217;re dealing with it the easiest way available: by simply creating the test data we need for a given test at the start of that test. I&#8217;ve mentioned the downsides of this approach in my previous post on managing test data (again, <a href="http://www.ontestautomation.com/managing-test-data-in-end-to-end-test-automation/" target="_blank">here</a> it is), but it&#8217;s all we can do for now. We&#8217;re still in the early stages of automation, so it&#8217;s not something that&#8217;s holding us back to much, but all parties involved realize that this is not a sustainable solution for the longer term.

**The way forward**  
What we&#8217;re looking at now is an approach that looks roughly like this:

  1. A database backup that contains all test data required is created with every new release.
  2. We are given permission to restore that database backup on demand, a process that takes a couple of minutes and currently is not yet automated.
  3. We are given access to a job that installs the latest data model configuration (this changes often, sometimes multiple times per day) to ensure that everything is up to date.
  4. We recreate the test data database manually before each regression test run.

This looks like the best possible solution at the moment, given the available knowledge and resources. There are still some things I&#8217;d like to improve in the long run, though:

  * I&#8217;d like database recreation and configuration to be a fully automated process, so it can more easily be integrated into the testing and deployment process.
  * There&#8217;s still the part where we need to make sure that the test data set is up to date. As the application evolves, so do our test cases, and somebody needs to make sure that the backup we use for testing contains all the required test data.

As you can see, we&#8217;re making progress, but it is slow. It makes me realize that managing test data for these complex automation projects is possibly the hardest problem I&#8217;ve encountered so far in my career. There&#8217;s no one-stop solution for it, either. So much depends on the availability of technical hooks, domain knowledge and resources at the client side.

On the up side, last week I met with a couple of fellow engineers from a testing services and solutions provider, just to pick their brain on this test data issue. They said they have encountered the same problem with their clients as well, and were working on what could be a solution to this problem. They too realize that it&#8217;ll never be a 100% solution to all test data issues for all organizations, but they&#8217;re confident that they can provide them (and consultants like myself) with a big step forwards. I haven&#8217;t heard too many details, but I know they know what they&#8217;re talking about, so there might be some light at the end of the tunnel! We&#8217;re going to look into a way to collaborate on this solution, which I am pretty excited about, since I&#8217;d love to have something in <a href="http://www.ontestautomation.com/on-becoming-a-test-automation-craftsman/" target="_blank">my tool belt</a> that helps my clients tackle their test data issues. To be continued!