---
id: 1762
title: Managing and publishing test results with Tesults
date: 2017-01-24T14:26:23+01:00
author: Bas
layout: revision
guid: http://www.ontestautomation.com/1756-autosave-v1/
permalink: /1756-autosave-v1/
---
A very important factor in the success of test automation is making test results readily available for everyone to see. Even the greatest set of automated tests becomes a lot less powerful if the results aren&#8217;t visible to anybody interested in the quality of the system under test. When people see that all tests pass and keep passing, it builds trust in the people working on or with an application. When tests fail, it should be immediately visible to all parties involved, so adequate actions can be taken to fix things (be it either the test itself or the system under test). Therefore, having adequate reporting in place and making test results easily accessible and clearly visible is something every development and testing team should take care of. Luckily, a lot of people reading this blog agree (at least to some extent), <a href="http://www.ontestautomation.com/creating-html-reports-for-your-selenium-tests-using-extentreports/" target="_blank">because</a> <a href="http://www.ontestautomation.com/using-the-extentreports-testng-listener-in-selenium-page-object-tests/" target="_blank">the</a> <a href="http://www.ontestautomation.com/create-your-own-html-report-from-selenium-tests/" target="_blank">posts</a> I&#8217;ve written in the past about creating test reports are among the most read on the site.

Recently, I&#8217;ve been introduced to a relatively new venture that attempts to make the management and publication of test results as easy as possible. <a href="http://www.tesults.com" target="_blank">Tesults</a> is a cloud-based platform that can be used to upload and display automated test results in order to give better insight into the quality of your system under test. Recently, they released a Java wrapper that allows for easy integration of the uploading of test results to Tesults, which triggered me to try it out and see how well Tesults works. In this blog post, I&#8217;ll walk you through the process.

After creating a Tesults account (it is a commercial service with a subscription model, but it&#8217;s free for everybody to try for 7 days), you have to set up a project and a target that the test results will be associated with. For example, the project can relate to the application you&#8217;re working on, whereas the target (which is a child of a project) can be used to distinguish between branches, projects, whatever you like. For each target, Tesults creates a target string that you need to specify in your test project and which forms the connection between your test results and the Tesults project. We&#8217;ll see how this target string is used later on.

We also need a couple of tests that generate the test results that are to be uploaded to Tesults. For this, I wrote a couple of straightforward <a href="http://rest-assured.io" target="_blank">REST Assured</a> tests: a couple of ones that should pass, one that should fail, and some data driven tests to see how Tesults handles these. As an example, here&#8217;s the test that&#8217;s supposed to fail:

<pre class="brush: java; gutter: false">@Test(description = "Test that Ayrton Senna is in the list of 2016 F1 drivers (fails)")
public void simpleDriverTestWithFailure() {
		
	given().
	when().
		get("http://ergast.com/api/f1/2016/drivers.json").
	then().
		assertThat().
		body("MRData.DriverTable.Drivers.driverId", hasItem("senna"));
}</pre>

Basically, for the test results to be uploaded to Tesults, they need to be gathered in a test results object. Each test case result consists of

  * A name for the test
  * A description of the purpose of the test
  * The test suite that the test belongs to (each target can in turn have multiple test suites)
  * A result (obviously), which can be either &#8216;pass&#8217;, &#8216;fail&#8217; or &#8216;unknown&#8217;

For my demo tests, I&#8217;m creating the test results object as follows:

<pre class="brush: java; gutter: false">List&lt;Map&lt;String,Object&gt;&gt; testCases = new ArrayList&lt;Map&lt;String, Object&gt;&gt;();

@AfterMethod
public void createResults(ITestResult result) {
		
	Map&lt;String, Object&gt; testCase = new HashMap&lt;String, Object&gt;();
		
	Map&lt;String, String&gt; parameters = new HashMap&lt;String, String&gt;();
		
	Object[] methodParams = result.getParameters();
		
	if(methodParams.length &gt; 0){
		parameters.put("circuit",methodParams[0].toString());
		parameters.put("country",methodParams[1].toString());
	}
		
	// Add test case name, description and the suite it belongs to
	testCase.put("name", result.getMethod().getMethodName());
	testCase.put("desc", result.getMethod().getDescription());
	testCase.put("params",parameters);
	testCase.put("suite", "RestAssuredDemoSuite");
				
	// Determine test result and add it, along with the error message in case of failure
	if (result.getStatus() == ITestResult.SUCCESS) {
		testCase.put("result", "pass");
	} else {
		testCase.put("result", "fail");
		testCase.put("reason", result.getThrowable().getMessage());
	}
		
	testCases.add(testCase);
}</pre>

So, after each test, I add the result of that test to the results object, together with additional information regarding that test. And yes, I know the above code is suboptimal (especially with regards to the parameter processing), but it works for this example.

After all tests have been executed, the test results can be uploaded to the Tesults platform with another couple of lines of code:

<pre class="brush: java; gutter: false">@AfterClass
public void uploadResults() {
		
	Map&lt;String,Object&gt; uploadResults = new HashMap&lt;String,Object&gt;();
	uploadResults.put("cases", testCases);
		
	Map&lt;String,Object&gt; uploadData = new HashMap&lt;String,Object&gt;();
	uploadData.put("target", TOKEN);
	uploadData.put("results", uploadResults);
		
	Map&lt;String, Object&gt; uploadedOK = Results.upload(uploadData);
	System.out.println("SUCCESS: " + uploadedOK.get("success"));
	System.out.println("MESSAGE: " + uploadedOK.get("message"));
}</pre>

Note the use of the target string here. Again, this string associates a test results object with the right Tesults project and target. After test execution and uploading of the test results, we can visit the Tesults web site to see what our test results look like:

<a href="http://www.ontestautomation.com/?attachment_id=1761" rel="attachment wp-att-1761"><img src="http://www.ontestautomation.com/wp-content/uploads/2017/01/tesults_results_overview_new.png" alt="Tesults results overview" width="1007" height="760" class="aligncenter size-full wp-image-1761" srcset="https://www.ontestautomation.com/wp-content/uploads/2017/01/tesults_results_overview_new.png 1007w, https://www.ontestautomation.com/wp-content/uploads/2017/01/tesults_results_overview_new-300x226.png 300w, https://www.ontestautomation.com/wp-content/uploads/2017/01/tesults_results_overview_new-768x580.png 768w" sizes="(max-width: 1007px) 100vw, 1007px" /></a>

You can click on each test to see the individual results:

<a href="http://www.ontestautomation.com/?attachment_id=1758" rel="attachment wp-att-1758"><img src="http://www.ontestautomation.com/wp-content/uploads/2017/01/tesults_results_detail.png" alt="Tesults test result details" width="603" height="589" class="aligncenter size-full wp-image-1758" srcset="https://www.ontestautomation.com/wp-content/uploads/2017/01/tesults_results_detail.png 603w, https://www.ontestautomation.com/wp-content/uploads/2017/01/tesults_results_detail-300x293.png 300w" sizes="(max-width: 603px) 100vw, 603px" /></a>

In the time I&#8217;ve used it, Tesults has shown to provide an easy to use way of uploading and publishing test results to all parties involved, thereby giving essential insight into test results and product quality. From discussions with them, I learned that the Tesults team is also considering the option to allow users to upload a file corresponding with a test case. This can be used for example to attach a screenshot of the browser state whenever a user interface-driven test fails, or to include a log file for more efficient failure analysis.

While on the subject of support and communication, the support I&#8217;ve been receiving from the Tesults people has been excellent. I had some trouble getting Tesults to work, and they&#8217;ve been very helpful when the problem was on my side and absolutely lightning fast with fixing the issues that were surfacing in their product. I hope they can keep this up as the product and its user base grows!

Having said that, it should be noted that Tesults is still a product under development, so by the time you&#8217;re reading this post, features might have been added, other features might look different, and maybe some features will have been removed entirely. I won&#8217;t be updating this post for every new feature added, changed or removed. I suggest you take a look at the <a href="https://www.tesults.com/docs" target="_blank">Tesults documentation</a> for an overview of the latest features.

On a closing note, I&#8217;ve mentioned earlier in this post that Tesults is a commercially licensed solution with a <a href="https://www.tesults.com/#plans" target="_blank">subscription-based revenue model</a>. The Tesults team have told me that their main target markets is teams, projects and (smaller) organization of 10-15 up to around 100 people. For smaller teams that might not want to invest too heavily in a reporting solution, they are always open to discussing custom plans. In that case, you can contact them at enterprise@tesults.com. As I said, I&#8217;ve found the Tesults team to be extremely communicative, helpful and open to suggestions.

_Disclaimer: I am in no way, shape or form associated with, nor do I have a commercial interest in Tesults as an organization or a product. I AM, however and again, of the opinion that good reporting is a critical but overlooked factor in the success of test automation. In my opinion, Tesults is an option well worth considering, given the ease of integration and the way test results are published and made available to stakeholders. I&#8217;m confident that this will only improve with time, as key features are added on a very regular basis._