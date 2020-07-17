---
id: 1954
title: Defining Continuous Testing for myself
date: 2017-08-16T10:00:02+02:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=1954
permalink: /defining-continuous-testing-for-myself/
categories:
  - General test automation
tags:
  - continuous delivery
  - continuous testing
  - test automation
---
On a couple of recent occasions, I found myself <a href="https://learn.techbeacon.com/units/guide-automation-testing-frameworks-how-build-yours" target="_blank">talking</a> <a href="https://www.youtube.com/watch?v=mpTwbitUtaM" target="_blank">about</a> Continuous Testing and how test automation related to this phenomenon (or buzzword, if you prefer that term). However, to this day I didn&#8217;t have a decent answer to the question of what Continuous Testing (CT) is and how exactly it relates to test automation. Now that I&#8217;m busy preparing another webinar (this time together with the people at <a href="https://www.testim.io/" target="_blank">Testim</a>, but more on that probably in another post), and we find ourselves again talking about CT, I thought it was due time to start carving out a definition for myself. This blog post is much like me thinking out loud, so bear with me.

To start, CT is definitely not equal to test automation, not even to test automation on steroids (i.e., test automation that is actually <a href="http://www.ontestautomation.com/on-supporting-continuous-testing-with-fitr-test-automation/" target="_blank">fast, stable and repeatable</a>. Instead, I see CT as an integrated part of the Continuous Delivery (CD) life cycle:

<a href="http://www.ontestautomation.com/defining-continuous-testing-for-myself/continuous_testing/" rel="attachment wp-att-1955"><img src="http://www.ontestautomation.com/wp-content/uploads/2017/08/continuous_testing.png" alt="Continuous Testing as part of the Continuous Delivery life cycle" width="837" height="568" class="aligncenter size-full wp-image-1955" srcset="https://www.ontestautomation.com/wp-content/uploads/2017/08/continuous_testing.png 837w, https://www.ontestautomation.com/wp-content/uploads/2017/08/continuous_testing-300x204.png 300w, https://www.ontestautomation.com/wp-content/uploads/2017/08/continuous_testing-768x521.png 768w" sizes="(max-width: 837px) 100vw, 837px" /></a>

You could also say that CT is a means of that teams can adopt to support CD while aiming to deliver quality software.

Let&#8217;s take a closer look and dissect the term &#8216;Continuous Testing&#8217;. The first part, &#8216;Continuous&#8217;, to me is a term that means two things in the CD context:

  1. The software that is being created is continuously tested (or, more likely, continually) in a given environment. In other words, any version of the software that enters an environment is immediately subjected to the tests that are associated with that environment. This environment can be a local development environment, a test environment, or even a production environment (where testing is often done in the form of monitoring).
  2. The software that is being created is continuously tested as it passes through environments. In other words: there&#8217;s no deploy that is not being followed by some form of testing, and in case of the local development environment, tests are run before any deployment (or better, commit and build) is being done.

<a href="http://www.ontestautomation.com/defining-continuous-testing-for-myself/continuous_testing_cycles/" rel="attachment wp-att-1956"><img src="http://www.ontestautomation.com/wp-content/uploads/2017/08/continuous_testing_cycles.png" alt="Continuous Testing in two dimensions" width="1059" height="391" class="aligncenter size-full wp-image-1956" srcset="https://www.ontestautomation.com/wp-content/uploads/2017/08/continuous_testing_cycles.png 1059w, https://www.ontestautomation.com/wp-content/uploads/2017/08/continuous_testing_cycles-300x111.png 300w, https://www.ontestautomation.com/wp-content/uploads/2017/08/continuous_testing_cycles-768x284.png 768w, https://www.ontestautomation.com/wp-content/uploads/2017/08/continuous_testing_cycles-1024x378.png 1024w" sizes="(max-width: 1059px) 100vw, 1059px" /></a>

This is not necessarily different from any other software delivery method, but what makes CT (and CD) stand out is that the time between deployments is typically **very short**. And that&#8217;s where the second part of the term Continuous Testing comes into play: &#8216;Testing&#8217;. _How_ is this testing being done? This is where automation often comes into play, as an enabler of fast feedback and software moving through the pipeline fast, yet in a controlled manner.

Teams that want to &#8216;do&#8217; CT and CD simply cannot be blocked by testing as an activity tacked on at the end. Instead, CT requires a shift of mind from traditional testing as an afterthought to testing being ingrained throughout the pipeline. Formalized handoffs and boundaries between environments will have to be replaced by testing activities that act as gatekeepers and safety nets. And where necessary and useful, this testing is supported by tools. In this respect, test automation can be an enabler of Continuous Testing. But (as is so often the case), only if that automation makes sense. Again, I refer to <a href="http://www.ontestautomation.com/on-supporting-continuous-testing-with-fitr-test-automation/" target="_blank">this blog post</a> if you want to know what I mean by &#8216;making sense&#8217; in a CT context.

I still don&#8217;t have a one line, encyclopedia-style definition for Continuous Testing, but at least the thought process I went through to write this (short) blog post helped me put some things into place. Next to <a href="https://leanpub.com/testingindevops" target="_blank">Katrina Clokie&#8217;s book on testing in DevOps</a>, the following articles have been a source of information for me (while being much better written than these ramblings):

<a href="https://learn.techbeacon.com/units/what-continuous-testing-know-basics-core-safety-net-devops-teams" target="_blank">What is continuous testing? Know the basics of this core safety net for DevOps teams</a>  
<a href="https://techbeacon.com/real-world-guide-continuous-testing" target="_blank">A real-world guide to continuous testing</a>  
<a href="https://www.tricentis.com/what-is-continuous-testing/" target="_blank">What is Continuous Testing?</a>  
<a href="https://www.tricentis.com/resource-assets/continuous-testing-vs-test-automation/" target="_blank">Continuous Testing vs. test automation (whitepaper)</a>  
<a href="https://www.sealights.io/blog/automated-testing-vs-continuous-testing/" target="_blank">The Great Debate: Automated Testing vs Continuous Testing</a>

What is your definition of Continuous Testing? Do you have one?