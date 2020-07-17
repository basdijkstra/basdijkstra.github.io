---
id: 1548
title: 'Service virtualization: open source or commercial tooling?'
date: 2016-07-30T11:10:03+02:00
author: Bas
layout: revision
guid: http://www.ontestautomation.com/1546-autosave-v1/
permalink: /1546-autosave-v1/
---
I&#8217;ve been talking regularly about the benefits of introducing <a href="http://www.ontestautomation.com/tag/service-virtualization-2/" target="_blank">service virtualization</a> to your software development and testing process. However, once you&#8217;ve decided that service virtualization is the way forward, you&#8217;re not quite there yet. One of the decisions that still needs to be made is the selection of a tool or tool suite.

_A note: Following the &#8216;why?&#8217;-&#8216;what?&#8217;-&#8216;how?&#8217; strategy of implementing new tools, tool selection (which is part of the &#8216;how?&#8217;) should only be done when it is clear why implementing service virtualization might be beneficial and what services are to be virtualized. A tool is a means of getting something done, nothing more.._

As with many different types of tools, one of the choices to be made in the tool selection process is the question of purchasing a commercial tool or taking the open source route. In this post, I&#8217;d like to highlight some of the benefits and drawbacks of either option.

**Commercial off the shelf (COTS) solutions**  
Many major tool vendors, including <a href="https://www.parasoft.com/product/parasoft-service-virtualization/" target="_blank">Parasoft</a>, <a href="http://www8.hp.com/nl/nl/software-solutions/service-virtualization/" target="_blank">HP</a> and <a href="http://www.ca.com/us/products/ca-service-virtualization.html" target="_blank">CA</a>, offer a service virtualization platform as part of their product portfolio. These tools are sophisticated solutions that are generally rich in features and possibilities. If you&#8217;d ask me, the most important benefits of going the commercial route would be:

  * **Their support for a multitude of communication protocols and message standards**. When you&#8217;re dealing with a lot of different types of messages, or with proprietary or uncommon message standards or communication protocols, commercial solutions offer a much higher chance of supporting everything you need to virtualize these out-of-the-box. This potentially saves a lot of time developing and maintaining a custom solution.
  * **Support for different types of testing**. Most commercial tools, next to their ability to simulate request/response behaviour, offer options to simulate behavior that is necessary to perform other types of tests. These options include random message dropping (to test resilience and error handling of your appliation under test) and setting performance characteristics (when virtual assets are to be included in performance testing).
  * **Seamless integration with test tools**. Commercial vendors often offer API and other functional testing tools that can be combined with their corresponding service virtualization solution to create effective and flexible testing solutions.
  * **Extensive product support**. When you have questions with regards to using these tools, support is often just a phone call away. Conditions are often formalized in the form of support and maintenance contracts.

As with everything, there are some drawbacks too when selecting a COTS service virtualization solution:

  * **Cost**. These solutions tend to be very expensive, both in license fees as well as consultancy fees required for successful implementation. This is the single biggest drawback for organizations to invest in service virtualization, as heard from friends that sell and implement these tools for a living.
  * **Possible vendor lock-in**. A lot of organizations prefer not to work with big tool vendors because it&#8217;s hard to migrate away if over time you decided to discontinue their tools. I have no first hand experience with this drawback, however.

**Open source solutions**  
As an alternative to the aforementioned commercially licensed service virtualization tools, more and more open source solutions are entering the field. Some good examples of these would be <a href="http://wiremock.org/" target="_blank">WireMock</a> and <a href="http://hoverfly.io/" target="_blank">Hoverfly</a>. These open source solutions have a number of advantages over paid-for alternatives:

  * **Get started quickly**. You can simply download and install them and get started right away. Documentation and examples are available online to get you up and running.
  * **POssibly cheap**. There are no license fees involved, which for many organizations is a reason to choose open source instead of COTS solutions. Beware of the <a href="http://www.joetheitguy.com/2013/10/23/hidden-costs-of-open-source-software/" target="_blank">hidden costs of open source software</a>, though! License fees aren&#8217;t the only factor to be considered.

Open source service virtualization solutions, too, have their drawbacks:

  * The range of features they offer are generally more limited compared to their commercial counterparts. For example, both WireMock and Hoverfly can virtualize HTTP-based messaging systems, but do not offer support for other types of messaging, such as JMS or MQ.
  * As a result if the above, these tools are geared towards a specific situation where they might be very useful, but they might not suffice when trying to do enterprise-wide implementation. For example, a HTTP-based service virtualization tool might have been implemented successfully for HTTP-based services, but when the team wants to extend service virtualization implementation to JMS or MQ services, that&#8217;s often not possible without either developing an adapter or adding another tool to the tool set.
  * When you have a question regarding an open source tool, support is usually provided on a best effort basis, either by the creator(s) of the tool or by the user community. Especially for tools that are not yet commonplace, support may be hard to get by.

**So, what to choose?**  
When given the choice, I&#8217;d follow these steps for making a decision between open source and commercial service virtualization and, more importantly, making it a success:

  1. Identify development and testing bottlenecks and decide whether service virtualization is a possible solution
  2. Research open source solution to see whether there is a tool available that enables you to virtualize the dependencies that form the bottleneck
  3. Compare the tools found to commercial tools that are also able to do this. **Make sure to base this comparison on the Total Cost of Ownership (TCO), not just on initial cost!**
  4. Select the tool that is the most promising for what you&#8217;re trying to achieve, do (in case of open source) or maybe request (in case of COTS) a proof of concept and evaluate whether the selected tool is fit for the job.
  5. Continue implementation and periodically evaluate your tool set to see whether the fit is still just as good (or better!).

If you have a story to tell concerning service virtualization tool selection and implementation, please do share it here in the comments!