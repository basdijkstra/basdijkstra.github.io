---
id: 368
title: An introduction to service virtualization
date: 2014-04-15T10:44:32+02:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=368
permalink: /an-introduction-to-service-virtualization/
categories:
  - Service virtualization
tags:
  - case study
  - service virtualization
  - web services
---
One of the concepts that is rapidly gaining popularity within the testing world &#8211; and in IT in general &#8211; is service virtualization (SV). This post provides a quick introduction to the SV concept and the way it leverages automated and manual testing efforts and thereby software development in general.

**What is service virtualization?**  
SV is the concept of simulating the behaviour of an application or resource that is required in order to perform certain types of tests, in case this resource is not readily available or the availability or use of this resource is too expensive. In other words, SV is aimed at the removal of traditional dependencies in software development when it comes to the availability of systems and environments. In this way, SV is complementary to other forms of virtualization, such as hardware (VPS) or operating system (VMware and similar solutions) virtualization.

Behaviour simulation is carried out using virtual assets, which are pieces of software that mimic application behaviour in some way. Although SV started out with the simulation of web service behaviour, modern SV tools can simulate all kinds of communication that is performed over common message protocols. In this way, SV can be used to simulate database transactions, mainframe interaction, etc.

<div id="attachment_370" style="width: 959px" class="wp-caption aligncenter">
  <a href="http://www.ontestautomation.com/wp-content/uploads/2014/04/service_virtualization_concept.png"><img aria-describedby="caption-attachment-370" src="http://www.ontestautomation.com/wp-content/uploads/2014/04/service_virtualization_concept.png" alt="http://www.w3.org/WAI/intro/people-use-web/Overview.html" width="949" height="407" class="size-full wp-image-370" srcset="https://www.ontestautomation.com/wp-content/uploads/2014/04/service_virtualization_concept.png 949w, https://www.ontestautomation.com/wp-content/uploads/2014/04/service_virtualization_concept-300x128.png 300w" sizes="(max-width: 949px) 100vw, 949px" /></a>
  
  <p id="caption-attachment-370" class="wp-caption-text">
    From a &#8216;live&#8217; test environment to a virtual test environment
  </p>
</div>

**What are the benefits of using service virtualization?**  
As mentioned in the first paragraph of this post, SV can significantly speed up the development process in case required resources:

  * are not available during (part of) the test cycle, thereby delaying tests or negatively influencing test coverage;
  * are too expensive to keep alive (e.g., test environments need to be maintained or rented continuously even though access is required only a couple of times per year);
  * cannot readily emulate the behaviour required for certain types of test cases;
  * are shared throughout different development teams, negatively influencing resource availability.

**Service virtualization tools**  
Currently, four commercial service virtualization tools are available on the market:

  * [Parasoft Virtualize](http://www.parasoft.com/service-virtualization)
  * [CA Lisa Service Virtualization](http://www.ca.com/us/products/detail/ca-lisa.aspx?intcmp=headernav)
  * [IBM Rational Test Virtualization Server](http://www-01.ibm.com/software/rational/servicevirtualization/products/)
  * [HP Service Virtualization](http://www8.hp.com/us/en/software-solutions/service-virtualization/)

Furthermore, several open source service virtualization projects have emerged in recent years, such as [WireMock](http://wiremock.org/) and [Betamax](http://freeside.co/betamax/). These offer significantly less features, obviously, but they just might fit your project requirements nonetheless, making them worthy of an evaluation.

Personally, I have extensive experience using Parasoft Virtualize and have been able to successfully implement it in a number of projects for our customers. Results have been excellent, as can be seen in the following case study.

**A case study**  
The central order management application at one of our customers relied heavily on an external resource for the successful provisioning of orders. This external resource requires manual configuration for each order that is created, resulting in the test team having to file requests for configuration changes for each test case. The delay for this configuration could be as much as a week, resulting in severe delays in testing and possible test coverage (as testers could only create a small amount of orders per test cycle).

Using service virtualization to simulate the behaviour of this external resource, this dependency has been removed altogether. The virtual asset implementing the external resource behaviour processes new orders in a matter of minutes, as opposed to weeks in case the &#8216;real&#8217; external resource is required. Using SV, testers can provision orders much faster and are able to achieve much higher test coverage as a result. This has resulted in a significant increase in software quality. Also, SV has been a key factor in the switch to an Agile development process. Without SV, the short development and testing cycles associated with the Agile software development method would not have been possible.

[Service virtualization on Wikipedia](http://en.wikipedia.org/wiki/Service_virtualization)