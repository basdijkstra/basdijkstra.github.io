---
id: 531
title: API testing best practices
date: 2014-07-25T08:00:14+02:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=531
permalink: /api-testing-best-practices/
categories:
  - API testing
  - Best practices
tags:
  - api testing
  - best practice
---
_This is the second post in a three-part series on API testing. The first post, which can be found [here](http://www.ontestautomation.com/api-testing-skills-why-you-need-them/ "API testing skills: why you need them"), provided a brief introduction on APIs, API testing and its relevance to the testing world. This post will feature some best practices for everybody involved in API testing. The third and final post will contain some useful code example for those of you looking to build your own automated API testing framework._

As was mentioned in the first post in this mini-series, API test execution differs from user interface-based testing since APIs are designed for communication between systems or system components rather than between a system or system component and a human being. This introduces some challenges to testing APIs, which I will try to tackle here.

**API communication**  
Whereas a lot of testing on the user interface level is still done by hand (and rightfully so), this is impossible for API testing; you need a tool to communicate with APIs. There are a lot of tools available on the market. Some of the best known tools that are specifically targeted towards API testing are:

  * [Parasoft SOAtest](http://www.parasoft.com/soatest)
  * SmartBear SoapUI ([free](http://www.soapui.org/) or [pro](http://smartbear.com/products/qa-tools/web-service-testing-tool/))
  * HP Service Test (as part of [HP Unified Functional Testing](http://www8.hp.com/nl/nl/software-solutions/unified-functional-testing-automated-testing/))

I have extensive experience with SOAtest and limited experience with SoapUI and can vouch for their usefulness in API testing.

**Structuring tests**  
An API usually consists of several methods or operations that can be tested individually as well as through the setup of test scenarios. These test scenarios are usually constructed by stringing together multiple API calls. I suggest a three step approach to testing any API:

  1. Perform syntax testing of individual methods or operations
  2. Perform functional testing of individual methods or operations
  3. Construct and execute test scenarios

_Syntax testing_  
This type of testing is performed to check whether the method or operation accepts correct input and rejects incorrect input. For example, syntax testing determines whether:

  * Leaving mandatory fields empty results in an error
  * Optional fields are accepted as expected
  * Filling fields with incorrect data types (for example, putting a text value into an integer field) results in an error

_Functional testing of individual operations or methods_  
This type of testing is performed to check whether the method or operations performs its intended action correctly. For example:

  * Is calculation X performed correctly when calling operation / method Y with parameters A, B and C?
  * Is data stored correctly for future use when calling a setter method?
  * Does calling a getter method retrieve the correct information?

_Test scenarios_  
Finally, when individual methods or operations have been tested successfully, method calls can be strung together to emulate business processes, For example:  
[<img src="http://www.ontestautomation.com/wp-content/uploads/2014/07/api_test_scenarios.png" alt="API test scenarios" width="819" height="693" class="aligncenter size-full wp-image-536" srcset="https://www.ontestautomation.com/wp-content/uploads/2014/07/api_test_scenarios.png 819w, https://www.ontestautomation.com/wp-content/uploads/2014/07/api_test_scenarios-300x253.png 300w" sizes="(max-width: 819px) 100vw, 819px" />](http://www.ontestautomation.com/wp-content/uploads/2014/07/api_test_scenarios.png)  
You see that this approach is not unlike user interface-based testing, where you first test individual components for their correct behaviour before executing end-to-end test scenarios.

**API virtualization**  
When testing systems of interconnected components, the availability of some of the components required for testing might be limited at the time of testing (or they might not be available at all). Reasons for limited availability of a component might be:

  * The component itself is not yet developed
  * The component features insufficient or otherwise unusable test data
  * The component is shared with other teams and therefore cannot be freely used

In any of these cases, virtualization of the API can be a valuable solution, enabling testing to continue as planned. Several levels of API virtualization exist:

  * Mocking &#8211; This is normally done for code objects using a framework such as [Mockito](http://www.ontestautomation.com/up-and-running-with-mockito/ "Up and running with: Mockito")
  * Stubbing &#8211; this is used to create a simple emulation of an API, mostly used for SOAP and REST web services
  * [Virtualization](http://www.ontestautomation.com/an-introduction-to-service-virtualization/ "An introduction to service virtualization") &#8211; This is the most advanced technique of the three, enabling the simulation of behaviour of complex components, including back-end database connectivity and transport protocols other than HTTP

**Non-functional testing**  
As with all software components, APIs can (and should!) be tested for characteristics other than functionality. Some of the most important nonfunctional API test types that should at least be considered are:

  * Security testing &#8211; is the API accessible to those who are allowed to use it and inaccessible to those without the correct permissions?
  * Performance &#8211; Especially for web services: are the response times acceptable, even under a high load?
  * Interoperability and connectivity &#8211; can be API be consumed in the agreed manner and does it connect to other components as expected?

Most of the high-end API testing tools offer solutions for execution of these (and many other types of) nonfunctional test types.

More useful API testing best practices can again be found in the [API Testing Dojo](http://www.soapui.org/Best-Practices/introduction.html).

Do you have any additional API testing best practices you would like to share with the world?