---
id: 1802
title: First steps as a test automation coach
date: 2017-03-01T08:00:17+01:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=1802
permalink: /first-steps-as-a-test-automation-coach/
categories:
  - General test automation
tags:
  - coaching
  - consulting
  - test automation
---
&#8220;We want our development teams to take the next step towards adopting Continuous Delivery by giving their test automation efforts a boost.&#8221;

That was the task I was given a couple of months ago when I started a new project, this one for a well-known media company here in the Netherlands. Previously, I&#8217;ve mainly been involved in more hands-on test automation implementation projects, meaning I was usually the one designing and implementing the test automation solution (either alone or as part of a team). For this project, however, my position would be much different:

  * There were multiple development teams to be supported (the exact number changed a couple of times during the assignment, but there were at least four at any time), meaning there was no way I was able to spend enough time on the implementation of automated tests for any of those teams.
  * This was a part time assignment, since I only had 2 days per week available due to other commitments. This made it even less possible to get involved in any serious test automation implementation myself.
  * Each development team was responsible for its own line of products and could make their own decisions on the technology stack to be used (within a certain bandwidth), most of which I&#8217;ve never worked with or even heard of before (GraphQL, for example), making it even less feasible to contribute to any actual tests.

Instead, at the start of the project, we decided that I would act more as a test automation coach of sorts, leaving the creation of the test automation to the development teams (which made perfect sense for this client). Something I&#8217;d never done before, so the fact that I was given the chance to do so was a pleasant surprise. Normally, as a contractor, I&#8217;m only hired to do stuff I&#8217;m already experienced in, but I guess that through my resume and the interview I built enough trust for them to hire me for the job anyway. I&#8217;m very grateful for that.

So, what did I do?

**Kickoff**  
As the development teams consisted of about 40 developers in total, with a wide range of levels of experience, background and preferences in technology and programming languages, we (the hiring manager, the client&#8217;s team of testers and myself) thought it would be a good idea to get them at least somewhat on the same level with regards to the concept of test automation. We did this by organizing a number of test automation awareness sessions, in which I presented my view on test automation. The focus of this presentation was mostly on the &#8216;why?&#8217; and the &#8216;what?&#8217; of it, because I quickly figured out that the developers themselves were perfectly capable of figuring out the &#8216;how?&#8217; (an impression I get from a lot of my clients nowadays, by the way).

**Taking inventory of test automation maturity**  
Next up was a series of interviews with all tech leads from the development teams, to see where they stood with regards to test automation, what they already did, what was lacking and what would be a good &#8216;next step&#8217; to allow that team to make the transition towards Continuous Delivery. This information was shared across teams to promote knowledge sharing. You&#8217;d be surprised to find out how often teams are struggling with something that&#8217;s already been solved by another teams just a couple of yards away, without either party knowing of the situation of the other..

**Test automation hackathons**  
The most important and most impactful part of my assignment was organizing a two-day &#8216;hackathon&#8217; (for lack of a better word) for each of the teams (one team at a time). The purpose of this hackathon was to take the team away from the daily grind of developing and delivering production code and have them work on their technical debt with regards to test automation. The rules of the game:

  * Organize the hackathon in a space separate from the work floor, both to give the team the feeling that they were removed from the usual work routine as well as prevent outside interference as much as possible.
  * Organize the hackathon as a Scrum sprint of sorts, with a kickoff/planning session, show and tell/standup twice a day and a demo session and retrospective at the end.
  * Deliver working software, meaning that I&#8217;d rather have one test that works and is fully integrated into the build and deployment process than fifty tests that do not run automatically. The most difficult hurdles are never in creating more tests, once you&#8217;ve got the groundwork taken care of.
  * Focus on a subject that the teams wants to have, but does not currently have. For some teams, this was unit testing for a specific type of software, for some it was end-to-end testing, or build pipelines, and in one case production monitoring. The subject didn&#8217;t matter, as long as it had to do with software quality and it was something the team did not already do.

**Results**  
The hackathons worked out really well. Monitoring the teams after they had completed their &#8216;two days of test automation&#8217; I could see they had indeed taken a step in the right direction, being more active on the test automation front and having a better awareness of what they were working towards. Mission accomplished!

As my assignment ended after that, I can&#8217;t say anything about the long term effects, unfortunately, but I&#8217;m convinced that the testers themselves can take over the role of test (automation) coach perfectly well. I will stay in touch with the client regularly to see how they&#8217;re doing, of course.

**What did I learn?**  
As I said, this was my first time acting more as a coach than as an engineer, so naturally I learned a lot of things myself:

  * Hackathons are a great way of improving test automation efforts for development teams. Pulling teams away from their daily grind and having them focus on improving their automation efforts is both useful and fun. I was lucky that management support was not an issue, so your mileage may vary, but my point stands.
  * I (think I) have what it takes to be a test automation coach. This was the biggest breakthrough for me personally. As a pretty introverted person who likes to play around with tools regularly, it was hard initially to step away from the keyboard and fight the urge to create tests myself, helping other people to become better at it instead. It IS the way forward for my career, though, I think, because I&#8217;ve yet again seen that there&#8217;s no one better at creating automated tests than a (good) developer. What I can bring to the table is experience and guidance as to the &#8216;why?&#8217; and &#8216;what?&#8217;.
  * Part time projects are great in terms of flexibility, especially when you find yourself in a coaching role. You can organize a hackathon, give teams guidance points and suggestions what to work on, and come back a couple of days later and see how they&#8217;re doing, evaluate, discuss and let them take the next step.

In short, my first adventure as a test automation coach has been a great experience. I&#8217;m looking forward to the next one!