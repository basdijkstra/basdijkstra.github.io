---
id: 1139
title: Creating mock RESTful APIs using Sandbox
date: 2015-10-30T07:21:24+01:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=1139
permalink: /creating-mock-restful-apis-using-sandbox/
categories:
  - API testing
  - Service virtualization
tags:
  - API
  - mocks
  - RAML
  - rest-assured
  - sandbox
  - service virtualization
---
While browsing through my Twitter feed a couple of days ago I saw someone mentioning <a href="https://getsandbox.com/" target="_blank">Sandbox</a>, a Software as a Service (SaaS) solution for quick creation and deployment of mock services for development and testing purposes. After starting to play around with it a bit, I was rather impressed by the ease with which one can create useful mocks from <a href="http://swagger.io/" target="_blank">Swagger</a>, <a href="http://raml.org/" target="_blank">RAML</a> and <a href="http://www.w3.org/TR/wsdl" target="_blank">WSDL</a> API specifications.

As an example, I created a RAML API model for a simple API that shows information about test tools. Consumers of this API can create, read, update and delete entries in the list. The API contains six different operations:

  * Add a test tool to the list
  * Delete a test tool from the list
  * Get information about a specific test tool from the list
  * Update information for a specific test tool
  * Retrieve the complete list of test tools
  * Delete the complete list of test tools

You can view the complete RAML specification for this API <a href="http://www.ontestautomation.com/files//sandbox/testtools.raml" target="_blank">here</a>.

Creating a skeleton for the mock API in Sandbox is as easy as registering and then loading the RAML specification into Sandbox:  
[<img src="http://www.ontestautomation.com/wp-content/uploads/2015/10/load_raml.png" alt="Loading the RAML specification in Sandbox" width="596" height="278" class="aligncenter size-full wp-image-1144" srcset="https://www.ontestautomation.com/wp-content/uploads/2015/10/load_raml.png 596w, https://www.ontestautomation.com/wp-content/uploads/2015/10/load_raml-300x140.png 300w" sizes="(max-width: 596px) 100vw, 596px" />](http://www.ontestautomation.com/wp-content/uploads/2015/10/load_raml.png)Sandbox then generates a fully functioning API skeleton based on the RAML:  
[<img src="http://www.ontestautomation.com/wp-content/uploads/2015/10/api_skeleton_operations.png" alt="API operations from the RAML" width="1285" height="408" class="aligncenter size-full wp-image-1145" srcset="https://www.ontestautomation.com/wp-content/uploads/2015/10/api_skeleton_operations.png 1285w, https://www.ontestautomation.com/wp-content/uploads/2015/10/api_skeleton_operations-300x95.png 300w, https://www.ontestautomation.com/wp-content/uploads/2015/10/api_skeleton_operations-1024x325.png 1024w" sizes="(max-width: 1285px) 100vw, 1285px" />](http://www.ontestautomation.com/wp-content/uploads/2015/10/api_skeleton_operations.png)Sandbox also creates a routing routine for every operation:

<pre class="brush: javascript; gutter: false">var testtools = require("./routes/testtools.js")

/* Route definition styles:
 *
 *	define(path, method, function)
 *	soap(path, soapAction, function)
 *
 */
Sandbox.define("/testtools", "POST", testtools.postTesttools);
Sandbox.define("/testtools", "GET", testtools.getTesttools);
Sandbox.define("/testtools", "DELETE", testtools.deleteTesttools);
Sandbox.define("/testtools/{toolname}", "GET", testtools.getTesttools2);
Sandbox.define("/testtools/{toolname}", "PUT", testtools.putTesttools);
Sandbox.define("/testtools/{toolname}", "DELETE", testtools.deleteTesttools2);</pre>

and empty responses for every operation (these are the responses for the first two operations):

<pre class="brush: javascript; gutter: false">/*
 * POST /testtools
 *
 * Parameters (body params accessible on req.body for JSON, req.xmlDoc for XML):
 *
 */
exports.postTesttools = function(req, res) {
	res.status(200);

	// set response body and send
	res.send(&#039;&#039;);
};

/*
 * GET /testtools
 *
 * Parameters (named path params accessible on req.params and query params on req.query):
 *
 * q(type: string) - query parameter - Search phrase to look for test tools
 */
exports.getTesttools = function(req, res) {
	res.status(200);

	// set response body and send
	res.type(&#039;json&#039;);
	res.render(&#039;testtools_getTesttools&#039;);
};</pre>

Sandbox also creates template responses (these are rendered using _res.render(&#8216;testtools_getTesttools&#8217;)_ in the example above). These are mostly useful when dealing with either very large JSON responses or with XML responses, though, and as our example API has neither, we won&#8217;t use them here.

To show that the generated API skeleton is fully working, we can simply send a GET to the <a href="http://testtools.getsandbox.com/testtools" target="_blank">mock URL</a> and verify that we get a response:  
[<img src="http://www.ontestautomation.com/wp-content/uploads/2015/10/test_api_skeleton.png" alt="Testing the generated API skeleton" width="1198" height="273" class="aligncenter size-full wp-image-1150" srcset="https://www.ontestautomation.com/wp-content/uploads/2015/10/test_api_skeleton.png 1198w, https://www.ontestautomation.com/wp-content/uploads/2015/10/test_api_skeleton-300x68.png 300w, https://www.ontestautomation.com/wp-content/uploads/2015/10/test_api_skeleton-1024x233.png 1024w" sizes="(max-width: 1198px) 100vw, 1198px" />](http://www.ontestautomation.com/wp-content/uploads/2015/10/test_api_skeleton.png)Now that we&#8217;ve seen our API in action, it&#8217;s time to implement the operations to have the mock return more meaningful responses. We also want to add some state to be able to store new entries to our test tool list for later reference. For example, to add a test tool submitted using a POST to our list &#8211; after verifying that all parameters have been assigned a value &#8211; we use the following implemenation for the _export.postTesttools_ method:

<pre class="brush: javascript; gutter: false">/*
 * POST /testtools
 *
 */
exports.postTesttools = function(req, res) {
    
    if (req.body.name === undefined) {
        return res.json(400, { status: "error", details: "missing tool name" });
    }
    
    if (req.body.description === undefined) {
        return res.json(400, { status: "error", details: "missing tool description" });
    }
    
    if (req.body.url === undefined) {
        return res.json(400, { status: "error", details: "missing tool website" });
    }
    
    if (req.body.opensource === undefined) {
        return res.json(400, { status: "error", details: "missing tool opensource indicator" });
    }

    // add tool to list of tools
    state.tools.push(req.body);
    
    return res.json(200, { status: "ok", details: req.body.name + " successfully added to list" });
};</pre>

Likewise, I&#8217;ve added meaningful implementations for all other methods. The complete code for our mock API implementation can be found <a href="http://www.ontestautomation.com/files//sandbox/testtools.js" target="_blank">here</a>.

Finally, to prove that the mock API works as desired, I wrote a couple of quick tests using <a href="https://github.com/jayway/rest-assured" target="_blank">REST Assured</a>. Here&#8217;s a couple of them:

<pre class="brush: java; gutter: false">// base URL for our Sandbox testtools API
static String baseUrl = "http://testtools.getsandbox.com/testtools";
	
// this is added to the URL to perform actions on a specific item in the list
static String testtoolParameter = "/awesometool";
	
// original JSON body for adding a test tool to the list 
static String testtoolJson = "{\"name\":\"awesometool\",\"description\":\"This is an awesome test tool.\",\"url\":\"http://awesometool.com\",\"opensource\":\"true\"}";
	
// JSON body used to update an existing test tool
static String updatedTesttoolJson = "{\"name\":\"awesometool\",\"description\":\"This is an awesome test tool.\",\"url\":\"http://awesometool.com\",\"opensource\":\"false\"}";

@BeforeMethod
public void clearList() {
		
	// clear the list of test tools
	delete(baseUrl);
		
	// add an initial test tool to the list
	given().
		contentType("application/json").
		body(testtoolJson).
	when().			
		post(baseUrl).
	then();
}

@Test
public static void testGetAll() {
		
	// verify that a test tool is in the list of test tools
	given().
	when().
		get(baseUrl).
	then().
		body("name", hasItem("awesometool"));
}

@Test
public static void testDeleteAll() {
		
	// verify that the list is empty after a HTTP DELETE		
	given().
	when().
		delete(baseUrl).
	then().
		statusCode(200);
	
	given().
	when().
		get(baseUrl).
	then().
		body(equalTo("[]"));
}</pre>

An Eclipse project containing all of the tests I&#8217;ve written (there really aren&#8217;t that many, by the way, but I can think of a lot more) can be downloaded <a href="http://www.ontestautomation.com/files//sandbox/SandboxRestAssured.zip" target="_blank">here</a>.

One final note: Sandbox is a commercial SaaS solution, and therefore requires you to fork over some money if you want to use it in a serious manner. For demonstration and learning purposes, however, their <a href="https://getsandbox.com/pricing" target="_blank">free plan</a> works fine.

Overall, I&#8217;ve found Sandbox to be a great platform for rapidly creating useful mock API implementation, especially when you want to simulate RESTful services. I&#8217;m not sure whether it works as well when you&#8217;re working with XML services, because it seems a little more cmplicated to construct meaningful responses without creating just a bunch of prepared semi-fixed responses. Having said that, I&#8217;m pretty impressed with what Sandbox does and I&#8217;ll surely play around with it some more in the future.