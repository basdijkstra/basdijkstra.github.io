---
id: 885
title: FindBy strategies for Selenium explained
date: 2015-05-20T10:32:39+02:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=885
permalink: /findby-strategies-for-selenium-explained/
categories:
  - Selenium
tags:
  - findby
  - page object design
  - pagefactory
  - selenium webdriver
---
The _@FindBy_ annotation is used in Page Objects in Selenium tests to specify the object location strategy for a WebElement or a list of WebElements. Using the PageFactory, these WebElements are usually initialized when a Page Object is created. In this post, I will demonstrate various ways in which you can use _@FindBy_ annotations to efficiently locate (groups of) WebElements.

**_@FindBy_**  
The @FindBy annotation is used to locate one or more WebElements using a single criterion. For example, to identify all elements that have the same _class_ attribute, we could use the following identification:

<pre class="brush: java; gutter: false">@FindBy(how = How.CLASS_NAME, using = "classname")
private List&lt;WebElement&gt; singlecriterion;</pre>

If we are sure there is only a single element that is identified by our location strategy, for example when we use the element ID, we can also directly assign the result to a WebElement variable:

<pre class="brush: java; gutter: false">@FindBy(how = How.ID, using = "elementid")
private WebElement element;</pre>

To instantiate the elements, we call the _initElements_ method of the _PageFactory_ class:

<pre class="brush: java; gutter: false">PageFactory.initElements(driver, this);</pre>

**_@FindBys_ and _@FindAll_**  
In some cases we want (or need) to use more than a single criterion to identify one or more objects, for instance when page elements do not have a unique ID. In this case, there are two possible annotations that can be used:

  * The _@FindBys_ annotation is used in case elements need to match **all of the given criteria**
  * The _@FindAll_ annotation is used in case elements need to match **at least one of the given criteria**

Let&#8217;s take a look at an example that illustrates the difference between the two.

The <a href="http://parabank.parasoft.com/parabank/index.htm" target="_blank">Parabank homepage</a> contains two textboxes, one for the username and one for the password. Both elements have a _name_ attribute that we are going to use to identify them within a Page Object.

Using _@FindBys_:

<pre class="brush: java; gutter: false">@FindBys({
	@FindBy(how = How.NAME, using = "username"),
	@FindBy(how = How.NAME, using = "password")
})
private List&lt;WebElement&gt; bothcriteria;</pre>

The _bothcriteria_ list should contain 0 elements, as there is no element that has both a _name_ attribute with the value _username_ and a _name_ attribute with the value _password_.

Using _@FindAll_:

<pre class="brush: java; gutter: false">@FindAll({
	@FindBy(how = How.NAME, using = "username"),
	@FindBy(how = How.NAME, using = "password")
})
private List&lt;WebElement&gt; eithercriterion;</pre>

The _eithercriterion_ list should contain 2 elements, as there is one element that has a _name_ attribute with the value _username_ and also one that has a _name_ attribute with the value _password_.

For validation purposes, if we print the number of results found by all of the above strategies using

<pre class="brush: java; gutter: false">System.out.println("Using @FindBy, we found " + singlecriterion.size() + " element(s)");
System.out.println("Using @FindBys, we found " + bothcriteria.size() + " element(s)");
System.out.println("Using @FindAll, we found " + eithercriterion.size() + " element(s)");</pre>

we see this:  
[<img src="http://www.ontestautomation.com/wp-content/uploads/2015/05/findby_results.png" alt="Results for different FindBy strategies" width="265" height="47" class="aligncenter size-full wp-image-893" />](http://www.ontestautomation.com/wp-content/uploads/2015/05/findby_results.png)It clearly works exactly as expected!

**A more verbose _FindBy_**  
Finally, if you have a lot of elements within your Page Object, you can also use a more verbose way of specifying your _@FindBy_ strategy. For example

<pre class="brush: java; gutter: false">@FindBy(className = "classname")</pre>

gives the exact same results as

<pre class="brush: java; gutter: false">@FindBy(how = How.CLASS_NAME, using = "classname")</pre>