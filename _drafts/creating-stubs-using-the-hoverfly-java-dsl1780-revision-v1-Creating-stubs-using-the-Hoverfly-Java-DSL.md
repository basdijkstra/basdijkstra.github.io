---
id: 1785
title: Creating stubs using the Hoverfly Java DSL
date: 2017-02-13T21:20:39+01:00
author: Bas
layout: revision
guid: http://www.ontestautomation.com/1780-revision-v1/
permalink: /1780-revision-v1/
---
One of the things I like best about the option to write stubs for <a href="http://www.ontestautomation.com/tag/service-virtualization-2/" target="_blank">service virtualization</a> in code is that by doing so, you&#8217;re able to store them in and access them through the same version control system (Git, for example) as your production code and your automated tests. I was excited when I read <a href="https://specto.io/blog/2017/1/4/stubbing-http-apis-and-microservices-with-the-hoverfly-java-dsl" target="_blank">a blog post on the SpectoLabs website</a> announcing that they had added a Java DSL to their most recent Hoverfly release. I&#8217;ve been keeping up with <a href="http://hoverfly.io" target="_blank">Hoverfly</a> as a product for a while now, and it&#8217;s rapidly becoming an important player in the world of open source service virtualization solutions.

This Java DSL is somewhat similar to what <a href="http://wiremock.org/" target="_blank">WireMock</a> <a href="http://www.ontestautomation.com/getting-started-with-wiremock/" target="_blank">offers</a>, in that it allows you to quickly create stubs in your code, right when and where you need them. This blog post will not be a comparison between Hoverfly and WireMock, though. Both tools have some very useful features and have earned (and are stil earning) their respective place in the service virtualization space, so it&#8217;s up to you to see which of these best fits your project.

Instead, back to Hoverfly. Let&#8217;s take a look at a very basic stub definition first:

<pre class="brush: java; gutter: false">@ClassRule
public static HoverflyRule hoverflyRule = HoverflyRule.inSimulationMode(dsl(
   	service("www.afirststub.com")
       		.get("/test")
       		.willReturn(success("Success","text/plain"))
));</pre>

The syntax used to create a stub is pretty straightforward, as you can see. Here, we have defined a stub that listens at _http://www.afirststub.com/test_ and returns a positive response, defined using the _success()_ method, which boils down to Hoverfly returning an HTTP response with a 200 status code. The response further contains a response body with a string _Success_ as its body and _text/plain_ as its content type. By replacing these values with other content and content type values, you can easily create a stub that exerts the behaviour required for your specific testing needs.

As you can see, a Hoverfly stub is defined using the JUnit _@ClassRule_ annotation. For those of you that use TestNG, you can manage the Hoverfly instance (the Hoverfly class is included in the _hoverfly-java_ dependency) in _@Before_ and _@After_ classes instead.

We can check that this stub works as intended by writing and running a simple <a href="http://rest-assured.io" target="_blank">REST Assured</a> test for it:

<pre class="brush: java; gutter: false">@Test
public void testAFirstStub() {
		
	given().
	when().
		get("http://www.afirststub.com/test").
	then().
		assertThat().
		statusCode(200).
	and().
		body(equalTo("Success"));
}</pre>

Since Hoverfly works as a proxy, it can return any data you specify, even for existing endpoints. This means that you don&#8217;t need to change existing configuration files and endpoints in your system under test when you&#8217;re running your tests, no matter whether you&#8217;re using an actual endpoint or the Hoverfly stub representation of it. A big advantage, if you ask me.

Consider the following (utterly useless) use case: the endpoint <a href="http://ergast.com/api/f1/drivers/max_verstappen.json" target="_blank">http://ergast.com/api/f1/drivers/max_verstappen.json</a> returns data for the Formula 1 driver Max Verstappen in JSON format (you can click the link to see what data is returned). Assume we want to test what happens when the _permanentNumber_ changes value from _33_ to, say, _999_, we can simply create a stub that listens at the same endpoint, but returns different data:

<pre class="brush: java; gutter: false">@ClassRule
public static HoverflyRule hoverflyRule = HoverflyRule.inSimulationMode(dsl(
       	service("ergast.com")
       		.get("/api/f1/drivers/max_verstappen.json")
       		.willReturn(success("{\"permanentNumber\": \"999\"}", "application/json"))
));</pre>

Note that I removed all other data that is returned by the original endpoint for brevity and laziness. Mostly laziness, actually. Again, a simple test shows that instead of the data returned by the real endpoint, we now get our data from the Hoverfly stub:

<pre class="brush: java; gutter: false">@Test
public void testStubFakeVerstappen() {
		
	given().
	when().
		get("http://ergast.com/api/f1/drivers/max_verstappen.json").
	then().
		assertThat().
		body("permanentNumber",equalTo("999"));
}</pre>

Apart from being quite useless, the example above also introduces an issue with defining stubs that return larger amounts of JSON data (or XML data, for that matter): since JSON is not really well supported out of the box in Java (nor is XML), we could potentially end up with a large and unwieldy string with lots of character escaping for larger response bodies. Luckily, Hoverfly offers a solution for that in the form of object (de-)serialization.

Assume we have a simple Car POJO with two fields: _make_ and _model_. If we create an instance of that Car object like this:

<pre class="brush: java; gutter: false">private static Car myCar = new Car("Ford", "Focus");</pre>

and we pass this to the stub definition as follows:

<pre class="brush: java; gutter: false">@ClassRule
public static HoverflyRule hoverflyRule = HoverflyRule.inSimulationMode(dsl(
	service("www.testwithcarobject.com")
 		.get("/getmycar")
       		.willReturn(success(json(myCar)))
));</pre>

then Hoverfly will automatically serialize the Car object instance to JSON, which we can visualize by creating another REST Assured test and having it log the response body to the console:

<pre class="brush: java; gutter: false">@Test
public void testStubGetCarObject() {
		
	given().
	when().
		get("http://www.testwithcarobject.com/getmycar").
	then().
		log().
		body().
	and().
		assertThat().
		body("make",equalTo("Ford"));
}</pre>

When run, this test generates the following console output, indicating that Hoverfly successfully serialized our Car instance to JSON:

<pre class="brush: text; gutter: false">{
    "make": "Ford",
    "model": "Focus"
}</pre>

Note that the getters of the POJO need to be named correctly for this to work. For example, the getter for the _make_ field needs to be called _getMake()_, or else the object will not be serialized.

The final Hoverfly feature that I&#8217;d like to demonstrate is the ability to simulate error flows by returning bad requests. This can be done simply as follows:

<pre class="brush: java; gutter: false">@ClassRule
public static HoverflyRule hoverflyRule = HoverflyRule.inSimulationMode(dsl(
       	service("www.badrequest.com")
       		.get("/req")
       		.willReturn(badRequest())
));</pre>

and can be verified by checking the status code corresponding with a bad request, which is HTTP 400, with a test:

<pre class="brush: java; gutter: false">@Test
public void testStubBadRequest() {
		
	given().
	when().
		get("http://www.badrequest.com/req").
	then().
		assertThat().
		statusCode(400);
}</pre>

Similar to the Hoverfly product in general, its Java DSL is still under construction. This post was written based on version 0.3.6 and does not reflect newer versions. I had a bit of trouble getting the code to run, initially, but the SpectoLabs team have been very responsive and helpful in resolving the questions I had and the issues I encountered.

As an end note, please be aware that the Java DSL we&#8217;ve seen in this post is just one way of using Hoverfly. For a complete overview of the features and possibilities provided by the tool, please take a look at the <a href="http://hoverfly.readthedocs.io/en/latest/" target="_blank">online documentation</a>.

A Maven project featuring all of the examples and tests in this post can be downloaded <a href="http://www.ontestautomation.com/files/HoverflyJavaDSL.zip" target="_blank">here</a>. Tip: you&#8217;ll need to set your Java compiler compliance level to 1.8 in order for the code to compile and run correctly.