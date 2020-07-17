---
id: 1105
title: Service virtualization implementation roadblocks
date: 2015-10-19T11:35:48+02:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=1105
permalink: /service-virtualization-implementation-roadblocks/
categories:
  - Service virtualization
tags:
  - roadblocks
  - service virtualization
---
In <a href="http://www.ontestautomation.com/category/service-virtualization/" target="_blank">previous posts</a> I have written about some of the merits of service virtualization (SV) as a means of emulating the behaviour of non-existent or hard or expensive to access software components in a distributed environment. In this post, I want to take another viewpoint and discuss some of the roadblocks you may encounter when implementing SV &#8211; and how to handle them.

**What if there&#8217;s no traffic to capture?**  
In case the service or component to be modeled does not yet exist or is otherwise unavailable, there&#8217;s no way to monitor and capture any traffic between your system under test and the &#8216;live&#8217; dependency. This means you need to create the virtual asset and its behaviour more or less from scratch. Of course, modern SV tools will do some of the work for you, for example by creating a skeleton for the virtual asset from a WSDL or RAML specification, but there&#8217;s a bit (or even a lot) of work left to do if you&#8217;re dealing with anything but the most trivial of dependencies. This means more time and therefore more money is needed to create a suitable virtual dependency.

And please don&#8217;t assume that there&#8217;s a complete specification of the component to be virtualized available for you to work from. Granted, a WSDL or RAML specification or anything similar can be obtained fairly often beforehand, but those express only a small portion of the required behaviour. They don&#8217;t state how specific combinations of request data or specific sequences of interactions are handled, for example. It&#8217;s therefore vital to discuss the required behaviour for the virtual asset with stakeholders early and often. Find out what the virtual asset needs to do (and what not) before starting to model it.

**Don&#8217;t rely too much on capture/playback of virtual asset behaviour**  
On the other hand, if the component to be virtualized IS available for capture/playback of traffic, please don&#8217;t rely too much on the capture/playback function of your SV tool of choice. Although capture/playback is a wonderful way to quickly create a virtual asset that can handle a couple of fixed request/response pairs, I think that there&#8217;s no way that an SV approach that relies solely on capture/playback is sustainable in the long run. To create assets that are usable, flexible and maintainable, you&#8217;ll always need to do some adjustments and additional modeling, likely to an extent where it&#8217;s better to start modeling your asset from the ground up rather than working with prerecorded traffic. In this way, it&#8217;s surprisingly much like test automation!

Having said that, the capture/playback approach certainly has its merits, most importantly when analyzing request/response traffic for components you&#8217;re not completely familiar with, or for which no complete specifications and/or subject matter experts are available to clarify any holes in the specs.

**How to handle custom or proprietary protocols and message formats**  
In a perfect world &#8211; at least from an integration and compatibility point of view &#8211; all message exchanges between dependent systems are executed using common, open and standardized protocols (such as REST or SOAP) and using standardized message formats (such as JSON or XML). Unfortunately, in practice, there are a lot of situations where less common or even proprietary protocols and message formats are used. Even though modern SV tools support many of these out of the box, they don&#8217;t cover the full spectrum, meaning that you will at some point in time encounter a situation where you&#8217;ll need to virtualize the behaviour for an application that uses some exotic message protocol or format (or both, if you&#8217;re really, really lucky).

In that case, you basically have two options:

  * Build a custom protocol listener and/or message handler to process and respond to incoming messages
  * Forget about using SV for virtualizing this dependency altogether

While it might seem that I&#8217;ve put the second option there in jest, it should actually be considered a serious option. Implementing SV is always a matter of balancing costs and benefits, and the effort needed to implement custom protocol listeners and message handlers might just not be outweighed by the benefits..

One possible solution to this problem might be to use <a href="https://communities.ca.com/community/ca-devtest-community/blog/2014/10/13/odp-makes-moot-the-question-do-you-support-that-protocol" target="_blank">Opaque Data Processing</a> or ODP, a technique that matches requests based on byte-level patterns and provides accurate responses based on a transaction library. Currently, this technique is only available for <a href="http://www.ca.com/us/devcenter/ca-service-virtualization.aspx" target="_blank">CA Service Virtualization</a> users, though. Also, I&#8217;m personally not too sure whether ODP will solve all of your problems, especially when it comes to parameterization and flexibility (see also my previous point on relying too much on capture/playback), but it&#8217;s definitely worth a try if you&#8217;re a (prospective) CA SV user.

**Some concluding considerations**  
Wrapping up, I&#8217;d like to offer the following suggestions for anyone encountering any of the roadblocks on his or her SV implementation journey:

  * Always go with modeling instead of capture/playback, as this allows you to create a more flexible and better maintainable virtual asset
  * Use capture/playback for analysis of message / interactions flows. This can give you useful insights in the way information is exchanged between your system under test and its dependencies
  * Discuss required behaviour early and often to avoid unnecessary (re-)work
  * Avoid custom protocol listeners and message handlers wherever possible, or at the very least do a thorough cost/benefit analysis

Happy virtualizing!