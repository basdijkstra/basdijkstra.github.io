---
id: 1619
title: (De)serializing POJOs in REST Assured
date: 2016-09-30T21:08:39+02:00
author: Bas
layout: revision
guid: http://www.ontestautomation.com/1614-revision-v1/
permalink: /1614-revision-v1/
---
In this post, I&#8217;d like to demonstrate how you can leverage the ability of REST Assured to (de-)serialize Plain Old Java Objects (better known as POJOs) for more powerful testing.

As an example, we&#8217;ll use the following POJO representing a car and some of its properties:

<pre class="brush: java; gutter: false">public class Car {
	
	String make;
	String model;
	int year;
	
	public Car() {		
	}
	
	public Car(String make, String model, int year) {
		
		this.make = make;
		this.model = model;
		this.year = year;
	}
	
	public String getMake() {
		return this.make;
	}

	public void setMake(String make) {
		this.make = make;
	}
	
	public String toString() {
		return "My car is a " + this.year + " " + this.make + " " + this.model;
	}
}</pre>

Please note that I&#8217;ve removed the getters and setters for the other properties for brevity. Now, let&#8217;s create an instance of the car:

<pre class="brush: java; gutter: false">Car myCar = new Car("Aston Martin","DB9",2004);</pre>

Say we want to transmit the information stored in this object instance to a RESTful API, without having to map each individual property of our car to a corresponding field in the request. REST Assured supports this by allowing you to serialize the _myCar_ object instance as follows:

<pre class="brush: java; gutter: false">@Test
public void testCarSerialization() {
				
	given().
		contentType("application/json").
		body(myCar).
	when().
		post("http://localhost:9876/carstub").
	then().
		assertThat().
		body(equalTo("Car has been stored"));
}</pre>

So, all you have to do is pass the object using _body()_. REST Assured will automatically translate this to the following request body:

<pre class="brush: text; gutter: false">{
    "make": "Aston Martin",
    "model": "DB9",
    "year": 2004
}</pre>

Neat, right? In this example, we serialized the car object to a JSON request, but REST Assured also allows you to serialize it to XML or HTML. Additionally, you can create custom object mappings as well. See <a href="https://github.com/rest-assured/rest-assured/wiki/Usage#object-mapping" target="_blank">this page</a> in the REST Assured documentation for more information.

REST Assured also supports deserialization, meaning that we can easily transform a suitably formatted API response to a POJO instance:

<pre class="brush: java; gutter: false">@Test
public void testCarDeserialization() {
		
	Car myDeserializedCar = get("http://localhost:9876/carstub").as(Car.class);
		
	System.out.println(myDeserializedCar.toString());
	
	Assert.assertEquals("Check the car make", myDeserializedCar.getMake(), "Aston Martin");		
}</pre>

Note that _http://localhost:9876/carstub_ points to a <a href="http://wiremock.org/" target="_blank">WireMock</a> stub I&#8217;ve created to illustrate this example. The fact that our assertion is passing and that the console shows the following output when running the test tells us that deserialization has been successful:

<pre class="brush: text; gutter: false">My car is a 2004 Aston Martin DB9</pre>

You can download a Maven project containing all of the code I&#8217;ve used in the examples in this blog post [here](http://www.ontestautomation.com/files/RestAssuredSerialization.zip).