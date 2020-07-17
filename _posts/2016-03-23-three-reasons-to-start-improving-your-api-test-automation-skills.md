---
id: 1340
title: Three reasons to start improving your API test automation skills
date: 2016-03-23T07:00:58+01:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=1340
permalink: /three-reasons-to-start-improving-your-api-test-automation-skills/
categories:
  - API testing
tags:
  - api testing
  - skill
  - test automation pyramid
---
Modern applications and software development methods have changed the requirements for testers and the skills they need to possess to add real value to their clients and projects. One of these emerging and sought after skills is the ability to design and execute automated tests for APIs. In this post, I will give you three reasons why it might be useful for you to start improving your API test automation skills.

<a href="http://www.ontestautomation.com/?attachment_id=1342" rel="attachment wp-att-1342"><img src="http://www.ontestautomation.com/wp-content/uploads/2016/03/api_wordcloud.jpg" alt="API word cloud" width="698" height="400" class="aligncenter size-full wp-image-1342" srcset="https://www.ontestautomation.com/wp-content/uploads/2016/03/api_wordcloud.jpg 698w, https://www.ontestautomation.com/wp-content/uploads/2016/03/api_wordcloud-300x172.jpg 300w" sizes="(max-width: 698px) 100vw, 698px" /></a>

**APIs are everywhere**  
The first reason why you should invest in your API test automation skills is a simple question of demand and supply: APIs are becoming ever more present in current IT solutions. From mobile applications to the Internet of Things, many modern systems and applications expose their data, their business logic or both through APIs. Whether you&#8217;re building an application that uses APIs to expose data or logic to the outside world, or you&#8217;re on the other side as an API consumer, you need to be able to perform tests on APIs. Otherwise, how are you going to ensure that an API and its integration with the outside world function as expected?

Oh, and if you&#8217;re testing an application that consumes a third-party API, please don&#8217;t fall into the trap of assuming that this API you&#8217;re using works perfectly as designed, or that integration with your own application will be seamless. Can anyone really assure you that that business-critical third party API you&#8217;re relying on has been tested for your specific situation and requirements? Thought not. Make sure it does and do some proper testing on it!

**API test automation hits the sweet spot between speed and coverage**  
The second reason why API test automation can be very useful is that automated checks on the API level hit the sweet spot between speed of execution and coverage of application features. Compared to the two other types of automated tests in the <a href="https://www.mountaingoatsoftware.com/blog/the-forgotten-layer-of-the-test-automation-pyramid" target="_blank">test automation pyramid</a>, API-level tests tend to:

  * _execute faster than user interface-driven tests_. User interface-driven automated tests, such as those written in Selenium WebDriver, need to fire up a browser and render several web pages every time a test is executed. When your tests go through a lot of different pages, execution time skyrockets. API-level tests, on the other hand, have to wait for a server responding to HTTP calls only. The only client-side processing that needs to be done is parsing the response and performing validation checks, for example on specific elements in the response. This is a lot faster than sending (possibly many) HTTP requests to a web server to fetch all objects required for a web page and then waiting until your browser has finished rendering the page.
  * _cover more business logic than unit tests_. Yes, you should have unit tests. And they should cover as much of the internal workings of your application that make up the business logic. However, there&#8217;s only so much unit tests can do. For example, unit tests can check whether the salary of a given employee is calculated correctly in the back end. They cannot guarantee that the same salary is correctly sent out to the front end layer of your application (or to the IRS) upon request, though. For this, you will need to perform tests at a higher level in your application, and API tests are usually perfect for that.

**Automated API tests tend to be more reliable**  
Apart from having the right mixture of speed of execution and coverage of functional aspects, API-level automated checks have another big advantage over user interface-driven automated tests: they&#8217;re usually far more reliable. User interface-driven tests constantly have to walk the wobbly rope of synchronization, ever changing (or &#8216;improving&#8217;) user interface designs, dynamic element identification methods, etc. API definitions and interfaces on the other hand are amongst the most stable parts of an application: they follow standardized specification formats (such as WSDL for SOAP or WADL, RAML or Swagger for REST) and once agreed upon, an API does not usually change all that much. This applies especially to outward-facing APIs. For example, Google can easily change its Gmail user interface without this impacting end users (apart from annoyance, maybe, but that&#8217;s a different story altogether), but sudden radical changes to its <a href="https://developers.google.com/gmail/api/" target="_blank">Gmail API</a> would render a lot of third-party applications that have Gmail integration useless. Therefore, changes to an API will usually be a lot fewer and further between, resulting in less maintenance required for your API-level automated tests.

<a href="http://www.ontestautomation.com/?attachment_id=1343" rel="attachment wp-att-1343"><img src="http://www.ontestautomation.com/wp-content/uploads/2016/03/sample_API_test.png" alt="A sample API test in REST Assured" width="582" height="243" class="aligncenter size-full wp-image-1343" srcset="https://www.ontestautomation.com/wp-content/uploads/2016/03/sample_API_test.png 582w, https://www.ontestautomation.com/wp-content/uploads/2016/03/sample_API_test-300x125.png 300w" sizes="(max-width: 582px) 100vw, 582px" /></a>

**Further reading**  
I hope this blog post has given you some insight into why I think API test automation skills are a valuable asset for any tester with an interest in test automation. If you want to read more on API testing and test automation, I highly recommend the API Testing Dojo on the <a href="https://www.soapui.org/testing-dojo/welcome-to-the-dojo/overview.html" target="_blank">SoapUI website</a>. Additionally, you can also check out my other posts on API testing <a href="http://www.ontestautomation.com/category/api-testing/" target="_blank">here</a>.