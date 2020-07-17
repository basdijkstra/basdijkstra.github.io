---
id: 1800
title: Service virtualization with Parasoft Virtualize Community Edition
date: 2017-02-19T18:12:18+01:00
author: Bas
layout: revision
guid: http://www.ontestautomation.com/1787-autosave-v1/
permalink: /1787-autosave-v1/
---
A couple of years ago, I took my first steps in the world of service virtualization when I took on a project where I developed stubs (virtual assets) to speed up the testing efforts and to enable automated testing for a telecommunications service provider. The tool that was used there was Parasoft Virtualize. I had been working with the Parasoft tool set before that project, most notably with SOAtest, their functional testing solution, but never with Virtualize.

In that project, we were able to massively decrease the time needed to create test data and run tests against the system. The time required to set up a test case went down from weeks to minutes, literally, enabling the test team to test more and more often and to introduce end-to-end test automation as part of their testing efforts. That&#8217;s where my love for the service virtualization (SV) field began, pretty much.

After that, I did a couple more projects using Virtualize, and started building more experience with SV, exploring other solutions including open source ones like <a href="http://hoverfly.io/" target="_blank">SpectoLabs Hoverfly</a> or <a href="http://wiremock.org/" target="_blank">WireMock</a>. What I quickly learned, also from my conversations with the people at Parasoft, was that a lot of organizations see the benefits (and even the need) for service virtualization. However, these organizations often get cold feet when they see the investment required to obtain a license for a commercial SV solution, which easily runs into the tens of thousands of dollars. Instead, they:

  * turn to open source solutions, a perfect choice when these offer all the features you need, or
  * start to build something themselves, which turns out to be a success much less often.

The people at Parasoft have clearly taken notice, because last week saw the official announcement of the free Community Edition of the Virtualize platform. Virtualize CE is their answer to the rise in popularity and possibilities that free or open source solutions offer. In this post, we&#8217;ll take a first look at what Virtualize CE has to offer. And that is quite a lot.

**Features**  
Here are a couple of the most important features that Virtualize CE offers:

  * Support for HTTP and HTTPS
  * Support for literal, XML and JSON requests and responses and SOAP-based and RESTful API simulation
  * Ability to create data driven responders (more on that later)
  * Recording traffic by acting as a proxy and replaying the recorded traffic for quick and easy virtual asset creation

Of course, there are also limitations when compared to the paid version of Virtualize, some of which are:

  * Traffic is limited to 11.000 hits per day
  * No support for protocols such as JMS and MQ, nor for other traffic types such as SQL queries or EDI messages
  * No ability to run Virtualize through the command line interface or configure virtual asset behaviour through the Virtualize API
  * Support is limited to the online resources

Still, Virtualize CE has a lot to offer. Let&#8217;s take a look.

**Downloading and installation**  
You can download Virtualize CE from the <a href="http://software.parasoft.com/virtualize/" target="_blank">Virtualize product page</a>, all it takes is supplying an active email address where the download link will be sent to. It&#8217;s quite a big download at 1.1 GB, but this has a reason: it includes the full version of both Virtualize and SOAtest (the Parasoft functional testing solution). This means that when you decide to upgrade to the full version, all you need is another license. No reinstalling of software required. After downloading and installing the software, you can simply unlock the CE license through the Preferences menu by entering the email address you supplied to get a download link. That&#8217;s it.

**Creating a first virtual asset**  
As an example, I&#8217;m going to create a simulation of a simple API that returns data related to music. Virtualize either lets you create a virtual asset from scratch or generates a skeleton for it based on an API definition in, for example, WSDL, RAML or Swagger format. For this example, I&#8217;ve taken this <a href="https://raw.githubusercontent.com/raml-org/raml-tutorial-200/step8/jukebox-api.raml" target="_blank">RAML definition</a> of a simple music management API. I won&#8217;t talk you through all the clicks and keystrokes it takes to create such an asset, but trust me, it is very straightforward and takes less than a minute. After the virtual asset skeleton has been created, you see the virtual asset definition with responders for all supported operations:

<a href="http://www.ontestautomation.com/?attachment_id=1789" rel="attachment wp-att-1789"><img src="http://www.ontestautomation.com/wp-content/uploads/2017/02/ota_virtualize_virtual_asset.png" alt="Our virtual asset with all of its responders" width="444" height="345" class="aligncenter size-full wp-image-1789" srcset="https://www.ontestautomation.com/wp-content/uploads/2017/02/ota_virtualize_virtual_asset.png 444w, https://www.ontestautomation.com/wp-content/uploads/2017/02/ota_virtualize_virtual_asset-300x233.png 300w" sizes="(max-width: 444px) 100vw, 444px" /></a>

Even better, it is automatically deployed onto the Tomcat server running within Virtualize, meaning that our virtual asset can be accessed right away (after the Tomcat server is started, obviously). By default, the server is running on localhost at port 9080, which means that we can for example do a GET call to _http://localhost:9080/Jukebox/songs_ (which returns a list of all songs known to the API) and see the following response:

<a href="http://www.ontestautomation.com/?attachment_id=1791" rel="attachment wp-att-1791"><img src="http://www.ontestautomation.com/wp-content/uploads/2017/02/ota_virtualize_first_response_live.png" alt="Our first response from a Virtualize responder" width="400" height="234" class="aligncenter size-full wp-image-1791" srcset="https://www.ontestautomation.com/wp-content/uploads/2017/02/ota_virtualize_first_response_live.png 400w, https://www.ontestautomation.com/wp-content/uploads/2017/02/ota_virtualize_first_response_live-300x176.png 300w" sizes="(max-width: 400px) 100vw, 400px" /></a>

This response is defined in the responder responsible for answering to the GET call to that endpoint, in the form of a predefined, canned response:

<a href="http://www.ontestautomation.com/?attachment_id=1792" rel="attachment wp-att-1792"><img src="http://www.ontestautomation.com/wp-content/uploads/2017/02/ota_virtualize_first_response_definition.png" alt="The definition of the above response" width="892" height="519" class="aligncenter size-full wp-image-1792" srcset="https://www.ontestautomation.com/wp-content/uploads/2017/02/ota_virtualize_first_response_definition.png 892w, https://www.ontestautomation.com/wp-content/uploads/2017/02/ota_virtualize_first_response_definition-300x175.png 300w, https://www.ontestautomation.com/wp-content/uploads/2017/02/ota_virtualize_first_response_definition-768x447.png 768w" sizes="(max-width: 892px) 100vw, 892px" /></a>

To alter the response, you can simply change the text, save your changes and the updated virtual asset will automatically be deployed to the Tomcat server.

**Making the virtual asset data driven**  
That&#8217;s all well and good, but it gets better: we can make our virtual asset a lot more powerful by creating the responses it returns in a data driven manner. Basically, this means that response messages can be populated with contents from a data source, such as a CSV or an Excel file or even a database table (accessed via JDBC). As an example, I&#8217;ve created an internal Table data source containing songs from one of my favourite albums of the last couple of years:

<a href="http://www.ontestautomation.com/?attachment_id=1793" rel="attachment wp-att-1793"><img src="http://www.ontestautomation.com/wp-content/uploads/2017/02/ota_virtualize_data_source.png" alt="Data source definition" width="676" height="499" class="aligncenter size-full wp-image-1793" srcset="https://www.ontestautomation.com/wp-content/uploads/2017/02/ota_virtualize_data_source.png 676w, https://www.ontestautomation.com/wp-content/uploads/2017/02/ota_virtualize_data_source-300x221.png 300w" sizes="(max-width: 676px) 100vw, 676px" /></a>

Based on the song ID, I&#8217;d like the virtual asset to look up the corresponding record in the data source and then populate the response with the other values in that same record. This is a mechanism referred to as data source correlation in Virtualize. It is defined on the responder level, so let&#8217;s apply it to the GET responder for the _http://localhost:9080/Jukebox/songs/{songId}_ endpoint:

<a href="http://www.ontestautomation.com/?attachment_id=1795" rel="attachment wp-att-1795"><img src="http://www.ontestautomation.com/wp-content/uploads/2017/02/ota_virtualize_data_source_correlation.png" alt="Configuring data source correlation" width="893" height="232" class="aligncenter size-full wp-image-1795" srcset="https://www.ontestautomation.com/wp-content/uploads/2017/02/ota_virtualize_data_source_correlation.png 893w, https://www.ontestautomation.com/wp-content/uploads/2017/02/ota_virtualize_data_source_correlation-300x78.png 300w, https://www.ontestautomation.com/wp-content/uploads/2017/02/ota_virtualize_data_source_correlation-768x200.png 768w" sizes="(max-width: 893px) 100vw, 893px" /></a>

What we define here is that whenever a GET request comes in that matches the _http://localhost:9080/Jukebox/songs/{songId}_ endpoint, Virtualize should look up the value of the path parameter with index 2, which is _{songId}_, in the _songId_ column of the _Songs_ data source. If there&#8217;s a match, the response can be populated with other values in the same data source row. The mapping between response message fields and data source columns can be easily defined in the response configuration window:

<a href="http://www.ontestautomation.com/?attachment_id=1796" rel="attachment wp-att-1796"><img src="http://www.ontestautomation.com/wp-content/uploads/2017/02/ota_virtualize_data_driven_response.png" alt="Populating the responder message with data source values" width="887" height="545" class="aligncenter size-full wp-image-1796" srcset="https://www.ontestautomation.com/wp-content/uploads/2017/02/ota_virtualize_data_driven_response.png 887w, https://www.ontestautomation.com/wp-content/uploads/2017/02/ota_virtualize_data_driven_response-300x184.png 300w, https://www.ontestautomation.com/wp-content/uploads/2017/02/ota_virtualize_data_driven_response-768x472.png 768w" sizes="(max-width: 887px) 100vw, 887px" /></a>

But does it work? Let&#8217;s find out by calling _http://localhost:9080/Jukebox/songs/d155b058-f51f-11e6-bc64-92361f002676_ and see if the data corresponding to the track _Strong_ (not coincidentally my favourite track of the album) is returned.

<a href="http://www.ontestautomation.com/?attachment_id=1797" rel="attachment wp-att-1797"><img src="http://www.ontestautomation.com/wp-content/uploads/2017/02/ota_virtualize_data_driven_response_live.png" alt="Response returned by the data driven responder" width="749" height="306" class="aligncenter size-full wp-image-1797" srcset="https://www.ontestautomation.com/wp-content/uploads/2017/02/ota_virtualize_data_driven_response_live.png 749w, https://www.ontestautomation.com/wp-content/uploads/2017/02/ota_virtualize_data_driven_response_live-300x123.png 300w" sizes="(max-width: 749px) 100vw, 749px" /></a>

And it is! This means our data driven responder works as intended. In this way, you can easily create flexible and powerful virtual assets.

One last feature I&#8217;d like to highlight is the ability to track requests and responses. With a single click of the mouse, you can turn on Event Monitoring for individual virtual assets and see what messages are received and sent by it in the Event Details perspective, for example for logging or virtual asset debugging purposes (remember that your virtual assets need to be tested too!):

<a href="http://www.ontestautomation.com/?attachment_id=1798" rel="attachment wp-att-1798"><img src="http://www.ontestautomation.com/wp-content/uploads/2017/02/ota_virtualize_event_viewer.png" alt="The Virtualize Event Viewer shows requests and responses" width="1407" height="496" class="aligncenter size-full wp-image-1798" srcset="https://www.ontestautomation.com/wp-content/uploads/2017/02/ota_virtualize_event_viewer.png 1407w, https://www.ontestautomation.com/wp-content/uploads/2017/02/ota_virtualize_event_viewer-300x106.png 300w, https://www.ontestautomation.com/wp-content/uploads/2017/02/ota_virtualize_event_viewer-768x271.png 768w, https://www.ontestautomation.com/wp-content/uploads/2017/02/ota_virtualize_event_viewer-1024x361.png 1024w" sizes="(max-width: 1407px) 100vw, 1407px" /></a>

Apart from the features presented here, there&#8217;s much, much more you can do with Parasoft Virtualize CE. If you find yourself looking for a service virtualization solution that&#8217;s easy to set up and use, this is one you should definitely check out. I&#8217;d love to hear your thoughts and experiences! And as always, I&#8217;ll happily answer any questions you might have.