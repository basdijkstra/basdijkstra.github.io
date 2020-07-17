---
id: 976
title: Stubs, mocks or virtual assets?
date: 2015-07-27T13:42:02+02:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=976
permalink: /stubs-mocks-or-virtual-assets/
categories:
  - API testing
  - Service virtualization
tags:
  - mocks
  - service virtualization
  - stubs
---
If, during the software development process, you stumble upon the need to access software components that:

  * Have not yet been developed,
  * Do not contain sufficient test data,
  * Require access fees, or
  * Are otherwise constrained with regards to accessibility,

there are several options you may consider to work around this issue. In this post, I will introduce three options and explain some of the most important of their characteristics.

Please note that the terms &#8216;stub&#8217; and &#8216;mock&#8217; are often mixed up in practice, so what is defined as a mock here might be called a stub somewhere else and vice versa. However, I tried to use definitions that are more or less agreed upon in the development and testing community.

**Stubs**  
The simplest form of removing dependency constraints is the use of stubs. A stub is a very simple placeholder that does pretty much nothing besides replacing another component. It provides no intelligence, no data driven functionality and no validations. It can be created quickly and is most commonly used by developers to mimick behaviour of objects and components not available in their development environment.

**Mocks**  
Mocks contain a little more intelligence compared to stubs. They are commonly configured to be used for specific test or development purposes. They are used to define and verify expectations with regards to behaviour. For example, a mock service might be configured to always return certain test data in response to a request recevied, so that specific test cases can be executed by testers. The difference between mocks and stubs from a testing perspective can be summarized by the fact that _a mock can cause a test case to fail, whereas a stub can&#8217;t_.

**Virtual assets**  
Virtual assets are simulated components that closely mimic the behaviour of &#8216;the real thing&#8217;. They can take a wide variety of inputs and return responses that their real-life counterpart would return too. They come with data driven capabilities that allow responses (and therefore behaviour) to be configured on the fly, even by people without programming knowledge. Virtual assets should also replicate connectivity to the simulated component by applying the same protocols (JMS, HTTP, etc.) and security configuration (certificates, etc.). The application of virtual assets in test environments is commonly called service virtualization.

[<img src="http://www.ontestautomation.com/wp-content/uploads/2015/07/testing.png" alt="Testing terms related to stubs and mocks" width="837" height="517" class="aligncenter size-full wp-image-982" srcset="https://www.ontestautomation.com/wp-content/uploads/2015/07/testing.png 837w, https://www.ontestautomation.com/wp-content/uploads/2015/07/testing-300x185.png 300w" sizes="(max-width: 837px) 100vw, 837px" />](http://www.ontestautomation.com/wp-content/uploads/2015/07/testing.png)

If you want to read more about component or API stubbing, mocking or virtualization, <a href="http://www.soapui.org/testing-dojo/best-practices/api-mocking.html" target="_blank">this page</a> in the SmartBear API Testing Dojo provides an interesting read. Also, Martin Fowler wrote a great piece on mocks and stubs on <a href="http://martinfowler.com/articles/mocksArentStubs.html" target="_blank">his blog</a> back in 2007.