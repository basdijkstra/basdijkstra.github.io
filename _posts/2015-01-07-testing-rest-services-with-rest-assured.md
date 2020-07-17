---
id: 711
title: Testing REST services with REST Assured
date: 2015-01-07T09:45:37+01:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=711
permalink: /testing-rest-services-with-rest-assured/
categories:
  - Test automation tools
  - Web service testing
tags:
  - java
  - rest-assured
  - testng
  - web services
---
For those of you wanting to add the possibility to validate RESTful web services to your test automation framework, REST Assured can be a very useful way to do just that. In this post, I want to show you how to perform some basic tests on both JSON and XML-based REST services.

Installing and configuring REST Assured is easy, just download the latest version from [the website](http://code.google.com/p/rest-assured/) and add the relevant .jar files to your testing project and you&#8217;re good to go.

**Testing a REST service that returns XML**  
First, let&#8217;s try validating a REST service that returns a response in XML format. As an example, I use the ParaBank REST service to get the customer details for a customer with ID 12212 (click [here](http://parabank.parasoft.com/parabank/services/bank/customers/12212/) to invoke the service and see the response in your browser). For this response, I want to check that the returned customer ID is equal to 12212, that the first name is equal to John, and that the last name is equal to Doe. The following REST Assured-test does exactly that. Note that the test consists of just a single statement, but I&#8217;ve added some line breaks to make it more readable.

<pre class="brush: java; gutter: false">package com.ontestautomation.restassured.test;

import static com.jayway.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;

public class RestServiceTest {
	
	public static void main (String args[]) {
		
		get("http://parabank.parasoft.com/parabank/services/bank/customers/12212/").
		then().
			assertThat().body("customer.id", equalTo("12212")).
		and().
			assertThat().body("customer.firstName", equalTo("John")).
		and().
			assertThat().body("customer.lastName", equalTo("Doe"));		
	}
}</pre>

Pretty straightforward, right? All you need to do is invoke the service using _get()_ and then perform the required checks using _assertThat()_ and the _equalTo()_ matcher (which is part of Hamcrest by the way, not of REST Assured). Identifying the response elements to be checked is done using a dot notation. For example, _customer.id_ identifies the _id_ element that is a child of the _customer_ element, which is the root element of the response message.

When we run this test, we see the results displayed neatly in our IDE:  
[<img src="http://www.ontestautomation.com/wp-content/uploads/2015/01/rest-assured_results.png" alt="REST Assured test results" width="859" height="112" class="aligncenter size-full wp-image-714" srcset="https://www.ontestautomation.com/wp-content/uploads/2015/01/rest-assured_results.png 859w, https://www.ontestautomation.com/wp-content/uploads/2015/01/rest-assured_results-300x39.png 300w" sizes="(max-width: 859px) 100vw, 859px" />](http://www.ontestautomation.com/wp-content/uploads/2015/01/rest-assured_results.png)One of the assertions fails, since the actual last name for customer 12212 is Smith, not Doe.

**Testing a REST service that returns JSON**  
REST Assured can be used just as easily to perform checks on REST services that return JSON instead of XML. In the next example, I use a REST service that takes a text string and returns the md5 checksum for that string, together with the original string.  
[<img src="http://www.ontestautomation.com/wp-content/uploads/2015/01/rest-assured_json_response.png" alt="REST service with JSON response" width="450" height="147" class="aligncenter size-full wp-image-717" srcset="https://www.ontestautomation.com/wp-content/uploads/2015/01/rest-assured_json_response.png 450w, https://www.ontestautomation.com/wp-content/uploads/2015/01/rest-assured_json_response-300x98.png 300w" sizes="(max-width: 450px) 100vw, 450px" />](http://www.ontestautomation.com/wp-content/uploads/2015/01/rest-assured_json_response.png)The following code example checks both the original string value and the md5 checksum value for this REST service:

<pre class="brush: java; gutter: false">package com.ontestautomation.restassured.test;

import static com.jayway.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;

public class RestServiceTestGherkin {
	
	public static void main(String args[]) {
		
		given().
			parameters("text", "test").
		when().
			get("http://md5.jsontest.com").
		then().
			body("md5",equalTo("098f6bcd4621d373cade4e832627b4f6")).
		and().
			body("original", equalTo("incorrect"));
	}
}</pre>

As you can see, checking values from JSON responses is just as straightforward as is the case for XML responses. Two additional things do have changed in this example though:

  * As this web service call takes parameters &#8211; in this case a single parameter containing the string for which the md5 value is to be calculated &#8211; we need to define the value of this parameter before calling the service. This is done using the _parameters()_ method, which takes parameter key-value pairs as its argument. Alternatively, we could have omitted the parameters call and just perform a _get(&#8220;http://md5.jsontest.com?text=test&#8221;)_ instead, but this is both more elegant and more maintainable.
  * Also, as you can see from this example, in REST Assured, you can specify your tests in a BDD format using the Given-When-Then construction that is also used (for example) in [Cucumber](http://www.ontestautomation.com/writing-bdd-tests-using-selenium-and-cucumber/ "Writing BDD tests using Selenium and Cucumber").

When we run this test, again, the test results show up nicely in our IDE:  
[<img src="http://www.ontestautomation.com/wp-content/uploads/2015/01/rest-assured_results_gherkin.png" alt="Test results for JSON REST service test" width="843" height="113" class="aligncenter size-full wp-image-718" srcset="https://www.ontestautomation.com/wp-content/uploads/2015/01/rest-assured_results_gherkin.png 843w, https://www.ontestautomation.com/wp-content/uploads/2015/01/rest-assured_results_gherkin-300x40.png 300w" sizes="(max-width: 843px) 100vw, 843px" />](http://www.ontestautomation.com/wp-content/uploads/2015/01/rest-assured_results_gherkin.png)I&#8217;ve added another defect on purpose to show how they are displayed: the value of the _original_ element in the response should of course be _test_ instead of _incorrect_.

**Combining REST Assured and TestNG**  
Finally, we can also combine REST Assured with TestNG to create an even more powerful REST service testing framework. In that way, you can for example use the reporting capabilities of TestNG for your REST service tests, as well as integrate REST Assured tests into an existing test framework. Doing so is as easy as replacing the default Hamcrest assertions with TestNG assertions and performing the required checks:

<pre class="brush: java; gutter: false">package com.ontestautomation.restassured.test;

import static com.jayway.restassured.RestAssured.*;

import org.testng.Assert;
import org.testng.annotations.Test;

import com.jayway.restassured.path.xml.XmlPath;

public class RestServiceTestNG {
	
	@Test
	public void testng() {
		
		// Get the response XML from the REST service and store it as a String
		String xml = get("http://parabank.parasoft.com/parabank/services/bank/customers/12212/").andReturn().asString();
		
		// Retrieve the values to be checked from the XML as a String
		XmlPath xmlPath = new XmlPath(xml).setRoot("customer");
		String customerId = xmlPath.getString("id");
		String firstName = xmlPath.getString("firstName");
		String lastName = xmlPath.getString("lastName");
		
		// Perform the required checks
		Assert.assertEquals(customerId, "12212");
		Assert.assertEquals(firstName, "John");
		Assert.assertEquals(lastName, "Doe");
	}
}</pre>

When using TestNG, we first store the response of the web service as a String, after which we can check whatever we want to check. It does make the test code a little longer, and in my personal opinion a little less elegant. On the upside, we can now seamlessly integrate our REST Assured-tests in a larger TestNG-based test framework and profit from the automatic report generation that TestNG provides.

Running this test results in the following output in our IDE:  
[<img src="http://www.ontestautomation.com/wp-content/uploads/2015/01/rest-assured_results_testng.png" alt="Test results for TestNG-based tests" width="1436" height="331" class="aligncenter size-full wp-image-722" srcset="https://www.ontestautomation.com/wp-content/uploads/2015/01/rest-assured_results_testng.png 1436w, https://www.ontestautomation.com/wp-content/uploads/2015/01/rest-assured_results_testng-300x69.png 300w, https://www.ontestautomation.com/wp-content/uploads/2015/01/rest-assured_results_testng-1024x236.png 1024w" sizes="(max-width: 1436px) 100vw, 1436px" />](http://www.ontestautomation.com/wp-content/uploads/2015/01/rest-assured_results_testng.png)and the following HTML report:  
[<img src="http://www.ontestautomation.com/wp-content/uploads/2015/01/rest-assured_results_testng_html.png" alt="HTML results for the TestNG-based REST Assured tests" width="1434" height="448" class="aligncenter size-full wp-image-723" srcset="https://www.ontestautomation.com/wp-content/uploads/2015/01/rest-assured_results_testng_html.png 1434w, https://www.ontestautomation.com/wp-content/uploads/2015/01/rest-assured_results_testng_html-300x94.png 300w, https://www.ontestautomation.com/wp-content/uploads/2015/01/rest-assured_results_testng_html-1024x320.png 1024w" sizes="(max-width: 1434px) 100vw, 1434px" />](http://www.ontestautomation.com/wp-content/uploads/2015/01/rest-assured_results_testng_html.png)**Additional features**  
Apart from the options shown above, REST Assured provides some other features that can come in very handy when testing REST-based web services. For instance, you can also test services that require authentication in order to be invoked. For basic authentication, you can simply add the credentials to your test statement as follows:

<pre class="brush: java; gutter: false">given().auth().basic("username", "password").when().</pre>

It is also possible to invoke services that require OAuth authorization (this requires the help of the [Scribe](https://github.com/fernandezpablo85/scribe-java) library):

<pre class="brush: java; gutter: false">given().auth().oauth(..).when().</pre>

If you want to explicitly validate that a web service returns the correct content type (JSON, in this case), you can do this:

<pre class="brush: java; gutter: false">get("/path/to/service").then().assertThat().contentType(ContentType.JSON)</pre>

For a complete overview of all REST Assured features, you can refer to the [online documentation](http://code.google.com/p/rest-assured/wiki/Usage).

Happy REST testing!