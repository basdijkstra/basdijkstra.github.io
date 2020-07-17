---
id: 1525
title: Adding service virtualization to your Continuous Delivery pipeline
date: 2016-07-02T18:07:48+02:00
author: Bas
layout: revision
guid: http://www.ontestautomation.com/1518-revision-v1/
permalink: /1518-revision-v1/
---
Lately I&#8217;ve been working on a couple of workshops and presentations on service virtualization and the trends in that field. One of these trends &#8211; integrating virtualized test environments, containerization and Continuous Delivery &#8211; is starting to see some real traction within the service virtualization realm. Therefore I think it might be interesting to take a closer look at it in this post.

**What is service virtualization again?**  
Summarizing: service virtualization is a method to simulate the behaviour of dependencies that are required to perform tests on your application under test. You can use service virtualization to create intelligent and realistic simulations of dependencies that are not available, don&#8217;t contain the required test data or are otherwise access-restricted. I&#8217;ve written a couple of blog posts on service virtualization in the past, so if you want to read more, check those out <a href="http://www.ontestautomation.com/tag/service-virtualization-2/" target="_blank">here</a>.

<a href="http://www.ontestautomation.com/?attachment_id=1522" rel="attachment wp-att-1522"><img src="http://www.ontestautomation.com/wp-content/uploads/2016/07/service_virtualization.png" alt="Service virtualization" width="1579" height="696" class="aligncenter size-full wp-image-1522" srcset="https://www.ontestautomation.com/wp-content/uploads/2016/07/service_virtualization.png 1579w, https://www.ontestautomation.com/wp-content/uploads/2016/07/service_virtualization-300x132.png 300w, https://www.ontestautomation.com/wp-content/uploads/2016/07/service_virtualization-768x339.png 768w, https://www.ontestautomation.com/wp-content/uploads/2016/07/service_virtualization-1024x451.png 1024w" sizes="(max-width: 1579px) 100vw, 1579px" /></a>

**Continuous Delivery and test environment challenges**  
The challenges presented by restricted access to dependencies during development and testing grow stronger when organizations are moving towards Continuous Delivery. A lack of suitable test environments for your dependencies can be a serious roadblock when you want to build, test and ultimately deploy software on demand and without human intervention. You can imagine that having virtual assets that emulate the behaviour of access-restricted components at your disposition &#8211; and more importantly: under your own control &#8211; is a significant improvement, especially when it comes to test execution and moving up the pipeline.

**Using virtual test environments as artefacts in your pipeline**  
While having virtual test environments run at some server in your network is already a big step, &#8216;the next level&#8217; when it comes to service virtualization as an enabler for Continuous Delivery is treating your virtual assets as artefacts that can be created, used and ultimately destroyed at will. I&#8217;ll talk about the specific benefits later, but first let&#8217;s see how we can achieve such a thing.

One way of treating your virtual test environments as CD artefacts is by containerizing them, for example using Docker. With Docker, you can for example create an image that contains your service virtualization engine, any virtual assets you would like to use and &#8211; if applicable &#8211; data sources that contain the necessary test data to emulate the required behaviour.

Another way of creating virtual test environments on demand is by hosting them using an on-demand cloud-based service such as Microsoft Azure. This allows you to spin up a simulated test environment when required, use and abuse it and destroy it after you are (or your deployment process) is done.

At the time of writing this blog post, I&#8217;m preparing a workshop where we demonstrate &#8211; and have the participants work with &#8211; both of these approaches, and while the technology is still pretty new, I think this is an interesting and exciting way of taking (or regaining) full control of your test environments and ultimately test faster, more and better.

<a href="http://www.ontestautomation.com/?attachment_id=1521" rel="attachment wp-att-1521"><img src="http://www.ontestautomation.com/wp-content/uploads/2016/07/containerized_service_virtualization.png" alt="Adding containerized service virtualization to your continuous delivery pipeline" width="1007" height="770" class="aligncenter size-full wp-image-1521" srcset="https://www.ontestautomation.com/wp-content/uploads/2016/07/containerized_service_virtualization.png 1007w, https://www.ontestautomation.com/wp-content/uploads/2016/07/containerized_service_virtualization-300x229.png 300w, https://www.ontestautomation.com/wp-content/uploads/2016/07/containerized_service_virtualization-768x587.png 768w" sizes="(max-width: 1007px) 100vw, 1007px" /></a>

**Benefits of containerized test environments**  
Using containerized test environments in your Continuous Delivery process has a couple of interesting advantages for your development and testing activities:

  * As said, test environments can be spun up on demand, so no more waiting until that dependency you need is available and configured like you need it to be.
  * You don&#8217;t need to worry about resetting the test environment to its original state after testing has completed. Just throw the test environment away and spin up a new instance ready for your next test run.
  * Having the same (virtual) test environment at your disposal every time makes it easier to reproduce and analyze bugs. No more &#8216;works on my machine&#8217;!

**Further reading**  
Want to read more about containerized service virtualization? See for an example specific to Parasoft Virtualize <a href="https://www.parasoft.com/resource/6945/" target="_blank">this white paper</a>. And keep an eye out for other service virtualization tool vendors, such as HP and CA. They&#8217;re likely working on something similar as well, if they haven&#8217;t released it already.

Also, if you&#8217;re more of a hands-on person, you can apply containerization to open source service virtualization solutions such as <a href="http://hoverfly.io" target="_blank">HoverFly</a> yourself.