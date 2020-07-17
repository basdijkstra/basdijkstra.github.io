---
id: 588
title: Defining test steps and object properties in an Excel Data Source for Selenium
date: 2014-08-27T08:15:52+02:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=588
permalink: /defining-test-steps-and-object-properties-in-an-excel-data-source-for-selenium/
categories:
  - Selenium
tags:
  - data driven testing
  - keyword driven testing
  - microsoft excel
  - reusability
  - selenium webdriver
---
Recently, one of the readers of this blog sent me an email asking whether it was possible to define not only input and validation parameters in an external data source (such as an Excel file), but also the test steps to be taken and the object properties on which these steps need to be performed by Selenium WebDriver. As it happens, I have been playing around with this idea for a while in the past, but until now I&#8217;ve never gotten round to writing a blog post on it.

**The Excel Data Source**  
As an example, we are going to perform a search query on Google and validate the number of results we get from this search query. This scripts consists of the following steps:

  1. Open a new browser instance
  2. Navigate to the Google homepage
  3. Type the search query in the text box
  4. Click on the search button
  5. Validate the contents of the web element displaying the number of search results against our expected value
  6. Close the browser instance

For each step, we are going to define (if applicable):

  * A keyword identifying the action to be taken
  * A parameter value that is used in the action (note that for example a type action takes a parameter, a click action does not)
  * The attribute type that uniquely identifies the object on which the action is performed
  * The corresponding attribute value

For our example test script, the Excel Data Source to be used could look something like this:  
[<img class="aligncenter size-full wp-image-592" src="http://www.ontestautomation.com/wp-content/uploads/2014/08/data_source.png" alt="Excel Data Source" width="604" height="165" srcset="https://www.ontestautomation.com/wp-content/uploads/2014/08/data_source.png 604w, https://www.ontestautomation.com/wp-content/uploads/2014/08/data_source-300x81.png 300w" sizes="(max-width: 604px) 100vw, 604px" />](http://www.ontestautomation.com/wp-content/uploads/2014/08/data_source.png)  
**Reading the Data Source**  
Similar to the concept explained in [a previous post](http://www.ontestautomation.com/data-driven-testing-in-selenium-webdriver-using-excel/ "Data driven testing in Selenium Webdriver using Excel"), first we need to read the values from the Excel sheet in order to execute the test steps defined. We need to be a little extra careful here as some cells might be empty and the methods we use do not particularly like reading values from cells that do not exist.

<pre class="brush: java; gutter: false">public static void main (String args[]) {

		String action = "";
		String value = "";
		String attribute = "";
		String attrval = "";
		
		try {
			// Open the Excel file for reading
			FileInputStream fis = new FileInputStream("C:\\Tools\\testscript.xls");
			// Open it for writing too
			FileOutputStream fos = new FileOutputStream("C:\\Tools\\testscript.xls");
			// Access the required test data sheet
			HSSFWorkbook wb = new HSSFWorkbook(fis);
			HSSFSheet sheet = wb.getSheet("steps");
			// Loop through all rows in the sheet
			// Start at row 1 as row 0 is our header row
			for(int count = 1;count&lt;=sheet.getLastRowNum();count++){
				HSSFRow row = sheet.getRow(count);
				System.out.println("Running test step " + row.getCell(0).toString());

				// Run the test step for the current test data row
				if(!(row.getCell(1) == null || row.getCell(1).equals(Cell.CELL_TYPE_BLANK))) {
					action = row.getCell(1).toString();
				} else {
					action = "";
				}

				if(!(row.getCell(2) == null || row.getCell(2).equals(Cell.CELL_TYPE_BLANK))) {
					value = row.getCell(2).toString();
				} else {
					value = "";
				}

				if(!(row.getCell(3) == null || row.getCell(3).equals(Cell.CELL_TYPE_BLANK))) {
					attribute = row.getCell(3).toString();
				} else {
					attribute = "";
				}

				if(!(row.getCell(4) == null || row.getCell(4).equals(Cell.CELL_TYPE_BLANK))) {
					attrval = row.getCell(4).toString();
				} else {
					attrval = "";
				}

				System.out.println("Test action: " + action);
				System.out.println("Parameter value: " + value);
				System.out.println("Attribute: " + attribute);
				System.out.println("Attribute value: " + attrval);
				
				String result = runTestStep(action,value,attribute,attrval);
				
				// Write the result back to the Excel sheet
				row.createCell(5).setCellValue(result);
				
			}
			
			// Save the Excel sheet and close the file streams
			wb.write(fos);
			fis.close();
			fos.close();
			
		} catch (Exception e) {
			System.out.println(e.toString());
		}
}</pre>

We do not read the value from the Result column, but rather we are going to execute the test steps using the values from the other columns, determine the result and write this back to the Excel sheet. In this way, we have a rudimentary logging function built in directly into our framework. Neat, right?

**Defining and executing test steps**  
Next, we need to implement the generic runTestStep method we use to execute the test steps we have defined in our Data Source. This can be done pretty straightforward by looking at the current Action keyword and then executing the necessary steps for that keyword.

<pre class="brush: java; gutter: false">public static String runTestStep(String action, String value, String attribute, String attrval) throws Exception {

		switch(action.toLowerCase()) {
		case "openbrowser":
			switch(value.toLowerCase()) {
			case "firefox":
				driver = new FirefoxDriver();
				driver.manage().timeouts().implicitlyWait(10, TimeUnit.SECONDS);
				return "OK";
			case "htmlunit":
				driver = new HtmlUnitDriver();
				driver.manage().timeouts().implicitlyWait(10, TimeUnit.SECONDS);
				return "OK";
			default:
				return "NOK";
			}
		case "navigate":
			driver.get(value);
			return "OK";
		case "type":
			try {
				WebElement element = findMyElement(attribute,attrval);
				element.sendKeys(value);
				return "OK";
			} catch (Exception e) {
				System.out.println(e.toString());
				return "NOK";
			}
		case "click":
			try {
				WebElement element = findMyElement(attribute,attrval);
				element.click();
				return "OK";
			} catch (Exception e) {
				System.out.println(e.toString());
				return "NOK";
			}
		case "validate":
			try {
				WebElement element = findMyElement(attribute,attrval);
				if (element.getText().equals(value)) {
					return "OK";
				} else {
					System.out.println("Actual value: " + element.getText() + ", expected value: " + value);
					return "NOK";
				}
			} catch (Exception e) {
				System.out.println(e.toString());
			}
		case "closebrowser":
			driver.quit();
			return "OK";
		default:
			throw new Exception("Unknown keyword " + action);
		}
}</pre>

This setup makes it very easy to add other keywords (e.g., for selecting a value from a dropdown list), both for standardized browser actions as well as for custom keywords. Every step returns a result (here either _OK_ or _NOK_) to indicate whether the action has been executed successfully. This is written back to the Excel sheet in our main method, so we can see whether our test steps have been executed correctly. 

**Finding the required object**  
I&#8217;ve also used a helper method findMyElement that, given an attribute and an attribute value, returns a WebElement that corresponds to these values. Its implementation is quite rudimentary, but it should be easy for you to extend it and make it more fail safe. I&#8217;m lazy sometimes. I haven&#8217;t even implemented all types of selectors that Selenium can handle!

<pre class="brush: java; gutter: false">public static WebElement findMyElement(String attribute, String attrval) throws Exception {

		switch(attribute.toLowerCase()) {
		case "id":
			return driver.findElement(By.id(attrval));
		case "name":
			return driver.findElement(By.name(attrval));
		case "xpath":
			return driver.findElement(By.xpath(attrval));
		case "css-select":
			return driver.findElement(By.cssSelector(attrval));
		default:
			throw new Exception("Unknown selector type " + attribute);
		}
}</pre>

Now, when I run the test, my code nicely reads all rows from Excel, determines what test action needs to be performed, executes it and reports the result back to Excel. After running the test, the Excel sheet looks like this:  
[<img src="http://www.ontestautomation.com/wp-content/uploads/2014/08/data_source_results.png" alt="Excel Data Source including test results" width="604" height="165" class="aligncenter size-full wp-image-603" srcset="https://www.ontestautomation.com/wp-content/uploads/2014/08/data_source_results.png 604w, https://www.ontestautomation.com/wp-content/uploads/2014/08/data_source_results-300x81.png 300w" sizes="(max-width: 604px) 100vw, 604px" />](http://www.ontestautomation.com/wp-content/uploads/2014/08/data_source_results.png)  
The only test step that fails does so because it Google doesn&#8217;t always return the same number of test results in the same amount of time, obviously. Nevertheless, it illustrates the added value of writing back the result of every step to the Excel sheet. Of course, you can very easily extend this to include a useful error message and even [a link to a screenshot](http://www.ontestautomation.com/how-to-create-screenshots-in-your-selenium-webdriver-tests/ "How to create screenshots in your Selenium Webdriver tests").

The Eclipse project containing the code I&#8217;ve used to create this example can be downloaded [here](http://www.ontestautomation.com/files/seleniumTestStepsAndObjectsInExcel.zip).