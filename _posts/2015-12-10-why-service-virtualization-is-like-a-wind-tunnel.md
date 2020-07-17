---
id: 1186
title: Why service virtualization is like a wind tunnel
date: 2015-12-10T11:56:23+01:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=1186
permalink: /why-service-virtualization-is-like-a-wind-tunnel/
categories:
  - Service virtualization
tags:
  - service virtualization
  - testing
---
This week I attended the second edition of the <a href="http://continuous-delivery-conference.com" target="_blank">Continuous Delivery Conference</a> here in the Netherlands. It was a very interesting day with some good keynotes and presentations, plus I had a lot of interesting discussions with both old and new acquaintances, something that really adds value to a conference for me as an independent consultant. But that&#8217;s not what this post is about.. Rather it&#8217;s about something that struck me when listening to one of the presentations. I&#8217;m not sure who was talking as I made notes only much later, but if it was you, please reveal yourself and come get your credits! The presenter briefly discussed service virtualization as a tool to use in the continuous delivery pipeline and compared it to using a wind tunnel for investigating the aeodynamic properties of cars or airplanes. This struck me as a very solid analogy, so much that I would like to share it with you here.

<a href="http://www.ontestautomation.com/why-service-virtualization-is-like-a-wind-tunnel/wind-tunnel-testing/" rel="attachment wp-att-1187"><img src="http://www.ontestautomation.com/wp-content/uploads/2015/12/wind-tunnel-testing.jpg" alt="Testing in a wind tunnel" width="1280" height="852" class="aligncenter size-full wp-image-1187" srcset="https://www.ontestautomation.com/wp-content/uploads/2015/12/wind-tunnel-testing.jpg 1280w, https://www.ontestautomation.com/wp-content/uploads/2015/12/wind-tunnel-testing-300x200.jpg 300w, https://www.ontestautomation.com/wp-content/uploads/2015/12/wind-tunnel-testing-768x511.jpg 768w, https://www.ontestautomation.com/wp-content/uploads/2015/12/wind-tunnel-testing-1024x682.jpg 1024w" sizes="(max-width: 1280px) 100vw, 1280px" /></a>

So why do I think service virtualization IS like wind tunnel testing, exactly?

**It allows for executing tests in a controlled environment**  
Testing car or airplane aerodynamics only yields valuable results if you know exactly what conditions applied and which input parameters (wind speed, angle, variation, etc.) were used to obtain the results. Similarly, when you&#8217;re testing any distributed system with external dependencies, you can only safely rely on the test results when you know exactly how those dependencies are behaving. With modern-day highly distributed applications &#8211; this especially applies to applications built using microservice architectures &#8211; not all dependencies are necessarily under your control anymore. If you want to have full control over the behaviour of those dependencies for stable and reliable testing, service virtualization is an excellent option.

**Tests can be repeated under the exact same circumstances**  
Wind tunnels enable test teams to rerun specific tests over and over again, using the same conditions every time. This allows them to exactly determine the effect of any change on the aerodynamics of their car or plane under test. In software testing, this is exactly what you want when you need to reproduce or analyze a defect or any other suspect behaviour. Unfortunately, when dependencies are outside your circle of control, this might not be easy, if possible at all. When you&#8217;re using virtual assets instead of external dependencies, it&#8217;s far easier to recreate the exact conditions that applied when the defect occurred. 

**It can be used to test for highly improbable situations**  
150mph wind gusts, wind coming from three directions at the same time, &#8230; Situations that might be really hard &#8211; if not impossible &#8211; to reproduce when road testing, but made possible by using a wind tunnel. It&#8217;s those corner cases where interesting behaviour of your test object might surface, so they are really worth looking at. With service virtualization, it&#8217;s possible, for example, to prepare highly improbable responses for a virtualized third party dependency and see how your application handles these. This is a great way to improve the resilience of and trust in the application you&#8217;re developing and testing.

**There&#8217;s always a need for real life road testing**  
As with testing cars and planes, you can go a long way using simulated test environments, but there&#8217;s no place like the road to _really_ see how your software holds up. So never trust on virtualization alone when testing any application that uses dependencies, because there&#8217;s always a situation or two you didn&#8217;t think of when virtualizing.. Instead, use your software wind tunnel wisely and your testing process will see the benefits.