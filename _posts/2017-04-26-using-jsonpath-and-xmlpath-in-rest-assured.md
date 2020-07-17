---
id: 1848
title: Using JsonPath and XmlPath in REST Assured
date: 2017-04-26T08:00:50+02:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=1848
permalink: /using-jsonpath-and-xmlpath-in-rest-assured/
categories:
  - API testing
tags:
  - jsonpath
  - rest-assured
  - xmlpath
---
While preparing my <a href="http://rest-assured.io" target="_blank">REST Assured</a> workshop for the <a href="http://www.romaniatesting.ro" target="_blank">Romanian Testing Conference</a> next month, I ran into a subject I feel I didn&#8217;t cover enough in the previous times I hosted the workshop: how to effectively use JsonPath and XmlPath to extract specific elements and element groups in RESTful API responses before verifying them. Here are a couple of tricks I learned since and worked into the exercises that&#8217;ll be part of the workshop from now on.

The examples in this post are all based on the following XML response:

<pre class="brush: xml; gutter: false">&lt;?xml version="1.0" encoding="UTF-8" ?&gt;
&lt;cars&gt;
	&lt;car make="Alfa Romeo" model="Giulia"&gt;
		&lt;country&gt;Italy&lt;/country&gt;
		&lt;year&gt;2016&lt;/year&gt;
	&lt;/car&gt;
	&lt;car make="Aston Martin" model="DB11"&gt;
		&lt;country&gt;UK&lt;/country&gt;
		&lt;year&gt;1949&lt;/year&gt;
	&lt;/car&gt;
	&lt;car make="Toyota" model="Auris"&gt;
		&lt;country&gt;Japan&lt;/country&gt;
		&lt;year&gt;2012&lt;/year&gt;
	&lt;/car&gt;
&lt;/cars&gt;</pre>

The syntax for JsonPath is very, very similar, except for the obvious lack of support for attributes in JsonPath (JSON does not have attributes).

**Extracting a single element based on its index**  
Let&#8217;s get started with an easy example. Say I want to check that the first car in the list is made in Italy. To do this, we can simply traverse the XML tree until we get to the right element, using the index [0] to select the first car in the list:

<pre class="brush: java; gutter: false">@Test
public void checkCountryForFirstCar() {
						
	given().
	when().
		get("http://path.to/cars").
	then().
		assertThat().
		body("cars.car[0].country", equalTo("Italy"));
}</pre>

Similarly, we can check that the last car came on the market in 2012, using the [-1] index (this points us to the last item in a list):

<pre class="brush: java; gutter: false">@Test
public void checkYearForLastCar() {
						
	given().
	when().
		get("http://path.to/cars").
	then().
		assertThat().
		body("cars.car[-1].year", equalTo("2012"));
}</pre>

**Extracting an attribute value**  
Just as easily, you can extract and check the value of an attribute in an XML document. If we want to check that the model of the second car in the list is &#8216;DB11&#8217;, we can do so using the &#8216;@&#8217; notation:

<pre class="brush: java; gutter: false">@Test
public void checkModelForSecondCar() {
						
	given().
	when().
		get("http://path.to/cars").
	then().
		assertThat().
		body("cars.car[1].@model", equalTo("DB11"));
}</pre>

**Counting the number of occurrences of a specific value**  
Now for something a little more complex: let&#8217;s assume we want to check that there&#8217;s only one car in the list that is made in Japan. To do this, we&#8217;ll need to apply a _findAll_ filter to the country element, and subsequently count the number of items in the list using _size()_:

<pre class="brush: java; gutter: false">@Test
public void checkThereIsOneJapaneseCar() {
		
	given().
	when().
		get("http://path.to/cars").
	then().
		assertThat().
		body("cars.car.findAll{it.country==&#039;Japan&#039;}.size()", equalTo(1));
}</pre>

Likewise, we can also check that there are two cars that are made either in Italy or in the UK, using the _in_ operator:

<pre class="brush: java; gutter: false">@Test
public void checkThereAreTwoCarsThatAreMadeEitherInItalyOrInTheUK() {
		
	given().
	when().
		get("http://path.to/cars").
	then().
		assertThat().
		body("cars.car.findAll{it.country in [&#039;Italy&#039;,&#039;UK&#039;]}.size()", equalTo(2));
}</pre>

**Performing a search for a specific string of characters**  
Finally, instead of looking for exact attribute or element value matches, we can also filter on substrings. This is done using the _grep()_ method (very similar to the Unix command). If we want to check the number of cars in the list whose make starts with an &#8216;A&#8217;, we can do so like this:

<pre class="brush: java; gutter: false">@Test
public void checkThereAreTwoCarsWhoseMakeStartsWithAnA() {
		
	given().
	when().
		get("http://localhost:9876/xml/cars").
	then().
		assertThat().
		body("cars.car.@make.grep(~/A.*/).size()", equalTo(2));
}</pre>

If you know of more examples, or if I missed another example of how to use JsonPath / XmlPath, do let me know!