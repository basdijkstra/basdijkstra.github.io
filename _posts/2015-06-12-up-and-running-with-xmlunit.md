---
id: 914
title: 'Up and running with: XMLUnit'
date: 2015-06-12T11:09:38+02:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=914
permalink: /up-and-running-with-xmlunit/
categories:
  - Test automation tools
tags:
  - java
  - up and running
  - xml
  - xmlunit
---
_This is the sixth article in our series on new, popular or otherwise interesting tools used in test automation. You can read all posts within this series by clicking [here](http://www.ontestautomation.com/tag/up-and-running/)._

**What is XMLUnit?**  
From the <a href="http://www.xmlunit.org" target="_blank">XMLUnit.org</a> website: XMLUnit provides you with the tools to verify the XML you emit is the one you want to create. It provides helpers to validate against an XML Schema, assert the values of XPath queries or compare XML documents against expected outcomes. The most important part is a diff-engine that provides you with full control over what kind of difference is important to you and which part of the generated document to compare with which part of your reference document.

**Where can I get XMLUnit?**  
XMLUnit can be downloaded from <a href="https://github.com/xmlunit/xmlunit" target="_blank">the XMLUnit GitHub site</a>. Snapshot binaries for XMLUnit 2.0 (the version that is covered in this post) can also be obtained from <a href="https://oss.sonatype.org/content/repositories/snapshots/org/xmlunit/" target="_blank">the Maven repository</a>.

**How do I install and configure XMLUnit?**  
Installing XMLUnit is as simple as downloading the latest xmlunit-core snapshot version and adding it as a dependency to your Java project.

**Creating a first XMLUnittest**  
Let&#8217;s start with a very simple test that validates the value of a specific element of a predefined XML message:

<pre class="brush: java; gutter: false">@Test
public void aFirstTest() {
		
	Source source = Input.fromString("&lt;foo&gt;bar&lt;/foo&gt;").build();
	XPathEngine xpath = new JAXPXPathEngine();
	String content = xpath.evaluate("/foo/text()", source);
	Assert.assertEquals(content, "bar");
}</pre>

Note that I have created this XMLUnit test as a [TestNG](http://www.ontestautomation.com/up-and-running-with-testng/) test to make running the tests easier and to have TestNG automatically generate a test report.

**Useful features**  
Using XMLUnit, XML validations can not only be done based on predefined strings containing XML, but also directly on the XML result of a call to a REST service. This is done in a very similar way:

<pre class="brush: java; gutter: false">@Test
public void restCallTest() {
		
	Source source = Input.fromURI("http://parabank.parasoft.com/parabank/services/bank/customers/12212").build();
	XPathEngine xpath = new JAXPXPathEngine();
	String content = xpath.evaluate("//city/text()", source);
	Assert.assertEquals(content, "Beverly Hills");
}</pre>

The only thing that is changed is the use of _Input.fromURI_ instead of _Input.fromString_. Similar methods are available to perform XPath-based validations directly from an _InputStream_, a _File_, or a _Node_ object, amongst others.

XMLUnit can not only perform element-based validation, it can also do a full compare of two XML messages. In this example, the XML returned by the same REST service call used in the previous example is compared against a predefined XML messages stored on the local file system (as _response.xml_ in the _messages_ subdirectory):

<pre class="brush: java; gutter: false">@Test
public void compareTest() {
		
	DiffBuilder db = DiffBuilder.compare(Input.fromFile("messages/response.xml")).withTest(Input.fromURI("http://parabank.parasoft.com/parabank/services/bank/customers/12212").build());
	Diff d = db.build();
		
	if(d.hasDifferences()) {
		Iterator&lt;Difference&gt; diffs = d.getDifferences().iterator();
		while(diffs.hasNext()) {
			Reporter.log(diffs.next().toString());
		}
	}
		
	Assert.assertEquals(d.hasDifferences(), false);
}</pre>

Any differences between the two XML files are written to the TestNG report file using the default _Reporter_:

[<img src="http://www.ontestautomation.com/wp-content/uploads/2015/06/xmlunit_differences_testng.png" alt="XMLUnit differences written to a TestNG report" width="1511" height="365" class="aligncenter size-full wp-image-917" srcset="https://www.ontestautomation.com/wp-content/uploads/2015/06/xmlunit_differences_testng.png 1511w, https://www.ontestautomation.com/wp-content/uploads/2015/06/xmlunit_differences_testng-300x72.png 300w, https://www.ontestautomation.com/wp-content/uploads/2015/06/xmlunit_differences_testng-1024x247.png 1024w" sizes="(max-width: 1511px) 100vw, 1511px" />](http://www.ontestautomation.com/wp-content/uploads/2015/06/xmlunit_differences_testng.png)

Finally, XMLUnit can also validate XML messages against an XSD document:

<pre class="brush: java; gutter: false">@Test
public void validationTest() {
		
	Validator v = Validator.forLanguage(Languages.W3C_XML_SCHEMA_NS_URI);
	v.setSchemaSources(Input.fromFile("messages/orderschema.xsd").build());
	ValidationResult result = v.validateInstance(Input.fromFile("messages/order.xml").build());
		
	Iterator&lt;ValidationProblem&gt; problems = result.getProblems().iterator();
		
	while (problems.hasNext()) {
		Reporter.log(problems.next().toString());
	}
		
	Assert.assertEquals(result.isValid(), true);
}</pre>

Again, any issues that occur during schema validation can be written to the TestNG report using the default _Reporter_:

[<img src="http://www.ontestautomation.com/wp-content/uploads/2015/06/xmlunit_schema_issues_testng.png" alt="XMLUnit schema issues in TestNG report" width="1716" height="364" class="aligncenter size-full wp-image-919" srcset="https://www.ontestautomation.com/wp-content/uploads/2015/06/xmlunit_schema_issues_testng.png 1716w, https://www.ontestautomation.com/wp-content/uploads/2015/06/xmlunit_schema_issues_testng-300x64.png 300w, https://www.ontestautomation.com/wp-content/uploads/2015/06/xmlunit_schema_issues_testng-1024x217.png 1024w" sizes="(max-width: 1716px) 100vw, 1716px" />](http://www.ontestautomation.com/wp-content/uploads/2015/06/xmlunit_schema_issues_testng.png)

**Further reading**  
An Eclipse project including the tests I&#8217;ve demonstrated above and the reports that have been generated can be downloaded [here](http://www.ontestautomation.com/files/XmlUnit.zip).