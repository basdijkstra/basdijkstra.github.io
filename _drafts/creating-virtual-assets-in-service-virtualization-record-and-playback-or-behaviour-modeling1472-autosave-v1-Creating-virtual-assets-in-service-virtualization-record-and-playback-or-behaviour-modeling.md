---
id: 1477
title: 'Creating virtual assets in service virtualization: record and playback or behaviour modeling?'
date: 2016-06-06T13:15:19+02:00
author: Bas
layout: revision
guid: http://www.ontestautomation.com/1472-autosave-v1/
permalink: /1472-autosave-v1/
---
When you&#8217;re implementing <a href="https://en.wikipedia.org/wiki/Service_virtualization" target="_blank">service virtualization</a>, there are basically two different options for the creation of your virtual assets. In this post, I would like to take a closer look at both of these approaches and discuss why I think one should be recommended over the other. Spoiler alert: as with so many things, this is never a matter of black or white. There are always situations where the use of one approach can be preferred over using the other. You&#8217;ll see as we dive deeper into this subject.

**Record and playback**  
The first approach for creating virtual assets is by first using your service virtualization solution as a proxy to capture request-response pairs (traffic) sent between your application under test and the dependency that is ultimately being virtualized.

<a href="http://www.ontestautomation.com/?attachment_id=1473" rel="attachment wp-att-1473"><img src="http://www.ontestautomation.com/wp-content/uploads/2016/06/from_record_to_playback.png" alt="Creating virtual assets using record and playback" width="1576" height="521" class="aligncenter size-full wp-image-1473" srcset="https://www.ontestautomation.com/wp-content/uploads/2016/06/from_record_to_playback.png 1576w, https://www.ontestautomation.com/wp-content/uploads/2016/06/from_record_to_playback-300x99.png 300w, https://www.ontestautomation.com/wp-content/uploads/2016/06/from_record_to_playback-768x254.png 768w, https://www.ontestautomation.com/wp-content/uploads/2016/06/from_record_to_playback-1024x339.png 1024w" sizes="(max-width: 1576px) 100vw, 1576px" /></a>

This approach does have a number of advantages:

  * Using the record and playback approach, you can have a functional virtual asset up and running in a couple of minutes. There is no need for behaviour modeling or time-consuming response creation.
  * This approach suits situations where there is no formal specification of the structure or contents of the traffic that passes between the application under test and the dependency to be virtualized.

However, there are also a number of downsides:

  * This approach can only be used when the dependency is available for traffic recording. Often, access to the dependency is restricted or the dependency simply does not exist at all.
  * The behaviour that will be exerted by the virtual asset is restricted to the request-response pairs that were previously recorded. This puts a severe limit to the flexibility of the virtual asset. A simple example would be the fact that the virtual asset will never be able to generate unique message ID&#8217;s when you adopt a pure record and playback strategy &#8211; a blocker for a lot of systems.

When looking at these advantages and disadvantages, one can easily see that applying record and playback for service virtualization is much the same as with test automation: it is a good aproach for quickly generating virtual assets, but there are severe limits with regards to flexibility, plus maintenance will most likely be a pain in the a**. Having said that, using record and playback CAN be beneficial, for instance to discover how request and response messages are structured when you&#8217;re confronted with a lack of formal message specifications (and yes, that happens more often than you&#8217;d think&#8230;).

**Modeling virtual asset behaviour**  
As an alternative, virtual asset behaviour can be modeled from the ground up, based on service and/or request and response message specifications. For example, any serious service virtualization solution allows you to generate virtual assets from WSDL or XSD documents (for SOAP-based web services) or WADL, RAML or Swagger specifications (for RESTful web services). To make them more flexible, data sources such as databases or Excel spreadsheets can be used to make the virtual assets data driven.

<a href="http://www.ontestautomation.com/?attachment_id=1474" rel="attachment wp-att-1474"><img src="http://www.ontestautomation.com/wp-content/uploads/2016/06/behaviour_modeling.png" alt="Creating virtual assets using behaviour modeling" width="1544" height="669" class="aligncenter size-full wp-image-1474" srcset="https://www.ontestautomation.com/wp-content/uploads/2016/06/behaviour_modeling.png 1544w, https://www.ontestautomation.com/wp-content/uploads/2016/06/behaviour_modeling-300x130.png 300w, https://www.ontestautomation.com/wp-content/uploads/2016/06/behaviour_modeling-768x333.png 768w, https://www.ontestautomation.com/wp-content/uploads/2016/06/behaviour_modeling-1024x444.png 1024w" sizes="(max-width: 1544px) 100vw, 1544px" /></a>

Some advantages of creating virtual assets from scratch are:

  * The resulting virtual assets are generally easier to maintain since their structure is linked to a specification. When the service interface specifications change, updating your virtual asset to reflect the latest interface definition version can often be done with a single click of a button.
  * Since the virtual asset is created using specifications rather than a limited set of request-response pairs, theoretically all incoming requests can be processed and responded to, no matter the data they contain. In practice, there might still be some situations where an incoming request cannot be handled, at least not without some more advanced virtual asset configuration, but the number of requests that CAN be handled is much higher compared to the record and playback approach.

Of course, the behaviour modeling approach too has some disadvantages:

  * It takes longer to create the virtual assets. Where record and playback generates a working asset in minutes, modeling a virtual asset from scratch might take longer, and the time required grows with the number of message types to be supported and the size of and number of elements in the response messages.
  * As said before, sometimes message or web service specifications for the dependency to be virtualized are not readily available, for example when the dependency is still under development. This makes it hard to create virtual assets (although in this case record and playback isn&#8217;t an option either).

**So, which approach should I choose?**  
After reading the arguments presented in this blog post, it shouldn&#8217;t be too hard to deduce that I am a big fan of creating virtual assets using the behaviour modeling approach. I firmly believe that although the initial setup of a virtual asset takes some additional work compared to record and playback, behaviour modeling results in virtual assets that are more flexible, more well-versed and better maintainable. There are some cases where using the record and playback approach may be beneficial, including vendor demos that show how easy service virtualization really is (beware of those!). In general, though, you should go for building virtual assets from the ground up.

**Leveraging previously recorded traffic to create more flexible virtual assets**  
Again, I&#8217;m not completely writing off the use of record and playback, espeically since some interesting recent developments open up options to leverage virtual assets created from previously recorded traffic. Perhaps the most interesting option was shown to me recently by Hasan Ferit Enişer, an M.Sc. student from the <a href="http://www.cmpe.boun.edu.tr/" target="_blank">Computer Engineering department</a> at Boğaziçi University. I have been in touch with him on and off for the last year or so. He is doing some interesting research that touches on service virtualization and he&#8217;s looking to apply some of the specification mining theories proposed in <a href="http://people.cs.umass.edu/~brun/pubs/pubs/Krka14fse.pdf" target="_blank">this paper</a> to prerecorded traffic and virtual assets. This research is still in an early phase, so there&#8217;s no telling what the end result will look like and how applicable it will be to industry challenges, but it&#8217;s an interesting development nonetheless and one that I&#8217;ll keep following closely. Hopefully I&#8217;ll be able to write a follow-up post with some interesting results in the not too distant future.