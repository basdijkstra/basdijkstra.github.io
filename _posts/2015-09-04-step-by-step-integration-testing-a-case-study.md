---
id: 1018
title: 'Step-by-step integration testing: a case study'
date: 2015-09-04T12:52:22+02:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=1018
permalink: /step-by-step-integration-testing-a-case-study/
categories:
  - General test automation
  - Test automation tools
tags:
  - integration testing
  - test automation
---
For the last 9 months or so, I have been working as a tester on a project where we develop and deliver the supply chain suite connected to a brand new highly automated warehouse for a big Dutch retailer. As with so many modern software development projects, we have to deal with a lot of different applications and the information that is exchanged between them. For example, the process of ordering a single article on the website and then processing it all the way until the moment it is on your doorstep involves 10+ applications and multiple times that amount of XML messages.

Testing whether all these applications communicate correctly with one another is not simply a matter of placing an order and seeing what happens. It requires structured testing and a bottom-up approach, starting with the smallest level of integration and moving up until the complete set of applications involved is exercised. In this post, I will try and sketch how we have done this using three levels of integration testing.

First, here&#8217;s a highly simplified overview of what the application landscape looks like. On one side we have the supply chain suite and all other applications containing and managing necessary information. On the other side there&#8217;s the warehouse itself. I have been mostly involved in testing the former, but now that we&#8217;re into the final stages of the project, I am also involved (to an extent) in integration testing between both sides.

[<img src="http://www.ontestautomation.com/wp-content/uploads/2015/09/application_landscape.png" alt="The application landscape" width="1281" height="632" class="aligncenter size-full wp-image-1020" srcset="https://www.ontestautomation.com/wp-content/uploads/2015/09/application_landscape.png 1281w, https://www.ontestautomation.com/wp-content/uploads/2015/09/application_landscape-300x148.png 300w, https://www.ontestautomation.com/wp-content/uploads/2015/09/application_landscape-1024x505.png 1024w" sizes="(max-width: 1281px) 100vw, 1281px" />](http://www.ontestautomation.com/wp-content/uploads/2015/09/application_landscape.png)

**Level 1: message-level integration testing**  
The first level of integration testing that we perform is on the message level. On this level, we check whether message type XYZ can be sent successfully from application A to application B. Virtually all application integration is done using the Microsoft BizTalk platform. To create and perform these tests, we use <a href="http://bizunit.codeplex.com/" target="_blank">BizUnit</a>, a test tool specifically designed for testing BizTalk integrations. Every test follows the same procedure:

  1. Prepare the environment by cleaning relevant input and output queues and file locations
  2. Place the message type to be tested on the relevant BizTalk receive location (a queue or RESTful web service)
  3. Validate whether the message has been processed successfully by BizTalk and placed on the correct send location.
  4. Check whether the messages have been archived properly for auditing purposes
  5. Rinse and repeat for other message flows

Note that on this test level, no checks are performed on the contents of messages. The only checks that are performed concern message processing and routing. BizTalk does not do anything or even care for message contents, it only processes and routes messages based on XML header information. Therefore, it does not make sense to perform message content validations here.

[<img src="http://www.ontestautomation.com/wp-content/uploads/2015/09/bizunit_test_scope.png" alt="Scope of the BizUnit message level tests" width="1589" height="311" class="aligncenter size-full wp-image-1026" srcset="https://www.ontestautomation.com/wp-content/uploads/2015/09/bizunit_test_scope.png 1589w, https://www.ontestautomation.com/wp-content/uploads/2015/09/bizunit_test_scope-300x59.png 300w, https://www.ontestautomation.com/wp-content/uploads/2015/09/bizunit_test_scope-1024x200.png 1024w" sizes="(max-width: 1589px) 100vw, 1589px" />](http://www.ontestautomation.com/wp-content/uploads/2015/09/bizunit_test_scope.png)

**Level 2: business process-level integration**  
The second level of integration testing that is performed focuses on successful completion of different business processes, such as customer orders, customer returns, purchasing, etc. These business processes involve the exchange of information between multiple applications. As the warehouse management system is developed in parallel, that interface is simulated using a custom built simulation tool. On a side note: this is a form of <a href="http://www.ontestautomation.com/category/service-virtualization/" target="_blank">service virtualization</a>.

Tests on this level involve triggering the relevant business process and tracking the process instance as related messages pass through the applications involved. For example, a test on the customer order process is started by creating a new order and verifying amongst other things if the order:

  * can be successfully picked and shipped by the warehouse simulator,
  * is created, updated and closed correctly by the supply chain suite,
  * is administrated correctly in the order manager,
  * triggers the correct stock movements, and
  * successfully triggers the invoicing process

[<img src="http://www.ontestautomation.com/wp-content/uploads/2015/09/business_process_test_scope.png" alt="Scope of the business process tests" width="1397" height="692" class="aligncenter size-full wp-image-1031" srcset="https://www.ontestautomation.com/wp-content/uploads/2015/09/business_process_test_scope.png 1397w, https://www.ontestautomation.com/wp-content/uploads/2015/09/business_process_test_scope-300x149.png 300w, https://www.ontestautomation.com/wp-content/uploads/2015/09/business_process_test_scope-1024x507.png 1024w" sizes="(max-width: 1397px) 100vw, 1397px" />](http://www.ontestautomation.com/wp-content/uploads/2015/09/business_process_test_scope.png)

A number of tests on this level have been automated using <a href="http://fitnesse.org/" target="_blank">FitNesse</a>. For me, this was the first project where I had to use FitNesse, and while it does the job, I haven&#8217;t exactly fallen in love with it yet. Maybe I just don&#8217;t know enough about how to use it properly?

**Level 3: integration with warehouse**  
The third and final level of integration testing was done on the interface between the supply chain suite and connecting applications and the actual warehouse itself. As both systems were developed in parallel, it took quite a bit of time before we finally were able to test this interface properly. And even though our warehouse simulation had been designed and implemented very carefully, and it certainly did a lot for us in speeding up the development process, the first integration tests showed that there is no substitute for the real thing. After lots of bug fixing and retesting, we were able to successfully complete this final level of integration testing.

[<img src="http://www.ontestautomation.com/wp-content/uploads/2015/09/warehouse_integration_test_scope.png" alt="Scope of the warehouse integration tests" width="1401" height="601" class="aligncenter size-full wp-image-1033" srcset="https://www.ontestautomation.com/wp-content/uploads/2015/09/warehouse_integration_test_scope.png 1401w, https://www.ontestautomation.com/wp-content/uploads/2015/09/warehouse_integration_test_scope-300x129.png 300w, https://www.ontestautomation.com/wp-content/uploads/2015/09/warehouse_integration_test_scope-1024x439.png 1024w" sizes="(max-width: 1401px) 100vw, 1401px" />](http://www.ontestautomation.com/wp-content/uploads/2015/09/warehouse_integration_test_scope.png)

For this final level of integration testing, we were not able to use automated tests due to the time required by the warehouse to physically pick and ship the created orders. It would not have made sense to build automated tests that have to wait for an hour or more before a response indicating the order has been shipped is returned from the warehouse. The test cases executed mostly follow the same steps as those in level 2 as they are also focused on executing business processes.

I hope this post has given you some ideas on how to break down the task of integration testing for a large and reasonably complex application landscape. Thinking in different integration levels has certainly helped me to determine which steps and which checks to include in a given test scenario.