---
id: 189
title: Data driven testing in Selenium Webdriver using Excel
date: 2013-11-01T10:35:18+01:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=189
permalink: /data-driven-testing-in-selenium-webdriver-using-excel/
categories:
  - Selenium
tags:
  - data driven testing
  - microsoft excel
  - selenium webdriver
---
Most commercial automated software tools on the market support some sort of data driven testing, which allows you to automatically run a test case multiple times with different input and validation values. As Selenium Webdriver is more an automated testing framework than a ready-to-use tool, you will have to put in some effort to support data driven testing in your automated tests. In this article, I will show you one way of implementing data driven testing in Selenium. There are lots of different approaches possible, and I am aware that the solution presented here can possible be enhanced further enhanced and extended as well, but it will set you off in the right direction when you want to implement data driven testing in your own tests.

**The test data source**  
Before we dive into the implementation in Selenium, let&#8217;s first look at the test data source we are going to use to store our input and validation values. As it is widely used in the testing world for test script and test data administration, I usually prefer to use Microsoft Excel as the format for storing my parameters. An additional advantage of using Excel is that you can easily outsource the test data administration to someone other than yourself, someone who might have better knowledge of the test cases that need to be run and the parameters required to execute them.

In the example presented here, I have used a very simple Excel sheet containing a single input parameter (_SearchString_ &#8211; a Google search string) and a single validation parameter (_PageTitle_ &#8211; the page title displayed by the browser after the search has been executed). Yes, we are performing a pretty trivial test case, but it is sufficient to demonstrate the principle behind the solution presented here.

[<img class="aligncenter size-medium wp-image-190" alt="data_driven_test_data_source" src="http://www.ontestautomation.com/wp-content/uploads/2013/10/data_driven_test_data_source-300x86.png" width="300" height="86" srcset="https://www.ontestautomation.com/wp-content/uploads/2013/10/data_driven_test_data_source-300x86.png 300w, https://www.ontestautomation.com/wp-content/uploads/2013/10/data_driven_test_data_source.png 357w" sizes="(max-width: 300px) 100vw, 300px" />](http://www.ontestautomation.com/wp-content/uploads/2013/10/data_driven_test_data_source.png)

**Reading data from the test data source**  
Next, we need a way to open this Excel sheet and read data from it within our Selenium test script. For this purpose, I use the Apache POI library, which allows you to read, create and edit Microsoft Office-documents using Java. The library, as well as its JavaDoc, can be found at <http://poi.apache.org>. The classes and methods we are going to use to read data from our Excel sheet are located in the _org.apache.poi.hssf.usermodel_ package.

A simple _main_ method, where we loop through all rows in the Excel sheet containing test data and call the actual test method using the current test data, could look like this:

<pre class="brush: java; gutter: false; first-line: 1; highlight: []; html-script: false">public static void main (String args[]) {

	try {
		// Open the Excel file
		FileInputStream fis = new FileInputStream("Z:\\Documents\\Bas\\blog\\datasources\\testdata.xls");
		// Access the required test data sheet
		HSSFWorkbook wb = new HSSFWorkbook(fis);
		HSSFSheet sheet = wb.getSheet("testdata");
		// Loop through all rows in the sheet
		// Start at row 1 as row 0 is our header row
		for(int count = 1;count&lt;=sheet.getLastRowNum();count++){
			HSSFRow row = sheet.getRow(count);
			System.out.println("Running test case " + row.getCell(0).toString());
			// Run the test for the current test data row
			runTest(row.getCell(1).toString(),row.getCell(2).toString());
		}
		fis.close();
	} catch (IOException e) {
		System.out.println("Test data file not found");
	}	
}</pre>

Simple, yet effective. Of course, many enhancements or improvements can be made to this example, such as:

  * The path to the test data file can be passed as an argument to the main method, or the user can be allowed to select a test data sheet, for example using JFileChooser
  * The fact that the first row contains column headers can be made optional, for example by passing a Boolean argument to our method
  * Instead of using column indexes as we have done here (in the _getCell_ method, we could use column headers and have our code determine the correct column index for a given column at runtime

**Executing our sample test**  
The actual test we are going to execute is located in the _runTest_ method, which is called from the _main_ method for every test data row in our sheet. It starts a browser driver, in this case a </em>HtmlUnitDriver</em>, executes a Google query using the query from the Excel sheet and checks the page title to see whether it matches the expected value. The code is pretty straightforward:

<pre class="brush: java; gutter: false; first-line: 1; highlight: []; html-script: false">public static void runTest(String strSearchString, String strPageTitle) {
		
		// Start a browser driver and navigate to Google
		WebDriver driver = new HtmlUnitDriver();
        driver.get("http://www.google.com");

        // Enter the search string and send it
        WebElement element = driver.findElement(By.name("q"));
        element.sendKeys(strSearchString);
        element.submit();
        
        // Check the title of the page
        if (driver.getTitle().equals(strPageTitle)) {
        	System.out.println("Page title is " + strPageTitle + ", as expected");
        } else {
        	System.out.println("Expected page title was " + strPageTitle + ", but was " + driver.getTitle() + " instead");
        }
        
        //Close the browser
        driver.quit();
}</pre>

When we run this test, we can see in our stdout that the test script is executed three times, once for each row in the test data sheet:  
[<img src="http://www.ontestautomation.com/wp-content/uploads/2013/11/data_driven_console_output.png" alt="data_driven_console_output" class="aligncenter size-medium wp-image-199" srcset="https://www.ontestautomation.com/wp-content/uploads/2013/11/data_driven_console_output.png 636w, https://www.ontestautomation.com/wp-content/uploads/2013/11/data_driven_console_output-300x42.png 300w" sizes="(max-width: 636px) 100vw, 636px" />](http://www.ontestautomation.com/wp-content/uploads/2013/11/data_driven_console_output.png)Adding or changing test cases is now as easy as editing the Excel sheet associated with our test. As long as nothing is changed in the location or the order of the columns, no maintenance is required in our Selenium script.