---
id: 298
title: Building and using an Object Repository in Selenium Webdriver
date: 2014-04-01T09:49:00+02:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=298
permalink: /building-and-using-an-object-repository-in-selenium-webdriver/
categories:
  - Selenium
tags:
  - java
  - maintainability
  - object repository
  - selenium webdriver
---
One of the main burdens of automated GUI test script maintainability is the amount of maintenance needed when object properties change within the application under test. A very common way of minimizing the time it takes to update your automated test scripts is the use of a central object repository (or object map as it&#8217;s also referred to sometimes). A basic object repository can be implemented as a collection of key-value pairs, with the key being a logical name identifying the object and the value containing unique objects properties used to identify the object on a screen.

Selenium Webdriver offers no object repository implementation by default. However, implementing and using a basic object repository is pretty straightforward. In this article, I will show you how to do it and how to lighten the burden of test script maintenance in this way.

Note that all code samples below are written in Java. However, the object repository concept as explained here can be used with your language of choice just as easily.

**Creating the object repository**  
First, we are going to create a basic object repository and fill it with some objects that we will use in our test script. In this article, I am going to model a very basic scenario: go to the Bing search engine, search for a particular search query and determine the number of search results returned by Bing. To execute this scenario, our script needs to manipulate three screen objects:

  * The textbox where the search string is typed
  * The search button to be clicked in order to submit the search query
  * The text field that displays the number of search results

Our object map will a simple .properties text file that we add to our Selenium project:  
[<img src="http://www.ontestautomation.com/wp-content/uploads/2014/04/object_map_file1.png" alt="Our object map" width="315" height="110" class="aligncenter size-full wp-image-301" srcset="https://www.ontestautomation.com/wp-content/uploads/2014/04/object_map_file1.png 315w, https://www.ontestautomation.com/wp-content/uploads/2014/04/object_map_file1-300x104.png 300w" sizes="(max-width: 315px) 100vw, 315px" />](http://www.ontestautomation.com/wp-content/uploads/2014/04/object_map_file1.png)The key for each object, for example _bing.homepage.textbox_, is a logical name for the object that we will use in our script. The corresponding value consists of two parts: the attribute type used for uniquely identifying the object on screen and the corresponding attribute value. For example, the aforementioned text box is uniquely identified by its _id_ attribute, which has the value _sb\_form\_q_.

**Retrieving objects from the object repository**  
To retrieve objects from our newly created object map, we will define an _ObjectMap_ with a constructor taking a single argument, which is the path to the .properties file:

<pre class="brush: java; gutter: false; first-line: 1; highlight: []; html-script: false">public class ObjectMap {
	
	Properties prop;
	
	public ObjectMap (String strPath) {
		
		prop = new Properties();
		
		try {
			FileInputStream fis = new FileInputStream(strPath);
			prop.load(fis);
			fis.close();
		}catch (IOException e) {
			System.out.println(e.getMessage());
		}
	}</pre>

The class contains a single method _getLocator_, which returns a _By_ object that is used by the Selenium browser driver object (such as a _HtmlUnitDriver_ or a _FirefoxDriver_):

<pre class="brush: java; gutter: false; first-line: 1; highlight: []; html-script: false">public By getLocator(String strElement) throws Exception {
		
		// retrieve the specified object from the object list
		String locator = prop.getProperty(strElement);
		
		// extract the locator type and value from the object
		String locatorType = locator.split(":")[0];
		String locatorValue = locator.split(":")[1];
		
		// for testing and debugging purposes
		System.out.println("Retrieving object of type &#039;" + locatorType + "&#039; and value &#039;" + locatorValue + "&#039; from the object map");
		
		// return a instance of the By class based on the type of the locator
		// this By can be used by the browser object in the actual test
		if(locatorType.toLowerCase().equals("id"))
			return By.id(locatorValue);
		else if(locatorType.toLowerCase().equals("name"))
			return By.name(locatorValue);
		else if((locatorType.toLowerCase().equals("classname")) || (locatorType.toLowerCase().equals("class")))
			return By.className(locatorValue);
		else if((locatorType.toLowerCase().equals("tagname")) || (locatorType.toLowerCase().equals("tag")))
			return By.className(locatorValue);
		else if((locatorType.toLowerCase().equals("linktext")) || (locatorType.toLowerCase().equals("link")))
			return By.linkText(locatorValue);
		else if(locatorType.toLowerCase().equals("partiallinktext"))
			return By.partialLinkText(locatorValue);
		else if((locatorType.toLowerCase().equals("cssselector")) || (locatorType.toLowerCase().equals("css")))
			return By.cssSelector(locatorValue);
		else if(locatorType.toLowerCase().equals("xpath"))
			return By.xpath(locatorValue);
		else
			throw new Exception("Unknown locator type &#039;" + locatorType + "&#039;");
	}</pre>

As you can see, objects can be identified using a number of different properties, including object IDs, CSS selectors and XPath expressions.

**Using objects in your test script**  
Now that we can retrieve objects from our object map, we can use these in our scripts to execute the desired scenario:

<pre class="brush: java; gutter: false; first-line: 1; highlight: []; html-script: false">public static void main (String args[]) {

		// Create a new instance of the object map
		ObjectMap objMap = new ObjectMap("objectmap.properties");

		// Start a browser driver and navigate to Google
		WebDriver driver = new HtmlUnitDriver();
        driver.get("http://www.bing.com");

        // Execute our test
        try {
        	
        	// Retrieve search text box from object map and type search query
        	WebElement element = driver.findElement(objMap.getLocator("bing.homepage.textbox"));
			element.sendKeys("Alfa Romeo");
			
			// Retrieve search button from object map and click it
			element = driver.findElement(objMap.getLocator("bing.homepage.searchbutton"));
			element.click();
			
			// Retrieve number of search results using results object from object map
			element = driver.findElement(objMap.getLocator("bing.resultspage.results"));
			System.out.println("Search result string: " + element.getText());
			
			// Verify page title
			Assert.assertEquals(driver.getTitle(), "Alfa Romeo - Bing");
			
		} catch (Exception e) {
			System.out.println("Error during test execution:\n" + e.toString());
		}
        
	}</pre>

You can see from this code sample that using an object from the object map in your test is as easy as referring to its logical name (i.e., the key in our object map).

**Object repository maintenance**  
With this straightforward mechanism we have been able to vastly reduce the amount of time needed for script maintenance in case object properties change. All it takes is an update of the appropriate entries in the object map and we&#8217;re good to go and run our tests again.

Thanks to [Selenium Master](http://seleniummaster.com/sitecontent/index.php/selenium-web-driver-menu/selenium-test-automation-with-java/170-using-object-map-in-selenium-web-driver-automation) for explaining this concept clearly for me to apply.

An example Eclipse project using the pattern described above can be downloaded [here](http://www.ontestautomation.com/files/seleniumFramework.zip).