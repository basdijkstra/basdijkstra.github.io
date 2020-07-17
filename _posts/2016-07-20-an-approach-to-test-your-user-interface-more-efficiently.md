---
id: 1531
title: An approach to test your user interface more efficiently
date: 2016-07-20T08:00:03+02:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=1531
permalink: /an-approach-to-test-your-user-interface-more-efficiently/
categories:
  - General test automation
tags:
  - model-view-viewmodel
  - unit testing
  - user interface
---
As returning readers of this blog might have read before, I am pretty skeptical about writing automated tests that interact with the application under test at the user interface level. These UI tests tend to be:

  * slow in execution, since they are typically end-to-end tests from an application-layer perspective, and
  * demanding when it comes to maintenance, because the user interface is typically a part of an application subject to frequent changes.

However, the user interface often is an important component of an application and therefore it requires testing effort. Since this blog is all about test automation, I&#8217;d like to talk about a different approach to user interface test automation in this blog post.

But first, let me explain a subtle yet important difference. On the one hand, there&#8217;s **testing the user interface**. Here, the actual logic incorporated in the user interface is isolated and tested as a unit. Everything that&#8217;s happening &#8216;behind&#8217; the user interface is out of scope of the test and therefore usually mocked. On the other hand, there&#8217;s **testing application logic through the user interface**. This is generally done using tools such as Selenium. This type of automated tests uses the user interface as its point of entry and validation, even though the actual logic that processes the input and generates the output to be checked is not implemented at the user interface layer at all.

<a href="http://www.ontestautomation.com/?attachment_id=1532" rel="attachment wp-att-1532"><img src="http://www.ontestautomation.com/wp-content/uploads/2016/07/ui_testing.png" alt="Testing the user interface versus testing through the user interface" width="1614" height="502" class="aligncenter size-full wp-image-1532" srcset="https://www.ontestautomation.com/wp-content/uploads/2016/07/ui_testing.png 1614w, https://www.ontestautomation.com/wp-content/uploads/2016/07/ui_testing-300x93.png 300w, https://www.ontestautomation.com/wp-content/uploads/2016/07/ui_testing-768x239.png 768w, https://www.ontestautomation.com/wp-content/uploads/2016/07/ui_testing-1024x318.png 1024w" sizes="(max-width: 1614px) 100vw, 1614px" /></a>

Now, only when you specifically want to perform end-to-end application tests, or when there really isn&#8217;t any other option than to use the user interface drive tests that validate underlying application logic should you resort to tools such as Selenium. In all other cases, it might be a better idea to see if there&#8217;s a better option available.

**User interface architectures and testability**  
In the remainder of this post I want to zoom in on a commonly used user interface pattern, namely Model-View-ViewModel, or MVVM in short, and what I think is a suitable approach for writing automated tests for user interfaces adhering to this pattern.

**MVVM explained (briefly)**  
The MVVM pattern lets you separate presentation logic (that defines what information is shown on the screen) from the actual presentation (that defines how the information is displayed). Schematically, the pattern looks like this:

<a href="http://www.ontestautomation.com/?attachment_id=1533" rel="attachment wp-att-1533"><img src="http://www.ontestautomation.com/wp-content/uploads/2016/07/model-view-viewmodel.png" alt="A schematic representation of the Model-View-ViewModel pattern" width="1274" height="505" class="aligncenter size-full wp-image-1533" srcset="https://www.ontestautomation.com/wp-content/uploads/2016/07/model-view-viewmodel.png 1274w, https://www.ontestautomation.com/wp-content/uploads/2016/07/model-view-viewmodel-300x119.png 300w, https://www.ontestautomation.com/wp-content/uploads/2016/07/model-view-viewmodel-768x304.png 768w, https://www.ontestautomation.com/wp-content/uploads/2016/07/model-view-viewmodel-1024x406.png 1024w" sizes="(max-width: 1274px) 100vw, 1274px" /></a>

The _View_ is the user interface, which is made up of textboxes, labels, buttons, etc. It is responsible for defining the structure, layout and appearance of what you seen on the screen. The </em>Model</em> is an implementation of the domain model. It consists of a data model along with business rules and validation logic. The _View Model_ is the intermediary between the View and the Model, handling user interface actions (such as the click of a button) and interacting with models usually by invoking methods in the model classes (for example to update a record in a database). It is also responsible for presenting data from the model (for example the results of a query) to the view in a representation that can be easily displayed on screen.

One of the key ideas behind MVVM and other <a href="http://www.martinfowler.com/eaaDev/uiArchs.html" target="_blank">user interface architectures</a>, such as Model-View-Controller (MVC) and Model-View-Presenter (MVP), is that the separation of concerns ingrained in these architectures makes them testable using unit tests, instead of having to rely on expensive and slow end-to-end tests.

**Testing user interfaces built on MVVM**  
Since all business logic is contained in the view model, it makes sense to make it the center of attention for our testing efforts. Since view models are implemented as classes in object orientedn languages these tests are usually defined as unit tests. This immediately shows the value of asking yourself &#8216;am I testing the user interface or merely testing my application _through_ the user interface?&#8217;. In case of the former, writing complicated, slow and brittle end-to-end tests with tools such as Selenium is pure overkill.

Instead, we write a simple unit test that covers the business logic in the view model. We mock the model part, since business and implementation logic further down the application stack can be tested using (again) unit tests, or when the view model for example invokes web services for storing, processing and retrieving data, we can write API-level tests to cover that part of our application. The view part can &#8211; when MVVM is applied correctly &#8211; be left out of scope, since buttons, labels and text boxes are usually standardized objects that &#8216;only&#8217; need to be positioned correctly on screen (in reality, designing good user interfaces is a respectable and skillful job of it&#8217;s own, no disrespect intended). If you really want to write checks to verify that the view part is implemented correctly, there&#8217;s always tools such as <a href="http://galenframework.com/" target="_blank">Galen Framework</a> that allow you to check your user interface at the design level (i.e., visual checks).

**Links to example implementations and tests**  
Rather than writing my own example here, I&#8217;d like to link to some well-written and understandable examples of the application of MVVM and the way you can write unit tests for your MVVM-based user interface here. I&#8217;m currently in the early stages of implementing a suitable testing approach for the user interface of the key application at my current project, so you can expect some real-life examples from my own hand in a future post. For now, I encourage you to take a look at the following blog posts and examples:

  * <a href="http://www.syntaxsuccess.com/viewarticle/unit-testable-code-with-mvvm" target="_blank">Unit testable code with MVVM</a> by Torgeir Helgevold
  * <a href="https://msdn.microsoft.com/en-us/magazine/dn463790.aspx" target="_blank">Writing a testable presentation layer with MVVM</a> at the Microsoft Developer Network
  * And for a Java perspective: <a href="https://dukescript.com/best/practices/2015/02/16/tdd-with-dukescript.html" target="_blank">Test driven development with MVVM</a> at the DukeScript web site