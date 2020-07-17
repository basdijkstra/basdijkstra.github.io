---
id: 2028
title: Creating data driven API tests with REST Assured and TestNG
date: 2017-09-20T16:05:08+02:00
author: Bas
layout: revision
guid: http://www.ontestautomation.com/1423-revision-v1/
permalink: /1423-revision-v1/
---
As you&#8217;ve probably read in previous posts on this site, I am a big fan of the <a href="http://rest-assured.io" target="_blank">REST Assured</a> library for writing tests for RESTful web services. In this post, I will demonstrate how to create even more powerful tests with REST Assured by applying the data driven principle, with some help from TestNG.

**Combining REST Assured and TestNG**  
REST Assured itself is a Domain Specific Language (DSL) for writing tests for RESTful web services and does not offer a mechanism for writing data driven tests (i.e., the ability to write a single test that can be executed multiple times with different sets of input and validation parameters). However, REST Assured tests are often combined with <a href="http://junit.org/junit4/" target="_blank">JUnit</a> or <a href="http://testng.org/doc/index.html" target="_blank">TestNG</a>, and the latter offers an easy to use mechanism to create data driven tests through the use of the DataProvider mechanism.

**Creating a TestNG DataProvider**  
A TestNG DataProvider is a method that returns an object containing test data that can then be fed to the actual tests (REST Assured tests in this case). This data can be hardcoded, but it can also be read from a database or a JSON specification, for example. It&#8217;s simply a matter of implementing the DataProvider in the desired way. The following example DataProvider creates a test data object (labeled _md5hashes_) that contains some example text strings and their md5 hash:

<pre class="brush: java; gutter: false">@DataProvider(name = "md5hashes")
public String[][] createMD5TestData() {
		
	return new String[][] {
			{"testcaseOne", "4ff1c9b1d1f23c6def53f957b1ed827f"},
			{"testcaseTwo", "39738347fb533d798aca9ae0f56ca126"},
			{"testcaseThree", "db6b151bb4bde46fddb361043bc3e2d9"}
	};
}</pre>

**Using the DataProvider in REST Assured tests (with query parameters)**  
Using the data specified in the DataProvider in REST Assured tests is fairly straightforward. In the example below, we&#8217;re using it in combination with a RESTful web service that uses a single query parameter:

<pre class="brush: java; gutter: false">@Test(dataProvider = "md5hashes")
public void md5JsonTest(String originalText, String md5Hash) {
		
	given().
		parameters("text", originalText).
	when().
		get("http://md5.jsontest.com").
	then().
		assertThat().
		body("md5", equalTo(md5Hash));
}</pre>

Notice how the data in the DataProvider can be used for specification of expected results just as easy as for specifying input parameters. When we run this test, we can see in the output that the test is run three times, once for every set of input and validation parameters:

<a href="http://www.ontestautomation.com/creating-data-driven-api-tests-with-rest-assured-and-testng/rest_assured_data_driven_console_output/" rel="attachment wp-att-1426"><img src="http://www.ontestautomation.com/wp-content/uploads/2016/05/rest_assured_data_driven_console_output.png" alt="Console output for our data driven REST Assured test" width="730" height="221" class="aligncenter size-full wp-image-1426" srcset="https://www.ontestautomation.com/wp-content/uploads/2016/05/rest_assured_data_driven_console_output.png 730w, https://www.ontestautomation.com/wp-content/uploads/2016/05/rest_assured_data_driven_console_output-300x91.png 300w" sizes="(max-width: 730px) 100vw, 730px" /></a>

**Using the DataProvider in REST Assured tests (with query parameters)**  
The other parameter approach commonly used with RESTful web services is the use of path parameters. Let&#8217;s create another DataProvider, this one specifying Formula 1 circuits and the country they are situated in:

<pre class="brush: java; gutter: false">@DataProvider(name = "circuitLocations")
public String[][] createCircuitTestData() {
		
	return new String[][] {
			{"adelaide","Australia"},
			{"detroit","USA"},
			{"george","South Africa"}
	};
}</pre>

The <a href="http://ergast.com/mrd/" target="_blank">Ergast Developer API</a> provides motor racing data through a public API (check it out, it&#8217;s really cool). Here&#8217;s how to parameterize the call to get specific circuit data and check the country for each circuit with REST Assured, again using our DataProvider:

<pre class="brush: java; gutter: false">@Test(dataProvider = "circuitLocations")
public void circuitLocationTest(String circuitId, String location) {
		
	given().
		pathParameters("circuitId",circuitId).
	when().
		get("http://ergast.com/api/f1/circuits/{circuitId}.json").
	then().
		assertThat().
		body("MRData.CircuitTable.Circuits[0].Location.country",equalTo(location));
}</pre>

The only real difference is in how to set up path parameter use in REST Assured (see also <a href="https://github.com/jayway/rest-assured/wiki/Usage#path-parameters" target="_blank">here</a>), otherwise making your tests data driven is just as easy as with query parameters.

When we run this second test, we can see again that it&#8217;s run three times, once for every circuit specified:

<a href="http://www.ontestautomation.com/creating-data-driven-api-tests-with-rest-assured-and-testng/rest_assured_data_driven_console_output_path_params/" rel="attachment wp-att-1427"><img src="http://www.ontestautomation.com/wp-content/uploads/2016/05/rest_assured_data_driven_console_output_path_params.png" alt="Console output for our data driven REST Assured test (with path parameters_" width="752" height="218" class="aligncenter size-full wp-image-1427" srcset="https://www.ontestautomation.com/wp-content/uploads/2016/05/rest_assured_data_driven_console_output_path_params.png 752w, https://www.ontestautomation.com/wp-content/uploads/2016/05/rest_assured_data_driven_console_output_path_params-300x87.png 300w" sizes="(max-width: 752px) 100vw, 752px" /></a>

A Maven Eclipse project containing the code demonstrated in this blog post can be downloaded <a href="http://www.ontestautomation.com/files/RestAssuredDataDriven.zip" target="_blank">here</a>.