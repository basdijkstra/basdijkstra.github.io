---
id: 211
title: A very basic web service test tool
date: 2013-11-04T12:32:31+01:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=211
permalink: /a-very-basic-web-service-test-tool/
categories:
  - Web service testing
tags:
  - java
  - web services
---
For those of us involved in testing web services and SOA-based applications, there are lots of different test tools on the market that can help speed up the testing and make them repeatable and easy to maintain. Some examples of these tools are SoapUI from SmartBear and SOAtest from Parasoft. SoapUI is available both as a freeware product and as an enterprise edition offering additional functionality. SOAtest is only available in a paid version.

One of the biggest downsides of the freeware version of SoapUI is the lack of data driven test support. This feature is only available in the paid version. In [a previous post](http://www.ontestautomation.com/data-driven-testing-in-selenium-webdriver-using-excel/) I introduced a simple way of implementing data driven testing in Selenium Webdriver tests, using Microsoft Excel sheets as the test data container format. As I am very regularly involved in web service testing myself, and often want to use data driven testing to create maintainable and flexible test suites, I thought it would be a good idea to see whether it&#8217;s possible to quickly create a very basic web service testing tool that supports data driven testing.

**The test data source**  
As in the previous example, let&#8217;s create a test data source first. I chose to base the solution presented here on predefined XML request message files as this saves us the trouble of creating XML objects in our code. There are obvious downsides to this, of course, but for now we will focus on the ability to call a web service and subsequently capture and validate the result.

Our test data source looks like this:

[<img src="http://www.ontestautomation.com/wp-content/uploads/2013/11/webservice_test_data_source.png" alt="webservice_test_data_source" width="500" class="aligncenter size-medium wp-image-212" srcset="https://www.ontestautomation.com/wp-content/uploads/2013/11/webservice_test_data_source.png 885w, https://www.ontestautomation.com/wp-content/uploads/2013/11/webservice_test_data_source-300x34.png 300w" sizes="(max-width: 885px) 100vw, 885px" />](http://www.ontestautomation.com/wp-content/uploads/2013/11/webservice_test_data_source.png)

It contains columns to identify the current test case, the path to the XML file containing the SOAP message to be sent and the endpoint to which the message should be sent. The last two columns contain the name of an element in the web service response we would like to validate and its expected value, respectively.

**Reading our test data source**  
In our very basic web service test tool, we will process this Excel sheet just as we did in the [Selenium example](http://www.ontestautomation.com/data-driven-testing-in-selenium-webdriver-using-excel/) posted earlier. For every test data row, we will then execute the following steps:

  * Create a SOAP request from the XML file
  * Send the SOAP request to the right web service endpoint and capture the response
  * Extract the value from a response message element and compare it to the expected value

**Create a SOAP request from the XML file**  
This is an easy step, as all we need to do is open the file, read all of its contents and transform it into a SOAP message object:

<pre class="brush: java; gutter: false; first-line: 1; highlight: []; html-script: false">private static SOAPMessage createSOAPRequest(String strPath) throws Exception {
        
    // Create a SOAP message from the XML file located at the given path
    FileInputStream fis = new FileInputStream(new File(strPath));
    MessageFactory factory = MessageFactory.newInstance();
    SOAPMessage message = factory.createMessage(new MimeHeaders(), fis);
    return message;
}</pre>

**Send the SOAP request to the right web service and capture the response**  
This is fairly straightforward as well and can be done using standard Java methods:

<pre class="brush: java; gutter: false; first-line: 1; highlight: []; html-script: false">private static SOAPMessage getSOAPResponse(SOAPMessage soapRequest, String strEndpoint) throws Exception, SOAPException {
    	
    // Send the SOAP request to the given endpoint and return the corresponding response
    SOAPConnectionFactory soapConnectionFactory = SOAPConnectionFactory.newInstance();
    SOAPConnection soapConnection = soapConnectionFactory.createConnection();
    SOAPMessage soapResponse = soapConnection.call(soapRequest, strEndpoint);
    return soapResponse;	
}</pre>

**Validating elements from the response message**  
Finally, we will parse the response message and validate the value of one of its elements. The validation result is sent to the stdout:

<pre class="brush: java; gutter: false; first-line: 1; highlight: []; html-script: false">private static void validateValue(SOAPMessage soapMsg, String strEl, String strExpected) throws Exception {
    	
    // Get all elements with the requested element tag from the SOAP message
    SOAPBody soapBody = soapMsg.getSOAPBody();
    NodeList elements = soapBody.getElementsByTagName(strEl);
        
    // Check whether there is exactly one element with the given tag
    if (elements.getLength() != 1){
        System.out.println("Expected exactly one element " + strEl + "in message, but found " + Integer.toString(elements.getLength()));
    } else {
        // Validate the element value against the expected value
        String strActual = elements.item(0).getTextContent();
        if (strActual.equals(strExpected)) {
        	System.out.println("Actual value " + strActual + " for element " + strEl + " matches expected value");
        } else {
        	System.out.println("Expected value " + strExpected + " for element " + strEl + ", but found " + strActual + " instead");
        }
    }
}</pre>

**Running the test**  
When we run the test, we see that the web service to be tested is called three times. Two tests succeed, the last test case fails (on purpose, to show that the validation is executed properly):

[<img src="http://www.ontestautomation.com/wp-content/uploads/2013/11/webservice_console_output.png" alt="webservice_console_output" width="500" class="aligncenter size-medium wp-image-219" srcset="https://www.ontestautomation.com/wp-content/uploads/2013/11/webservice_console_output.png 514w, https://www.ontestautomation.com/wp-content/uploads/2013/11/webservice_console_output-300x51.png 300w" sizes="(max-width: 514px) 100vw, 514px" />](http://www.ontestautomation.com/wp-content/uploads/2013/11/webservice_console_output.png)

**Extensions to our tool**  
As stated earlier in this post, there are a lot of improvements to be made to this very basic web service test tool. For instance, we could add:

  * Dynamic request message generation based on a template and data source values
  * XSD validation for the response messages
  * Support for non-SOAP-based (or plain XML) web services or even for transport protocols other than HTTP
  * &#8230;

Some of these improvements will probably be featured in later articles at [ontestautomation.com](http://www.ontestautomation.com). For questions or suggestions on topics to be covered on this blog, please do not hesitate to contact me at bas AT ontestautomation.com or leave a reply through the comment form below.

An example Eclipse project using the pattern described above can be downloaded [here](http://www.ontestautomation.com/files/WebServiceTester.zip).