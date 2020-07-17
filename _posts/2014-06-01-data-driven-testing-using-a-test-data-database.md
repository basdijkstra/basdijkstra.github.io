---
id: 450
title: Data driven testing using a test data database
date: 2014-06-01T07:36:53+02:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=450
permalink: /data-driven-testing-using-a-test-data-database/
categories:
  - General test automation
  - Web service testing
tags:
  - data driven testing
  - database
  - RESTful
  - web services
---
In [a previous post](http://www.ontestautomation.com/data-driven-testing-in-selenium-webdriver-using-excel/ "Data driven testing in Selenium Webdriver using Excel") I explained how to set up a data driven test in Selenium Webdriver using data from an Excel worksheet. However, you might have your potential test data stored in a database rather than in Excel. In this post, I will show you how to set up and run a data driven test using data from a database. In this example, I will use a REST webservice as the object to be tested and therefore won&#8217;t use Selenium Webdriver, but you can easily apply this approach to your Selenium tests as well.

First, we need a table containing our test data. For this example, I have created a simple table in a local MySQL installation, containing the following data:

[<img src="http://www.ontestautomation.com/wp-content/uploads/2014/06/test_data_table.png" alt="The table containing our test data" width="191" height="112" class="aligncenter size-full wp-image-451" />](http://www.ontestautomation.com/wp-content/uploads/2014/06/test_data_table.png)

As our test object, I am going to use [a public REST service API](http://api.zippopotam.us/) that returns, amongst other data, the city and state corresponding to a US zip code in JSON format (click [here](http://api.zippopotam.us/us/47501) for an example).

Now, let&#8217;s create a test that calls this API for all zipcodes in our test data table and verify whether the city and state returned by the service match the expected values stored in our table.

To do that, we first need to create a connection to our database table, retrieve the test data from it and call our test method for every row in the table. This is done using the following piece of code:

<pre class="brush: java; gutter: false; first-line: 1; highlight: []; html-script: false">public static void runTest() {
		
	try {
			
		// Retrieve database connection properties from the properties file
		String driver = DBDrivenProperties.getProperty("db.driver");
		String dburl = DBDrivenProperties.getProperty("db.url");
		String dbname = DBDrivenProperties.getProperty("db.dbname");
		String dbquery = DBDrivenProperties.getProperty("db.query");
		String dbuser = DBDrivenProperties.getProperty("db.username");
		String dbpassword = DBDrivenProperties.getProperty("db.password");
			
		// Load the MySQL JDBC driver
		Class.forName(driver);
			
		// Create a connection to the MySQL database
		Connection conn = DriverManager.getConnection(dburl + dbname, dbuser, dbpassword);
			
		// Create a statement to be executed
		Statement stmt = conn.createStatement();
			
		// Execute the query
		ResultSet rs = stmt.executeQuery(dbquery);
			
		// Loop through the query results and run the REST service test for every row
		while (rs.next()) {
			String zipcode = rs.getString("zipcode");
			String city = rs.getString("city");
			String state = rs.getString("state");
			try {
				testService(zipcode,city,state);
			} catch (IOException | JSONException e) {
				System.out.println(e.toString());
			}
		}
			
		// Close the database connection
		conn.close();
			
	} catch (ClassNotFoundException | SQLException e) {
		System.out.println(e.toString());
	}		
}</pre>

For this method to run, we need to add a MySQL JDBC driver to the classpath of our project, otherwise we get an error when we try to load the driver. You can get yours [here](http://dev.mysql.com/downloads/connector/).

For clarity, I have put all the configuration data that is needed to connect to the database and get the results from it in a separate properties file that looks like this:

[<img src="http://www.ontestautomation.com/wp-content/uploads/2014/06/properties_file.png" alt="The properties file" width="320" height="135" class="aligncenter size-full wp-image-453" srcset="https://www.ontestautomation.com/wp-content/uploads/2014/06/properties_file.png 320w, https://www.ontestautomation.com/wp-content/uploads/2014/06/properties_file-300x126.png 300w" sizes="(max-width: 320px) 100vw, 320px" />](http://www.ontestautomation.com/wp-content/uploads/2014/06/properties_file.png)

You can find the code that I use to retrieve the property values from this file in the project files. See the end of this post for a link to it.

Now that we have retrieved our test data from the database, let&#8217;s write the actual test method. This is very similar to the one I used in [a previous post](http://www.ontestautomation.com/testing-restful-webservices/ "Testing RESTful webservices"):

<pre class="brush: java; gutter: false; first-line: 1; highlight: []; html-script: false">public static void testService(String zipcode, String city, String state) throws IOException, JSONException {
		
	System.out.println("Validating response for " + DBDrivenProperties.getProperty("rest.url") + zipcode + "...");
		
	// Retrieve the base URL for the REST service and append the zipcode parameter
	String restURL = DBDrivenProperties.getProperty("rest.url") + zipcode;
		
	// Call the REST service and store the response
	HttpUriRequest request = new HttpGet(restURL);
	HttpResponse httpResponse = HttpClientBuilder.create().build().execute(request);

	// Convert the response to a String format
	String result = EntityUtils.toString(httpResponse.getEntity());

	// Convert the result as a String to a JSON object
	JSONObject jo = new JSONObject(result);
		
	// Get the array containing the places that correspond to the requested zipcode
	JSONArray ja = jo.getJSONArray("places");
		
	// Assert that the values returned by the REST service match the expected values in our database
	Assert.assertEquals(city, ja.getJSONObject(0).getString("place name"));
	Assert.assertEquals(state, ja.getJSONObject(0).getString("state"));	
}</pre>

The only thing that is new compared to the code in my previous post on testing REST webservices is that in this response, the elements I am interested in (being the city and state corresponding to the zip code in the request) are stored within a result array called _places_. To retrieve these, I need to dig one level deeper into my JSON response object using the JSONArray object. Other than that, the test method is pretty straightforward.

One warning I need to address before you go ahead and create your own tests using test data from a database is that I used a very broad query in this example (a simple _SELECT * FROM table_). This potentially generates a lot of results and subsequently a lot of test iterations. Even though this gives great test coverage, it also takes more time to execute all test cases. Especially when you use this approach in combination with Selenium Webdriver, you might want to use a narrower query (or limit the number of results returned) to prevent your test from taking too long to finish.

The Eclipse project files including all code needed to get this to work can be downloaded [here](http://www.ontestautomation.com/files/dataDrivenDB.zip).