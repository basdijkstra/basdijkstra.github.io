---
id: 1304
title: Using the TestNG ITestContext to create smarter REST Assured tests
date: 2016-03-09T20:51:35+01:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=1304
permalink: /using-the-testng-itestcontext-to-create-smarter-rest-assured-tests/
categories:
  - API testing
tags:
  - api testing
  - java
  - rest-assured
  - testng
---
In this post, I would like to demonstrate two different concepts that I think work very well together:

  * How to store and retrieve data objects using the TestNG _ITestContext_ for better code maintainability
  * How to communicate with RESTful web services that use basic or OAuth2 authorization using REST Assured

Using the PayPal sandbox API as an example, I will show you how you can create readable and maintainable tests for secured APIs using TestNG and REST Assured.

**The TestNG ITestContext**  
If you have a suffficiently large test suite, chances are high that you want to be able to share objects between individual tests to make your tests shorter and easier to maintain. For example, if you are calling a web service multiple times throughout your test suite and that web service requires an authentication token in order to be able to consume it, you might want to request and store that authentication token in the setup phase of your test suite, then retrieve and use it in all subsequent tests where this web service is invoked. This is exactly the scenario we&#8217;ll see in this blog post.

TestNG offers a means of storing and retrieving objects between tests through the <a href="http://testng.org/javadocs/org/testng/ITestContext.html" target="_blank">ITestContext</a> interface. This interface allows you to store (using the inherited _setAttribute()_ method) and retrieve (using _getAttribute()_) objects. Since the _ITestContext_ is created once and remains active for the duration of your test run, this is the perfect way to implement object sharing in your test suite. Making the _ITestContext_ available in your test methods is easy: just pass it as a parameter to your test method (we&#8217;ll see an example further down).

**REST Assured authentication options**  
As you might have read in one of my previous blog posts, <a href="http://rest-assured.io" target="_blank">REST Assured</a> is a Java library that allows you to write and execute readable tests for RESTful web services. Since we&#8217;re talking about secured APIs here, it&#8217;s good to know that REST Assured supports the following <a href="https://github.com/jayway/rest-assured/wiki/Usage#authentication" target="_blank">authentication mechanisms</a>:

  * Basic
  * Digest
  * OAuth (version 1 and 2)
  * Form

In the examples in this post, we&#8217;ll take a closer look at both Basic authentication (for requesting an OAuth token) and OAuth2 authentication (for invoking secured web service operations) in REST Assured.

**The PayPal sandbox API**  
To illustrate the concepts introduced above I chose to use the PayPal sandbox API. This is a sandbox version of the &#8216;live&#8217; PayPal API that can be used to test applications that integrate with PayPal, as well as to goof around. It&#8217;s free to use for anybody that has an active PayPal account. You can find all documentation on the API <a href="https://developer.paypal.com/" target="_blank">here</a>.

**Retrieving an Oauth2 access token**  
The first step &#8211; after creating the necessary test accounts in the sandbox environment &#8211; is to construct a call in REST Assured that retrieves an OAuth2 authentication token from the PayPal web service. This request uses basic authentication and looks like this:

<pre class="brush: java; gutter: false">@BeforeSuite
public void requestToken(ITestContext context) {

	String response =
			given().
				parameters("grant_type","client_credentials").
				auth().
				preemptive().
				basic("client_id","secret").
			when().
				post("https://api.sandbox.paypal.com/v1/oauth2/token").
				asString();
}</pre>

The actual values for _client_id_ and _secret_ are specific to the PayPal sandbox account. Note that we have stored the JSON response as a string. This makes it easier to parse it, as we will see in a moment. The response to this request contains our OAuth2 authentication token:

<a href="http://www.ontestautomation.com/using-the-testng-itestcontext-to-create-smarter-rest-assured-tests/access_token/" rel="attachment wp-att-1309"><img src="http://www.ontestautomation.com/wp-content/uploads/2016/03/access_token.png" alt="Our OAuth2 access token" width="1323" height="164" class="aligncenter size-full wp-image-1309" srcset="https://www.ontestautomation.com/wp-content/uploads/2016/03/access_token.png 1323w, https://www.ontestautomation.com/wp-content/uploads/2016/03/access_token-300x37.png 300w, https://www.ontestautomation.com/wp-content/uploads/2016/03/access_token-768x95.png 768w, https://www.ontestautomation.com/wp-content/uploads/2016/03/access_token-1024x127.png 1024w" sizes="(max-width: 1323px) 100vw, 1323px" /></a>

In order to store this token for use in our actual tests, we need to extract it from the response and store it in the TestNG _ITestContext_:

<pre class="brush: java; gutter: false">JsonPath jsonPath = new JsonPath(response);

String accessToken = jsonPath.getString("access_token");
		
context.setAttribute("accessToken", accessToken);

System.out.println("Access token: " + context.getAttribute("accessToken"));</pre>

The _System.out.println_ output shows us we have successfully stored the OAuth2 access token in the _ITestContext_:

<a href="http://www.ontestautomation.com/using-the-testng-itestcontext-to-create-smarter-rest-assured-tests/access_token_stored_in_itestcontext/" rel="attachment wp-att-1312"><img src="http://www.ontestautomation.com/wp-content/uploads/2016/03/access_token_stored_in_itestcontext.png" alt="Access token has been stored in the ITestContext" width="1011" height="24" class="aligncenter size-full wp-image-1312" srcset="https://www.ontestautomation.com/wp-content/uploads/2016/03/access_token_stored_in_itestcontext.png 1011w, https://www.ontestautomation.com/wp-content/uploads/2016/03/access_token_stored_in_itestcontext-300x7.png 300w, https://www.ontestautomation.com/wp-content/uploads/2016/03/access_token_stored_in_itestcontext-768x18.png 768w" sizes="(max-width: 1011px) 100vw, 1011px" /></a>

**Using the OAuth2 access token in your tests**  
Next, we want to use the previously stored token in subsequent API calls that require OAuth2 authentication. This is fairly straightforward: see for example this test that verifies that no payments have been made for the current test account:

<pre class="brush: java; gutter: false">@Test
public void checkNumberOfAssociatedPaymentsIsEqualToZero(ITestContext context) {

	given().
		contentType("application/json").
		auth().
		oauth2(context.getAttribute("accessToken").toString()).
	when().
		get("https://api.sandbox.paypal.com/v1/payments/payment/").
	then().
		assertThat().
		body("count", equalTo(0));
}</pre>

Note the use of _context.getAttribute()_ to retrieve the token from the _ITestContext_. This test passes, which not only tells us that no payments have yet been made by this account, but also that our authentication worked as expected (otherwise, we would have received an authentication error).

**Download an example project**  
The Maven project containing all code from this post can be downloaded <a href="http://www.ontestautomation.com/files/RestAssuredAuthentication.zip" target="_blank">here</a>.