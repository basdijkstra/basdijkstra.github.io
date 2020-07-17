---
id: 829
title: Should virtual assets contain business logic?
date: 2015-03-30T11:35:48+02:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=829
permalink: /should-virtual-assets-contain-business-logic/
categories:
  - Best practices
  - Service virtualization
tags:
  - service virtualization
---
A while ago I read an interesting [discussion](https://www.linkedin.com/groups/Service-Virtualization-Data-driven-program-4267305.S.5938503508822081536) in the LinkedIn Service Virtualization group that centered around the question whether or not to include business logic in virtual assets when implementing service virtualization. The general consensus was a clear NO, you should never include business logic in your virtual assets. The reason for this is that it makes them unnecessarily complex and increases maintenance efforts whenever the implementation (or even just the interface) of the service being simulated changes. In general, it is considered better to make your virtual assets purely data driven, and to keep full control over the data that is sent to the virtual asset, so that you can easily manage the data that is to be returned by it.

[<img src="http://www.ontestautomation.com/wp-content/uploads/2015/03/data_driven_service_virtualization.png" alt="Data driven virtual assets" width="766" height="359" class="aligncenter size-full wp-image-831" srcset="https://www.ontestautomation.com/wp-content/uploads/2015/03/data_driven_service_virtualization.png 766w, https://www.ontestautomation.com/wp-content/uploads/2015/03/data_driven_service_virtualization-300x141.png 300w" sizes="(max-width: 766px) 100vw, 766px" />](http://www.ontestautomation.com/wp-content/uploads/2015/03/data_driven_service_virtualization.png)

In general, I agree with the opinions voiced there. It is considered good practice to keep virtual assets as simple as possible, and an important way to achieve this is by adopting the aforementioned data driven approach. However, in a recent project where I was working on a service virtualization implementation I had no other choice than to implement a reasonable amount of business logic into some of the virtual assets.

Why? Let&#8217;s have a look.

The organization that implemented service virtualization was a central administrative body for higher education in the Netherlands, let&#8217;s call them C (for Client). Students can use C&#8217;s core application to register for university and college programs, and all information exchange between educational institutions and government institutions responsible for education went through this application as well. So, on one side we have the educational institutions, on the other side we have the government (so to speak). The virtual assets implemented simulated the interface between C and the government. The test environment for the government interface was used by the C test team, but they also provided their own test environment to the testers at the universities and colleges who wanted to do end-to-end tests:

[<img src="http://www.ontestautomation.com/wp-content/uploads/2015/03/test_situation.png" alt="The test situation" width="814" height="415" class="aligncenter size-full wp-image-834" srcset="https://www.ontestautomation.com/wp-content/uploads/2015/03/test_situation.png 814w, https://www.ontestautomation.com/wp-content/uploads/2015/03/test_situation-300x153.png 300w" sizes="(max-width: 814px) 100vw, 814px" />](http://www.ontestautomation.com/wp-content/uploads/2015/03/test_situation.png)

As there were a lot of different universities (around 20) using the C test environment for their end-to-end tests, coordinating and centralizing the test data they were using was a logistical nightmare. They each received a number of test students they could use for their tests, but apart from that, C had no control over the amount and nature of tests that were performed through their test environment. As a result, there was no way to determine the amount and state of test data sent to and from the virtual asset at any given point in time.

Moreover, the virtual asset needed to remember (part of) the test data that it received. These data were required in the calculation of the response to future requests. For example, one of the operations supported by the virtual asset was determining whether a student was eligible for a certain type of financial support. This depended on the number and type of education he or she had previously enrolled in and finished.

To enable the virtual asset to remember received data (and essentially become stateful), we hooked up a simple database to the virtual asset and stored the data entity updates we received, such as student enrollments, students finishing an education, etc., in that database. These data were then used to determine any response values that depended on both data and business logic. Simple data retrieval requests were of course implemented in data driven manner.

This deliberate choice to implement a certain &#8211; but definitely limited &#8211; amount of business logic in the virtual asset enabled not only company C, but also the organizations that depended on C&#8217;s test environments, to successfully perform realistic end-to-end test scenarios.

But&#8230;

Once the people at company C saw the power of service virtualization, they kept filing requests for increasingly complex business logic to be implemented in the virtual asset. As any good service virtualization consultant should do, I was very cautious in implementing these features. My main argument was that Icould implement these features rather easily, but someone eventually would have to maintain them, at which point I would be long gone. In the end, we found an agreeable mix of realistic behaviour (with the added complexity) and maintainability. They were happy because they had a virtual asset they could use and provide to their partners, I was happy because I delivered a successful and maintainable service virtualization implementation.

So, yes, in most cases you should make sure your virtual assets are purely data driven when you&#8217;re implementing service virtualization. However, in some situations, implementing a bit of business logic might just make the difference between an average and a great implementation. Just remember to keep it to the bare minimum.