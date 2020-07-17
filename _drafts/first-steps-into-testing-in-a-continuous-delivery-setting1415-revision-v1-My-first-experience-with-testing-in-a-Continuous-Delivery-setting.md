---
id: 1417
title: My first experience with testing in a Continuous Delivery setting
date: 2016-04-29T08:39:42+02:00
author: Bas
layout: revision
guid: http://www.ontestautomation.com/1415-revision-v1/
permalink: /1415-revision-v1/
---
For the first time in my 10 years in the testing business I am working on a project where they do Continuous Delivery (CD). This gives me an opportunity to learn as well as a lot to think about, especially when it comes to the role of testing and test automation in CD.

A lot has been written on the concept and the pros and cons of CD itself already, and since I&#8217;m clearly not an expert on the topic &#8211; although I did deliver a <a href="http://www.slideshare.net/BasDijkstra1/continuous-delivery-conference-2014-bas-dijkstra" target="_blank">presentation</a> at the inaugural Continuous Delivery Conference in the Netherlands &#8211; I won&#8217;t try and rehash information or opinions that have been presented on other blogs and in other articles before. If you want a good introduction to CD, I advise you to read <a href="http://www.amazon.com/Continuous-Delivery-Deployment-Automation-Addison-Wesley/dp/0321601912/" target="_blank">this book</a> by Jez Humble and Dave Farley instead.

However, there is a topic very closely related to CD that I feel does warrant a closer look, and that&#8217;s the creation of an optimal test strategy for projects and organizations that use CD. And by &#8216;optimal test strategy&#8217; I really mean 

> How can we make sure that testing activities in CD neither negatively affect speed of delivery nor neglect potentially poor quality of the product delivered?

Or in still other words: how can we make sure that we deliver high quality products fast?

Let&#8217;s take a quick look at what I think is the role of testing activities in the CD process.

**Testing in the CD process**  
Of course, the ulterior motive behind testing in CD isn&#8217;t any different from testing in any other software development and delivery approach: testing is done to be able to make informed statements about the quality of a product, in this case probably a piece of software. However, with CD, testers typically have a lot less time to test new functionality (let alone perform regression testing) than in typical waterfall or Agile software development processes where working software is released once every few weeks. In CD, changes made to the software are typically pushed through the CD pipeline and ultimately deployed into the production environment in a matter of minutes. As a consequence:

  * A lot of trust needs to be placed in the automated checks that are part of the CD pipeline
  * Manual testing needs to be done outside the CD pipeline. This may or may not be done in the production environment

**Test automation in CD**  
Because of the required speed of delivery, a lot of tests (<a href="http://www.developsense.com/blog/2013/03/testing-and-checking-redefined/" target="_blank">or checks, really</a>) should be automated. These automated checks should cover enough ground so that the team delivering the software is confident enough that the software does what it&#8217;s supposed to do once the CD pipeline is completed and the software is in the production environment.

Of course, not all tests can be automated. There&#8217;s still a need for testers to really explore and test (as opposed to check) the application. But this can only be done after the CD pipeline has been completed, i.e., testing can only be done on software that is already in production. The way this is solved in my current project is by putting the software behind a feature flag, meaning the software IS installed in the production environment, but only available once you set a feature flag (implemented as a cookie in our case as we&#8217;re dealing with a web application). This allows testers to test the software safely, without end users being impacted by any bugs that escaped the automated checks in the CD pipeline. Once all tests are completed, the feature flag is removed and the software is really in production. Another approach (used by Facebook, for example) is to make new features available to a small group of users first, wait for their feedback and then gradually roll out the new feature to a wider audience.

**An integrated test strategy**  
So, what should testing in a CD world look like? If you ask me, it all comes down to a proper integrated test strategy. That is, both developers and testers should know who tests what, so that there are no gaping holes in the overall test coverage (to prevent quality leakage) and no double work is done (to prevent unnecessary wasting of time). Only once you have clear who tests what and when this is done in the CD process, then you can decide whether or not to automate the accompanying tests and on what level (unit, integration, end-to-end).

Creating a well-functioning integrated test strategy requires adaptation from both testers and developers:

  * _Developers_ should become even more aware of the importance of testing and start to looking beyond plain happy-flow testing in their unit and integration tests. This removes a lot of potential defects that are otherwise detected later in the process, if at all.. If you&#8217;re a developer, please do read <a href="http://www.simpleprogrammer.com/2016/04/20/developers-poor-testers-can-done/" target="_blank">this article</a> on SimpleProgrammer to see what I&#8217;m trying to get at.
  * _Testers_ should start getting closer to developers, partly to better understand what they are doing, partly to help them with refining and improving their testing skills. This might require you to get comfortable reading and reviewing code, and if you&#8217;re into test automation or willing to become so, to start learning to write some code yourself.

**Further reading**  
If after reading this blog post you want another take on the role of testing and testers in a CD process, I highly recommend reading <a href="http://sdtimes.com/testing-in-a-continuous-delivery-world/" target="_blank">this article</a> from the SD Times.