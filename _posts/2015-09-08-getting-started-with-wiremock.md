---
id: 1037
title: 'Getting started with: WireMock'
date: 2015-09-08T06:42:45+02:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=1037
permalink: /getting-started-with-wiremock/
categories:
  - Service virtualization
  - Test automation tools
  - Web service testing
tags:
  - java
  - rest-assured
  - up and running
  - wiremock
---
_This is the seventh article in our series on new, popular or otherwise interesting tools used in test automation. You can read all posts within this series by clicking [here](http://www.ontestautomation.com/tag/up-and-running/)._

**What is WireMock?**  
From the <a href="http://www.wiremock.org" target="_blank">WireMock.org</a> website: WireMock is a flexible library for stubbing and mocking web services. Unlike general purpose mocking tools it works by creating an actual HTTP server that your code under test can connect to as it would a real web service. It supports HTTP response stubbing, request verification, proxy/intercept, record/playback of stubs and fault injection, and can be used from within a unit test or deployed into a test environment. Although it’s written in Java, there’s also a JSON API so you can use it with pretty much any language out there.

In short, WireMock is a very handy tool for all those situations where you need to set up a mock version of a web service, for example for testing or development purposes.

**Where can I get WireMock?**  
WireMock can be downloaded from <a href="http://repo1.maven.org/maven2/com/github/tomakehurst/wiremock/" target="_blank">the WireMock Maven repository</a>.

**How do I install and configure WireMock?**  
Installing WireMock is as simple as downloading the latest standalone version from <a href="http://repo1.maven.org/maven2/com/github/tomakehurst/wiremock/" target="_blank">the WireMock Maven repository</a> and adding it as a dependency to your Java project.

**Creating a first WireMock mock service**  
Let&#8217;s start by creating a very simple mock service that returns a plain text string and a HTTP status code.

<pre class="brush: java; gutter: false">public void setupStub() {
		
	stubFor(get(urlEqualTo("/an/endpoint"))
            .willReturn(aResponse()
                .withHeader("Content-Type", "text/plain")
                .withStatus(200)
                .withBody("You&#039;ve reached a valid WireMock endpoint")));
}</pre>

This code snippet creates a simple mock service that runs at the _/an/endpoint_ endpoint on the WireMock server (your localhost most of the time, but more about that in a bit). It returns a response with content type _text/plain_, HTTP status 200 and body text &#8220;_You&#8217;ve reached a valid WireMock endpoint_&#8220;.

**Running and testing your mock service**  
Of course, we would also like to validate whether the mock service we created does what we told it to do. In this post, I will use <a href="http://www.ontestautomation.com/up-and-running-with-junit/" target="_blank">JUnit</a>-based <a href="http://www.ontestautomation.com/testing-rest-services-with-rest-assured/" target="_blank">REST Assured</a> tests to validate the behaviour of our mocks. But first, we need to get our mock service up and running before we start testing. This can be done very easily with WireMock using a JUnit _@Rule_:

<pre class="brush: java; gutter: false">@Rule
public WireMockRule wireMockRule = new WireMockRule(8090);</pre>

This rule starts and stops the mocks defined in your JUnit test class for every test in it. Next, we can define a couple of REST Assured tests to see whether the mock service is doing what we expect:

<pre class="brush: java; gutter: false">@Test
public void testStatusCodePositive() {
		
	setupStub();
		
	given().
	when().
		get("http://localhost:8090/an/endpoint").
	then().
		assertThat().statusCode(200);
}
	
@Test
public void testStatusCodeNegative() {
	
	setupStub();
		
	given().
	when().
		get("http://localhost:8090/another/endpoint").
	then().
		assertThat().statusCode(404);
}
	
@Test
public void testResponseContents() {
		
	setupStub();
	
	String response = get("http://localhost:8090/an/endpoint").asString();
	Assert.assertEquals("You&#039;ve reached a valid WireMock endpoint", response);
}</pre>

When we run these tests, they will all pass, indicating our mock service behaves as expected:  
[<img src="http://www.ontestautomation.com/wp-content/uploads/2015/09/wiremock_junit_results.png" alt="WireMock JUnit test results" width="486" height="193" class="aligncenter size-full wp-image-1039" srcset="https://www.ontestautomation.com/wp-content/uploads/2015/09/wiremock_junit_results.png 486w, https://www.ontestautomation.com/wp-content/uploads/2015/09/wiremock_junit_results-300x119.png 300w" sizes="(max-width: 486px) 100vw, 486px" />](http://www.ontestautomation.com/wp-content/uploads/2015/09/wiremock_junit_results.png)

**Request-response matching**  
WireMock also allows you to define a stub that returns a particular answer based on (part of) the request message using the _matching_ matcher method:

<pre class="brush: java; gutter: false">public void setupStub() {
		
	stubFor(post(urlEqualTo("/pingpong"))
			.withRequestBody(matching("&lt;input&gt;PING&lt;/input&gt;"))
            .willReturn(aResponse()
                .withStatus(200)
                .withHeader("Content-Type", "application/xml")
                .withBody("&lt;output&gt;PONG&lt;/output&gt;")));
}</pre>

This service only returns a status code 200 and a response XML message when the request body equals a given value. Again, we can validate this easily using REST Assured:

<pre class="brush: java; gutter: false">@Test
public void testPingPongPositive() {
		
	setupStub();
		
	given().
		body("&lt;input&gt;PING&lt;/input&gt;").
	when().
		post("http://localhost:8090/pingpong").
	then().
		assertThat().
		statusCode(200).
		and().
		assertThat().body("output", org.hamcrest.Matchers.equalTo("PONG"));
}</pre>

WireMock also allows you to use regular expressions for more flexible request matching.

**Creating stateful mocks**  
As a final example, I&#8217;ll show how to create a stateful mock. This is done in WireMock using scenarios:

<pre class="brush: java; gutter: false">public void setupStub() {
		
	stubFor(get(urlEqualTo("/todolist"))
			.inScenario("addItem")
			.whenScenarioStateIs(Scenario.STARTED)
			.willReturn(aResponse()
                .withStatus(200)
                .withHeader("Content-Type", "application/xml")
                .withBody("&lt;list&gt;Empty&lt;/list&gt;")));
		
	stubFor(post(urlEqualTo("/todolist"))
			.inScenario("addItem")
			.whenScenarioStateIs(Scenario.STARTED)
			.willSetStateTo("itemAdded")
			.willReturn(aResponse()
				.withHeader("Content-Type", "application/xml")
                .withStatus(201)));
		
	stubFor(get(urlEqualTo("/todolist"))
			.inScenario("addItem")
			.whenScenarioStateIs("itemAdded")
			.willReturn(aResponse()
                .withStatus(200)
                .withHeader("Content-Type", "application/xml")
                .withBody("&lt;list&gt;&lt;item&gt;Item added to list&lt;/item&gt;&lt;/list&gt;")));	
}</pre>

So, when we first perform a GET on this mock, an empty to-do list is returned. Then, when we do a POST, the mock service state is changed from _Scenario.STARTED_ (a default in WireMock) to &#8220;_itemAdded_&#8220;. Then, when we do another GET, we get a to-do list with a single item on it:

<pre class="brush: java; gutter: false">@Test
public void testStatefulMock() {
		
	setupStub();
		
	given().
	when().
		get("http://localhost:8090/todolist").
	then().
		assertThat().
		statusCode(200).
		and().
		assertThat().body("list", org.hamcrest.Matchers.equalTo("Empty"));
		
	given().
	when().
		post("http://localhost:8090/todolist").
	then().
		assertThat().
		statusCode(201);
		
	given().
	when().
		get("http://localhost:8090/todolist").
	then().
		assertThat().
		statusCode(200).
		and().
		assertThat().body("list", org.hamcrest.Matchers.not("Empty")).
		and().
		assertThat().body("list.item", org.hamcrest.Matchers.equalTo("Item added to list"));
}</pre>

A trivial example, perhaps, but nevertheless it shows you how can create stateful mocks easily using WireMock.

**Further reading**  
WireMock provides many more useful features for creating just the mock you want. A complete reference guide can be found on <a href="http://wiremock.org/index.html" target="_blank">the WireMock website</a>.

An Eclipse project including the mock services and REST Assured tests I&#8217;ve used in this post can be downloaded [here](http://www.ontestautomation.com/files/WireMock.zip).