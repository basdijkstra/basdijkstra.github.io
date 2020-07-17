---
id: 739
title: Testing web services using in-memory web containers
date: 2015-01-29T13:30:18+01:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=739
permalink: /testing-web-services-using-in-memory-web-containers/
categories:
  - Web service testing
tags:
  - java
  - testng
  - web services
---
Last week, I read an interesting blog post on how to test Java web services inside an in-memory web container. I thought it was a fast and elegant solution to overcome a well-known unit testing problem: unit tests for web services become cumbersome when your web services uses the services provided by the container and you need to mock these container services. On the other hand, if you don&#8217;t mock these, your unit tests are virtually worthless. The blog post, originally written by [Antonio Goncalves](http://antoniogoncalves.org/2012/10/24/no-you-dont-need-to-mock-your-soap-web-service-to-test-it/), provides an elegant solution to this problem: use an in-memory container. In this case, he uses Sun’s implementation of Java SE 6, which includes a light-weight HTTP server API and implementation: _com.sun.net.httpserver_.

In this post, I will show a slightly improved version of his solution using my own web service implementation. In order to get the example below to work, all I needed to do was include a recent version of the _javax.jws_ library, which you can find for example [here](http://www.java2s.com/Code/JarDownload/javax.jws/javax.jws-3.1.2.2.jar.zip).

I have constructed a simple service that, given a composer, returns his name, his best known work, his country of birth and his age of death. The interface for this service looks like this:  
<!--more-->

<pre class="brush: java; gutter: false">import javax.jws.WebService;

@WebService
public interface LookUp {
	
	public String getName(Composer composer);
	
	public String getMostPopularComposition(Composer composer);
	
	public String getCountryOfBirth (Composer composer);
	
	public Integer getAge (Composer composer);	
}</pre>

The implementation of this web service is a simple class calling the _get_ methods of the _Composer_ class:

<pre class="brush: java; gutter: false">import javax.jws.WebService;

@WebService(endpointInterface = "com.ontestautomation.webservice.LookUp")
public class ComposerLookUp implements LookUp {

	@Override
	public String getName(Composer composer) {
		return composer.getName();
	}
	
	@Override
	public String getMostPopularComposition(Composer composer) {
		return composer.getMostPopularComposition();
	}

	@Override
	public String getCountryOfBirth(Composer composer) {
		return composer.getCountryOfBirth();
	}

	@Override
	public Integer getAge(Composer composer) {
		return composer.getAge();
	}
}</pre>

The _Composer_ class itself contains nothing more _get_ and _set_ methods for the Composer properties (the code below shows an example for only one property, the others are very similar):

<pre class="brush: java; gutter: false">public class Composer {
	
	String name;
	String mostPopularComposition;
	String countryOfBirth;
	Integer age;
	
	public Composer() {
	}
	
	public Composer(String name, String composition, String country, int age) {
		this.name = name;
		this.mostPopularComposition = composition;
		this.countryOfBirth = country;
		this.age = age;
	}
	
	public void setMostPopularComposition(String composition) {
		this.mostPopularComposition = composition;
	}
	
	public String getMostPopularComposition() {
		return this.mostPopularComposition;
	}
}</pre>

And now for the interesting part. We have defined a (very simple) web service, but how are we going to test it?

As said, using only a couple of lines of code, we can simply deploy our service in an in-memory container and call the methods from our test. First, let&#8217;s see how we can deploy and start the web service:

<pre class="brush: java; gutter: false">public class ComposerLookUpIntegrationTest {

	LookUp composerLookUp;
	Endpoint endpoint;

	@BeforeSuite
	public void setupService() throws MalformedURLException {
		
		// Publish the SOAP Web Service on a given endpoint
		endpoint = Endpoint.publish("http://localhost:8080/composerLookUp", new ComposerLookUp());
		
		// Provide data to access the web service
		URL wsdlDocumentLocation = new URL("http://localhost:8080/composerLookUp?wsdl");
		String namespaceURI = "http://webservice.ontestautomation.com/";
		String servicePart = "ComposerLookUpService";
		String portName = "ComposerLookUpPort";
		QName serviceQN = new QName(namespaceURI, servicePart);
		QName portQN = new QName(namespaceURI, portName);
		
		// Create a service instance
		Service service = Service.create(wsdlDocumentLocation, serviceQN);
		composerLookUp = service.getPort(portQN, LookUp.class);
	}</pre>

The _publish()_ method in the Endpoint class uses by default the light-weight HTTP server implementation that is included in Sun&#8217;s Java SE distribution. The only downside is that this HTTP server is in a com.sun package and therefore might not be portable.

To verify that we have indeed successfully launched our web service, let&#8217;s open the URL to the WSDL in a browser:  
[<img src="http://www.ontestautomation.com/wp-content/uploads/2015/01/composerservice_wsdl.png" alt="The WSDL for our ComposerService" width="1557" height="770" class="aligncenter size-full wp-image-743" srcset="https://www.ontestautomation.com/wp-content/uploads/2015/01/composerservice_wsdl.png 1557w, https://www.ontestautomation.com/wp-content/uploads/2015/01/composerservice_wsdl-300x148.png 300w, https://www.ontestautomation.com/wp-content/uploads/2015/01/composerservice_wsdl-1024x506.png 1024w" sizes="(max-width: 1557px) 100vw, 1557px" />](http://www.ontestautomation.com/wp-content/uploads/2015/01/composerservice_wsdl.png)  
That was easy, right? Now we can define whichever test we want to perform on our web service. Again, I used TestNG as a test framework:

<pre class="brush: java; gutter: false">@Test
public void checkComposerAge() {

	// Create a new Composer object
	Composer composer = new Composer("Mozart","Requiem","Austria",35);
	
	// Check whether the service returns the right data
	Assert.assertEquals(composerLookUp.getAge(composer),new Integer(35),"Mozart died at the age of 35");
}</pre>

Notice that we call the method as defined in the web service interface _LookUp_ rather than the get method of the _Composer_ class, as that would defy the purpose of our test altogether!

Next to the functional test, we can also easily perform a test that checks whether our web service has been deployed successfully:

<pre class="brush: java; gutter: false">@Test
public void checkServiceIsSetupProperly() {
		
	// Check that the service is published
	Assert.assertTrue(endpoint.isPublished());
		
	// Check that the service has the correct binding
	Assert.assertEquals("http://schemas.xmlsoap.org/wsdl/soap/http", endpoint.getBinding().getBindingID(),"Verify that the service binding is correct");
}</pre>

When I run the test suite containing this test (and two other tests), we can see that our tests are all executed successfully:  
[<img src="http://www.ontestautomation.com/wp-content/uploads/2015/01/test_results.png" alt="Test results for our tests" width="1158" height="399" class="aligncenter size-full wp-image-746" srcset="https://www.ontestautomation.com/wp-content/uploads/2015/01/test_results.png 1158w, https://www.ontestautomation.com/wp-content/uploads/2015/01/test_results-300x103.png 300w, https://www.ontestautomation.com/wp-content/uploads/2015/01/test_results-1024x353.png 1024w" sizes="(max-width: 1158px) 100vw, 1158px" />](http://www.ontestautomation.com/wp-content/uploads/2015/01/test_results.png)  
Hopefully this post has shown you how you can easily deploy and test a web service within an actual web container, without having to do a lot of cumbersome coding and/or configuration.

An Eclipse project containing the complete code for the sample web service and the tests that I have written and executed can be downloaded from [here](http://www.ontestautomation.com/files/TestWS.zip).