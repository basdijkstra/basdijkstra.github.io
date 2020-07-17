---
id: 862
title: Creating a reusable FitNesse test suite (or how lasagna beats spaghetti)
date: 2015-05-13T14:04:50+02:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=862
permalink: /creating-a-reusable-fitnesse-test-suite/
categories:
  - General test automation
tags:
  - fitnesse
  - maintainability
---
In my current project I am &#8211; aside from other test-related tasks &#8211; responsible for the development and maintenance of an automated regression test suite in <a href="http://fitnesse.org/" target="_blank">FitNesse</a>.

Before starting here in December of last year, I had no prior experience with FitNesse. I had heard of it before, though, and knew it was both a wiki and an automated test framework. I have used a couple of different wiki systems before (mainly Atlassian Confluence), but only for documentation purposes, never for test automation. As you probably know, it&#8217;s very easy to start using a wiki and to start adding content.

However, maintaining a workable structure and making sure that information can be found easily is a different question. If you start to add information to a wiki at will, you&#8217;ll soon end up with a structure that resembles something like this:

[<img src="http://www.ontestautomation.com/wp-content/uploads/2015/05/spaghetti.jpg" alt="It&#039;s easy to make a wiki look like this!" width="610" height="322" class="aligncenter size-full wp-image-863" srcset="https://www.ontestautomation.com/wp-content/uploads/2015/05/spaghetti.jpg 610w, https://www.ontestautomation.com/wp-content/uploads/2015/05/spaghetti-300x158.jpg 300w" sizes="(max-width: 610px) 100vw, 610px" />](http://www.ontestautomation.com/wp-content/uploads/2015/05/spaghetti.jpg)

This is what seemed to have happened with the existing automated test suite as well. It was working decently, but tests were unstructured, there was a lot of duplication and finding your way around was no easy task. Pages were linked to one another randomly and there were several levels of inclusion (at least three or four) in most of the tests.

Now don&#8217;t get me wrong, I like spaghetti (a lot, actually!), but it soon became pretty clear that maintaining and extending such an automated test suite was going to be a daunting, if not impossible task. And as history has taught so many people who started out with test automation: if your tests are not easily maintainable, they&#8217;ll become outdated soon and all previous efforts will be in vain.

So it became pretty clear that some major restructuring was needed to make this automated test suite maintainable and ready for the future. Even more so since the intent is to hand over the test suite to the standing organization once the project finishes (go-live is scheduled for Q3 of this year at the moment).

I have a hard time instructing others something that I don&#8217;t fully understand myself (and who doesn&#8217;t) so I knew I needed to come up with a better structure. I learned some valuable things myself along the way and I will share these lessons with you all in the remainder of this post.

**Lesson 1: Take a good look at the application under test**  
The application under test &#8211; a supply chain suite for an online retailer &#8211; seemed to be a rather complex one at first. At second glance, however, it wasn&#8217;t that complicated a system at all.

The application mainly acts as an accumulator and distributor of information related to various order types, i.e., a message goes into the application, it does some data storage and processing, and another message (or maybe multiple messages) come out again. This means that there are basically three different types of actions that a system-level test should perform:

  1. Send a predefined input message to the system
  2. Check whether the input leads to one or more output messages
  3. Check whether the output message(s) contain(s) the correct information

That was pretty much all there was to the system. All test scenarios consisted of a combination of these three actions, where different input messages (both in message types and message data) triggered different test scenarios.

**Lesson 2: Create reusable building blocks**  
Once I realized the above, I could start to untangle the existing test suite by identifying and isolating reusable building blocks. Each of these blocks was responsible for exactly one of the three action types I identified. This meant I could create blocks (which are essentially wiki pages) for:

  * Sending an input message to the AUT (one page for each input message type)
  * Capturing the resulting output messages (one page for each output message type)
  * Validating the contents of the output messages (one page for each output message type)

I also created pages containing message templates for the messages to be sent, and some helper pages that contained global variables and global scenarios. The latter contain FitNesse fixtures that can be used independent of a message type.

For instance, all message types contain the same header, and therefore message header validation is the same for every message independent of its type and the data it contains. Therefore only a single validation scenario is needed to be able to validate all message headers.

**Lesson 3: Limit the include depth to 1 when creating tests**  
After all building blocks and other helper elements were in place, I could start creating actual tests by simply stringing together the required message templates and building blocks.

[<img src="http://www.ontestautomation.com/wp-content/uploads/2015/05/fitnesse_customerorder_suite.png" alt="The resulting FitNesse test suite structure" width="1010" height="628" class="aligncenter size-full wp-image-865" srcset="https://www.ontestautomation.com/wp-content/uploads/2015/05/fitnesse_customerorder_suite.png 1010w, https://www.ontestautomation.com/wp-content/uploads/2015/05/fitnesse_customerorder_suite-300x187.png 300w" sizes="(max-width: 1010px) 100vw, 1010px" />](http://www.ontestautomation.com/wp-content/uploads/2015/05/fitnesse_customerorder_suite.png)

An example test scenario where an order containing a single article is sent to and handled by a warehouse could look like this:  
`<br />
!include -c GlobalVariables<br />
!include -c GlobalScenarios<br />
!include -c OrderCreationMessageTemplateSingleArticle<br />
!include -c SendOrderCreationMessage (action type 1)<br />
!include -c CaptureWarehouseInstructionMessage (action type 2)<br />
!include -c ValidateWarehouseInstructionMessage (action type 3)<br />
!include -c OrderCompletionMessageTemplateSingleArticle<br />
!include -c SendWarehouseOrderCompletionMessage (action type 1)<br />
!include -c CaptureOrderCompletionFeedbackMessage (action type 2)<br />
!include -c ValidateOrderCompletionFeedbackMessage (action type 3)<br />
`  
As you can see from the example, there was never more than a single level of inclusion as I didn&#8217;t allow building blocks to refer to other building blocks by inclusion. In this way, I was able to reform the existing automated test suite to something that looks a lot more like this:

[<img src="http://www.ontestautomation.com/wp-content/uploads/2015/05/lasagna.png" alt="With a bit of effort your automated FitNesse tests might look like this!" width="650" height="494" class="aligncenter size-full wp-image-869" srcset="https://www.ontestautomation.com/wp-content/uploads/2015/05/lasagna.png 650w, https://www.ontestautomation.com/wp-content/uploads/2015/05/lasagna-300x228.png 300w" sizes="(max-width: 650px) 100vw, 650px" />](http://www.ontestautomation.com/wp-content/uploads/2015/05/lasagna.png)

Fundamentally, it consists of the same ingredients as the spaghetti, but it&#8217;s far more structured and just as tasty!

As a relative FitNesse novice I am very curious to read about your experience with the tool, so please do share your lessons learned in the comments. Maybe we can create even better recipes together!