---
id: 1613
title: How (not) to test RESTful APIs with Selenium WebDriver
date: 2016-09-29T20:54:56+02:00
author: Bas
layout: revision
guid: http://www.ontestautomation.com/1605-revision-v1/
permalink: /1605-revision-v1/
---
I&#8217;ve seen the question of how to do RESTful API testing with Selenium WebDriver come up a lot in various places. I&#8217;ve seen it mainly on LinkedIn, but a couple of weeks ago I was asked this question in person as well. So, as your ever helpful consultant, I thought I&#8217;d be a good idea to show you how it&#8217;s done.

First, let&#8217;s create a new browser driver object. For the sake of simplicity, I&#8217;ll use Firefox, because it doesn&#8217;t require setting up a separate driver. And yes, I know implicit waits aren&#8217;t the best waiting strategy, but I didn&#8217;t feel like writing an _ExpectedCondition_.

<pre class="brush: java; gutter: false">driver = new FirefoxDriver();
driver.manage().timeouts().implicitlyWait(10, TimeUnit.SECONDS);</pre>

Now, instead of browsing to a web page, we&#8217;ll simply browse to the RESTful API endpoint for which we want to check the response. I&#8217;ll use _api.zippopotamus.us_ as an example here to look up some data for the US zip code 90210:

<pre class="brush: java; gutter: false">driver.get("http://api.zippopotam.us/us/90210");</pre>

This gives us the following output:

<a href="http://www.ontestautomation.com/how-not-to-test-restful-apis-with-selenium-webdriver/api_json_response/" rel="attachment wp-att-1606"><img src="http://www.ontestautomation.com/wp-content/uploads/2016/09/api_json_response.png" alt="Response from the zippopotam.us API in JSON format" width="1622" height="70" class="aligncenter size-full wp-image-1606" srcset="https://www.ontestautomation.com/wp-content/uploads/2016/09/api_json_response.png 1622w, https://www.ontestautomation.com/wp-content/uploads/2016/09/api_json_response-300x13.png 300w, https://www.ontestautomation.com/wp-content/uploads/2016/09/api_json_response-768x33.png 768w, https://www.ontestautomation.com/wp-content/uploads/2016/09/api_json_response-1024x44.png 1024w" sizes="(max-width: 1622px) 100vw, 1622px" /></a>

Let&#8217;s grab the text we see on screen and convert it to a JSON object (using the _org.json_ package):

<pre class="brush: java; gutter: false">WebElement element = driver.findElement(By.xpath("//pre"));
JSONObject jsonObject = new JSONObject(element.getText());</pre>

Now that we have a JSON object containing the API response, we can extract specific elements and check their value:

<pre class="brush: java; gutter: false">String valueToCheck = jsonObject.get("country").toString();
Assert.assertEquals(valueToCheck, "United States");</pre>

As a final step, throw everything away, never do this again and please forget everything you&#8217;ve seen so far in this post. Selenium is not an API testing tool. It has never been, and it will never be. So please don&#8217;t try and force it to be.

Please stick to simulating UI interaction when using Selenium and use a dedicated, fit for purpose tool for API testing. As an example, <a href="http://rest-assured.io/" target="_blank">REST Assured</a> can perform the exact same check as the above. With a single line of code. Which is far better readable to boot:

<pre class="brush: java; gutter: false">@Test
public void doRestTestProperly() {
		
	given().
	when().
		get("http://api.zippopotam.us/us/90210").
	then().
		assertThat().
		body("country", equalTo("United States"));
}</pre>

So, here&#8217;s to hoping I did my part in exterminating questions that feature &#8216;Selenium&#8217; and &#8216;API testing&#8217; in the same sentence.