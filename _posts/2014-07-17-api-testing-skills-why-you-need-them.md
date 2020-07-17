---
id: 512
title: 'API testing skills: why you need them'
date: 2014-07-17T13:03:39+02:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=512
permalink: /api-testing-skills-why-you-need-them/
categories:
  - API testing
tags:
  - api testing
  - test automation
---
_This is the first post in a three-part series on API testing. This post is an introduction on APIs, API testing and its relevance to the testing world. The second part of this series will feature some best practices for everybody involved in API testing. The third and final post will contain some useful code example for those of you looking to build your own automated API testing framework._

As information systems become more and more distributed and systems and devices become ever more interconnected, the use of APIs has seen exponential growth in the past couple of years. Where traditional (or old-fashioned) computer systems were monolithic in nature, nowadays they are often made up of reusable components that communicate and exchange information with one another through various APIs.

The figure below depicts the growth in number of publicly accessible APIs, as published by [ProgrammableWeb](http://www.programmableweb.com):  
[<img src="http://www.ontestautomation.com/wp-content/uploads/2014/07/api_growth.png" alt="Growth in publicly accessible APIs in recent years" width="1189" height="705" class="aligncenter size-full wp-image-515" srcset="https://www.ontestautomation.com/wp-content/uploads/2014/07/api_growth.png 1189w, https://www.ontestautomation.com/wp-content/uploads/2014/07/api_growth-300x177.png 300w, https://www.ontestautomation.com/wp-content/uploads/2014/07/api_growth-1024x607.png 1024w" sizes="(max-width: 1189px) 100vw, 1189px" />](http://www.ontestautomation.com/wp-content/uploads/2014/07/api_growth.png)

Estimates for the number of publicly available APIs for the coming years range from 300.000 in 2016 to around a million by 2017.

With APIs becoming more and more common and important, proper testing of these APIs has become a hot issue with both API providers and consumers. As a provider, you wouldn&#8217;t want to be associated with an API of poor quality. As a consumer, you wouldn&#8217;t want your software system and/or your business to rely on a buggy API. 

However, as APIs are designed for computer-to-computer interaction, rather than computer-to-user interaction, they do not have a user interface through which the tester can access the API. Moreover, to properly assess whether the output as given by the API is correct, a tester would need to know at least something about the internal workings of the API (i.e., perform white-box testing rather than traditional black-box testing). This might make API testing seem &#8216;hard&#8217; or &#8216;difficult&#8217; for some testers.

What makes API testing even more important is that in the current wave of layered information systems, business rules and business logic is often coded and enforced within the API layer (and not in the user interface or the database layer, for example). This is yet another reason for every development project that features the development or consumption of APIs to pay sufficient attention to API testing.

In the next part of this series, I will present some pointers for those of you who are involved in API testing, are looking to do so, or have been asked to do so. Many of my tips are also featured on the [API Testing Dojo](http://www.soapui.org/Dojo/overview.html). There, you can also find some exercises to test and sharpen your API testing skills. A highly recommended resource.