---
id: 440
title: Testing RESTful webservices
date: 2014-05-23T07:06:07+02:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=440
permalink: /testing-restful-webservices/
categories:
  - Web service testing
tags:
  - API
  - java
  - RESTful
  - testing
  - web services
---
With the world of IT systems and applications becoming more and more distributed, testing the interfaces between system components becomes ever more important. [In a previous post](http://www.ontestautomation.com/a-very-basic-web-service-test-tool/ "A very basic web service test tool") I have introduced a very basic way to test a SOAP web service interface. However, nowadays, in the world of APIs, the REpresentational State Transfer or REST standard becomes increasingly popular due to its lightweight nature. This article introduces a way to test RESTful APIs in a simple yet effective manner, and in a way that integrates smoothly with your existing Java-based testing frameworks, such as those based on Selenium Webdriver.

RESTful APIs come in several varieties. In this post, we are going to see two of them: one returning XML responses (I used [this](http://parabank.parasoft.com/parabank/services/bank/customers/12212/) one) and one returning JSON responses (I used [this](http://api.openweathermap.org/data/2.5/weather?q=Amsterdam) one). You&#8217;re welcome to click on either link to see the difference in response format.

Also, I have used some external libraries to get the code to work:

  * [The Apache HttpComponents library](http://hc.apache.org) to communicate with web services
  * JUnit 4 for the assertions. You could also use the Selenium Webdriver library for this.

**Verify the HTTP status code**  
The first thing we are going to test for our services is whether they respond to our requests at all. This means we are going to validate whether the HTTP response code for the REST service is correct. There way to do this is equal for both XML and JSON REST APIs:

<pre class="brush: java; gutter: false; first-line: 1; highlight: []; html-script: false">public static void testStatusCode(String restURL) throws ClientProtocolException, IOException {

	HttpUriRequest request = new HttpGet(restURL);
	HttpResponse httpResponse = HttpClientBuilder.create().build().execute(request);
		
	Assert.assertEquals(httpResponse.getStatusLine().getStatusCode(),HttpStatus.SC_OK);
}</pre>

Pretty simple, right? As you probably know, calling a RESTful API is done by either sending a HTTP POST or a HTTP GET to a specific URL. This in contrast with SOAP web services, where you send a specific XML message to an endpoint. Then, retrieve the HTTP status code from the response and check whether this is OK (i.e., equal to HTTP 200).

**Verify the response content type**  
Next, we are going to verify whether our web service sends us the content type we expect (either XML or JSON). Again, this can be done with just a couple of lines of code:

<pre class="brush: java; gutter: false; first-line: 1; highlight: []; html-script: false">public static void testMimeType(String restURL, String expectedMimeType) throws ClientProtocolException, IOException {
		
	HttpUriRequest request = new HttpGet(restURL);
	HttpResponse httpResponse = HttpClientBuilder.create().build().execute(request);
		
	Assert.assertEquals(expectedMimeType,ContentType.getOrDefault(httpResponse.getEntity()).getMimeType());
}</pre>

Again, just call the RESTful API URL and retrieve the information you need from the response. In this case, we are interested in the MIME type, which should be either &#8216;application/xml&#8217; or &#8216;application/json&#8217;, depending on the URL we use. 

**Verify the response content**  
Now that we have verified whether our services return correct responses with the correct MIME type, it&#8217;s time to look at the actual content of the response. This is where testing XML RESTful APIs differs from testing JSON RESTful APIs, since we need to parse the response to extract the elements and element values we&#8217;re interested in.

For XML, we can do this using the following piece of code:

<pre class="brush: java; gutter: false; first-line: 1; highlight: []; html-script: false">public static void testContent(String restURL, String element, String expectedValue) throws ClientProtocolException, IOException, SAXException, ParserConfigurationException {
		
	Document doc = DocumentBuilderFactory.newInstance().newDocumentBuilder().parse(restURL);
	NodeList nodelist = doc.getElementsByTagName(element);
		
	Assert.assertEquals(expectedValue,nodelist.item(0).getTextContent());		
}</pre>

Note that we use a different way to retrieve the result from the REST URL as we need to parse it as actual XML. After we&#8217;ve done that, we simply look for the XML element we&#8217;re interested in and compare its value to the value we expect.

For JSON, we can verify element values as follows:

<pre class="brush: java; gutter: false; first-line: 1; highlight: []; html-script: false">public static void testContentJSON(String restURL, String element, String expectedValue) throws ClientProtocolException, IOException, SAXException, ParserConfigurationException, JSONException {

	HttpUriRequest request = new HttpGet(restURL);
	HttpResponse httpResponse = HttpClientBuilder.create().build().execute(request);

	// Convert the response to a String format
	String result = EntityUtils.toString(httpResponse.getEntity());

	// Convert the result as a String to a JSON object
	JSONObject jo = new JSONObject(result);

	Assert.assertEquals(expectedValue, jo.getString(element));
}</pre>

Here, we need to take an intermediate step to parse the response to a sensible format. First, we convert the HttpResponse to a String, then we convert this String to a JSONObject. From this JSONObject we can then extract the value for a specific element and compare it to an expected value.

The above examples should get you started nicely when you&#8217;re asked to include testing RESTful web services in your automated testing solutions.

Again, happy testing!

An example Eclipse project where I use the code above can be downloaded [here](http://www.ontestautomation.com/files/RESTTester.zip).