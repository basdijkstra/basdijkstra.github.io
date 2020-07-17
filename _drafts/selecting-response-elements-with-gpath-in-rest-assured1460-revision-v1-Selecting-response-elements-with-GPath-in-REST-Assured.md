---
id: 1464
title: Selecting response elements with GPath in REST Assured
date: 2016-05-27T12:06:24+02:00
author: Bas
layout: revision
guid: http://www.ontestautomation.com/1460-revision-v1/
permalink: /1460-revision-v1/
---
Hey, another post on REST Assured! This time I&#8217;d like to take a closer look at how you can select elements from a JSON or XML response to check their values. For this, REST Assured uses GPath, a path expression language integrated into the Groovy language. It is similar to XPath for XML, but GPath can handle both XML and JSON. This makes it an excellent fit for checking responses of RESTful web services. Read more on GPath <a href="http://groovy.jmiguel.eu/groovy.codehaus.org/GPath.html" target="_blank">here</a> or <a href="http://groovy-lang.org/processing-xml.html#_gpath" target="_blank">here</a>.

To see how GPath works and how you can use it effectively in REST Assured tests, let&#8217;s consider the following JSON response from the <a href="http://ergast.com/mrd/" target="_blank">Ergast MRD API</a>. This response lists all drivers for the 2016 Formula 1 season:

<a href="http://www.ontestautomation.com/?attachment_id=1461" rel="attachment wp-att-1461"><img src="http://www.ontestautomation.com/wp-content/uploads/2016/05/json_drivers_2016.png" alt="JSON response containing all Formula 1 drivers for the 2016 season" width="518" height="578" class="aligncenter size-full wp-image-1461" srcset="https://www.ontestautomation.com/wp-content/uploads/2016/05/json_drivers_2016.png 518w, https://www.ontestautomation.com/wp-content/uploads/2016/05/json_drivers_2016-269x300.png 269w" sizes="(max-width: 518px) 100vw, 518px" /></a>

You can see the full response by selecting (or otherwise performing a GET call to) <a href="http://ergast.com/api/f1/2016/drivers.json" target="_blank">this link</a>.

**Extracting a single element value**  
Say we want to extract the _driverId_ of the last driver and check that it is equal to _wehrlein_. Like with XPath, you can simply specify the path to the element starting with the root node and navigating through the tree until the required node is reached. Note that the index [-1] is used by GPath to denote the last matching element.

<pre class="brush: java; gutter: false">@Test
public void extractAndCheckSingleValue() {
		
	given().
	when().
		get("http://ergast.com/api/f1/2016/drivers.json").
	then().
		assertThat().
		body("MRData.DriverTable.Drivers.driverId[-1]",equalTo("wehrlein"));
}</pre>

**Extracting a set of element values**  
Another check we might want to perform is that the collection of driverId values contains some specific values. This is done using a GPath expression very similar to the previous example:

<pre class="brush: java; gutter: false">@Test
public void extractAndCheckMultipleValues() {
		
	given().
	when().
		get("http://ergast.com/api/f1/2016/drivers.json").
	then().
		assertThat().
		body("MRData.DriverTable.Drivers.driverId",hasItems("alonso","button"));
}</pre>

**Using the REST Assured base path**  
By now, you might have noticed that in every GPath expression we needed to start navigating from the root node MRData downwards. This results in quite long GPath expressions. REST Assured has a nifty little feature that can simplify these expressions by having you define a BasePath:

<pre class="brush: java; gutter: false">@BeforeTest
public void initPath() {
		
	RestAssured.rootPath = "MRData.DriverTable.Drivers";
}</pre>

This cleans up our tests and has a positive effect on maintainability:

<pre class="brush: java; gutter: false">@Test
public void extractAndCheckMultipleValues() {
		
	given().
	when().
		get("http://ergast.com/api/f1/2016/drivers.json").
	then().
		assertThat().
		body("driverId",hasItems("alonso","button"));
}</pre>

**Extracting a specific subset of values**  
GPath also supports array slicing to retrieve specific subsets of a collection of values. In the example below, we check that the collection of elements with index [0] through [2] has exactly three items (the ones at index [0], [1] and [2]):

<pre class="brush: java; gutter: false">@Test
public void extractAndCheckArraySliceSize() {
		
	given().
	when().
		get("http://ergast.com/api/f1/2016/drivers.json").
	then().
		assertThat().
		body("driverId[0..2]",hasSize(3));
}</pre>

**Filtering values**  
Using GPath, you can also filter values to return an even more specific subset of values. For example, if we only want to return the collection of _permanentNumber_ values in use this year that are between 20 and 30 inclusive, we can do this:

<pre class="brush: java; gutter: false">@Test
public void extractAndCheckRange() {
		
	given().
	when().
		get("http://ergast.com/api/f1/2016/drivers.json").
	then().
		assertThat().
		body("findAll{Drivers-&gt;Drivers.permanentNumber &gt;= \"20\" && Drivers.permanentNumber &lt;= \"30\"}.permanentNumber",hasItem("22")).
		and().
		body("findAll{Drivers-&gt;Drivers.permanentNumber &gt;= \"20\" && Drivers.permanentNumber &lt;= \"30\"}.permanentNumber",not(hasItem("33")));
}</pre>

The quotes around the lower and upper boundary are required since the API call returns the _permanentNumber_ values as strings instead of integers.

**Use parameters**  
To make the previous example a little more flexible, we can parameterize both the lower and upper boundaries as well as the values that are expected to be (or not to be) in the resulting collection. Let&#8217;s specify these in a TestNG _DataProvider_ first:

<pre class="brush: java; gutter: false">@DataProvider(name = "rangesAndValues")
public String[][] createTestDataObject() {
		
	return new String[][] {
		{"20","30","22","25"},
		{"30","40","33","31"},
		{"1","9","9","4"}
	};
}</pre>

Now we can apply these in our test:

<pre class="brush: java; gutter: false">@Test(dataProvider = "rangesAndValues")
public void extractAndCheckRangeParameterized(String lowerLimit, String upperLimit, String inCollection, String notInCollection) {
		
	given().
	when().
		get(getDriverListFor2016).
	then().		
		assertThat().
		body("findAll{Drivers-&gt;Drivers.permanentNumber &gt;= \"" + lowerLimit + "\" && Drivers.permanentNumber &lt;= \"" + upperLimit + "\"}.permanentNumber",hasItem(inCollection)).
		and().
		body("findAll{Drivers-&gt;Drivers.permanentNumber &gt;= \"" + lowerLimit + "\" && Drivers.permanentNumber &lt;= \"" + upperLimit + "\"}.permanentNumber",not(hasItem(notInCollection)));
}</pre>

Note that the syntax gets a little messy here, especially since we have to keep the escaped double quotes in the GPath expression. There might be an easier way to do this, but I haven&#8217;t found one that still supports the _given()/when()/then()_ format. Any pointers on this are well appreciated!

**Sample code**  
You can download a Maven project containing all the code examples presented in this blog post <a href="http://www.ontestautomation.com/files/RestAssuredGPath.zip" target="_blank">here</a>.