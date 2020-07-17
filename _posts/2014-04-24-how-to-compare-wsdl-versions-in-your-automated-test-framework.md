---
id: 383
title: How to compare WSDL versions in your automated test framework
date: 2014-04-24T09:30:32+02:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=383
permalink: /how-to-compare-wsdl-versions-in-your-automated-test-framework/
categories:
  - Web service testing
tags:
  - compare
  - java
  - wsdl
---
A colleague of mine posed an interesting question last week. He had a test setup with three different machines on which his application under test was installed and deployed. He wanted to make sure in his test that the web service interface offered by these deployments was exactly the same by comparing the WSDLs associated with each installation. However, the tool he used (Parasoft SOAtest) only supports regression testing of single WSDL instances, i.e., it can validate whether a certain WSDL has been changed over time, but it cannot compare two or more different WSDL instances.

Luckily, SOAtest supports extension of its functionality using scripting, and I found a nice Java API that would do exactly what he asked. In this post, I&#8217;ll show you how this is done in Java. In SOAtest, I did it with a Jython script that imported and used the appropriate Java classes, but apart from syntax the solution is the same.

The Java API I used can be found [here](http://www.membrane-soa.org/soa-model/). The piece of code that executes the comparison is very straightforward:

<pre class="brush: java; gutter: false; first-line: 1; highlight: []; html-script: false">private static void compareWSDL(){
		
	// configure the log4j logger
	BasicConfigurator.configure();
		
	// create a new wsdl parser
	WSDLParser parser = new WSDLParser();

	// parse both wsdl documents
	Definitions wsdl1 = parser.parse("Z:\\Documents\\Bas\\wsdlcompare\\ParaBank.wsdl");
	Definitions wsdl2 = parser.parse("Z:\\Documents\\Bas\\wsdlcompare\\ParaBank2.wsdl");

	// compare the wsdl documents
	WsdlDiffGenerator diffGen = new WsdlDiffGenerator(wsdl1, wsdl2);
	List&lt;Difference&gt; lst = diffGen.compare();
		
	// write the differences to the console
	for (Difference diff : lst) {
		System.out.println(diff.dump());
	}
}</pre>

For this example, I used two locally stored copies of a WSDL document where I changed the definition of a single element (I removed the _minOccurs=&#8221;0&#8243;_ attribute). The API uses Log4J as the logging engine, so we need to initialize that in our code and add a _log4j.properties_ file to our project:  
[<img src="http://www.ontestautomation.com/wp-content/uploads/2014/04/log4j_properties.png" alt="Log4J properties file" width="524" height="167" class="aligncenter size-full wp-image-386" srcset="https://www.ontestautomation.com/wp-content/uploads/2014/04/log4j_properties.png 524w, https://www.ontestautomation.com/wp-content/uploads/2014/04/log4j_properties-300x95.png 300w" sizes="(max-width: 524px) 100vw, 524px" />](http://www.ontestautomation.com/wp-content/uploads/2014/04/log4j_properties.png)  
When we run our code, we can see that the WSDL documents are compared successfully, and that the difference I injected by hand is detected nicely by the WSDL compare tool:  
[<img src="http://www.ontestautomation.com/wp-content/uploads/2014/04/wsdl_compare_output.png" alt="Console output for the WSDL comparison" width="600" height="180" class="aligncenter size-full wp-image-387" srcset="https://www.ontestautomation.com/wp-content/uploads/2014/04/wsdl_compare_output.png 600w, https://www.ontestautomation.com/wp-content/uploads/2014/04/wsdl_compare_output-300x90.png 300w" sizes="(max-width: 600px) 100vw, 600px" />](http://www.ontestautomation.com/wp-content/uploads/2014/04/wsdl_compare_output.png)  
A nice and clean answer to yet another automated testing question, just as it should be.

An example Eclipse project using the pattern described above can be downloaded [here](http://www.ontestautomation.com/files/WSDLcompare.zip).