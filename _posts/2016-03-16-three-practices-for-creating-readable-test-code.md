---
id: 1326
title: Three practices for creating readable test code
date: 2016-03-16T07:00:53+01:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=1326
permalink: /three-practices-for-creating-readable-test-code/
categories:
  - General test automation
tags:
  - assertj
  - java
  - readable code
  - rest-assured
---
You&#8217;ve probably read before that writing code that is readable and easy to understand is considered good practice for any software developer. Readable code takes less time to understand, to hand over to your colleagues or clients and to update if needs be. Since writing automated checks is essentially just another form of software development (<a href="http://www.kohl.ca/2005/test-automation-is-software-development/" target="_blank">this was true in 2005</a> and it sure hasn&#8217;t changed since then), it makes sense to apply the &#8216;write readable code&#8217; practice there as well. But how do you go about doing that? In this post, I would like to introduce three practices that are guaranteed to have a positive impact on the readability of your test code.

**Practice: Use fluent assertions libraries**  
Some other fans of readable code with far better development skills than yours truly have created some useful libraries that allow you to write assertions in a style that is almost equal to natural languauge, so-called fluent assertions. Two examples of these fluent assertion libraries for Java are <a href="http://joel-costigliola.github.io/assertj/" target="_blank">AssertJ</a> and <a href="http://google.github.io/truth/" target="_blank">Truth</a>.

Let&#8217;s take AssertJ as an example. Consider the following TestNG assertions:

<pre class="brush: java; gutter: false">assertEquals(4, calculator.add(2,2));

assertTrue(muppets.hasMember("Kermit"));

assertEquals(0, mammals.getAnimalsByType("fish").size());</pre>

When converted to AssertJ assertions, these look like:

<pre class="brush: java; gutter: false">assertThat(calculator.add(2,2)).isEqualTo(4);

assertThat(muppets.hasMember("Kermit")).isTrue();

assertThat(mammals.getAnimalsByType("fish")).isEmpty();</pre>

The exact same checks are being executed, but the AssertJ assertions are the better readable ones in my opinion, since you can read them from the left to the right without having to jump back in order to understand what the assertion does. AssertJ has many more features for improving the readability of your test code, so be sure to check it out. The library also comes with a <a href="https://github.com/joel-costigliola/assertj-core/blob/master/src/main/scripts/convert-testng-assertions-to-assertj.sh" target="_blank">script</a> that automatically converts your TestNG assertions to AssertJ assertions for you.

**Practice: Have Page Object methods return Page Objects**  
This practice applies mostly to Selenium WebDriver code, although I can imagine there are other uses for it as well. When you use the <a href="http://www.ontestautomation.com/using-the-page-object-model-pattern-in-selenium-testng-tests/" target="_blank">Page Object model</a> in your code, you can easily improve the readability of your tests by assigning Page Object return types to all methods in your Page Object. Each method represents a specific user action that is to be performed on the page, and the type of Page Object that is returned depends on the page where the user will end up after that specific action is performed. For example, a simple _LoginPage_ object could look like this:

<pre class="brush: java; gutter: false">public class LoginPage {
	
	private WebDriver driver;
	
	@FindBy(id="txtFieldUsername")
	private WebElement txtFieldUsername;
	
	@FindBy(id="txtFieldPassword")
	private WebElement txtFieldPassword;
	
	@FindBy(id="btnLogin")
	private WebElement btnLogin;
	
	public LoginPage(WebDriver driver) {
		
		this.driver = driver;
		
		if(!driver.getTitle().equals("Login Page")) {
			driver.get("http://example.com/login");
		}
		
		PageFactory.initElements(driver, this);
	}
	
	public LoginPage setUsername(String username) {
		
		txtFieldUsername.sendKeys(username);
		return this;
	}
	
	public LoginPage setPassword(String password) {
		
		txtFieldPassword.sendKeys(password);
		return this;
	}
	
	public HomePage clickLoginButton() {
		
		btnLogin.click();
		return new HomePage(driver);
	}
}</pre>

Note that this Page Object also uses the <a href="https://selenium.googlecode.com/git/docs/api/java/org/openqa/selenium/support/PageFactory.html" target="_blank">PageFactory</a> pattern, which improves Page Object code readability as well. This pattern is beyond the scope of this blog post, though.

Assuming we also have a _HomePage_ object with a method that returns a boolean indicating we are indeed on the home page, we can write a simple login test that looks like this:

<pre class="brush: java; gutter: false">@Test
public void loginTest() {
		
	new LoginPage(driver)
		.setUsername("myUser")
		.setPassword("myPassword")
		.clickLoginButton();
		
	Assert.assertTrue(new HomePage(driver).isAt(), "Home page has been loaded successfully after login action");
}</pre>

You can instantly see what this test does, due to the fact that all of our Page Object methods return a specific type of Page Object. This enables us to chain methods from either the same or different Page Objects (as long as their return types match up) and create this type of readable tests.

**Practice: Employ a Given/When/Then structure for your tests wherever possible**  
Another way to improve the readability of your test code is to structure your tests following the Given/When/Then format that is often used in Behaviour Driven Development (BDD). This applies even when you&#8217;re not doing BDD, by the way. Just think of it like this:

  * Given: set up test data and other preconditions
  * When: perform a specific action
  * Then: check the results

Some test tools support this format explicitly, for example <a href="http://rest-assured.io" target="_blank">REST Assured</a>:

<pre class="brush: java; gutter: false">@Test
public void testMD5() {
		
	given().
		parameters("text", "test").
	when().
		get("http://md5.jsontest.com").
	then().
		body("md5",equalTo("098f6bcd4621d373cade4e832627b4f6")).
	and().
		body("original", equalTo("test"));
}</pre>

For other tools, you may need to apply this implicitly:

<pre class="brush: java; gutter: false">@Test
public void testCalculatorAddition() {
		
	//Given
	Calculator calculator = new Calculator();
	
	//When
	calculator.add(2);
		
	//Then
	assertEquals(calculator.getResult(), 2);		
}</pre>

An additional benefit of writing your tests in this way is that it makes it much easier to discuss them with people without any substantial programming background. Most people will recognize what a test does if you break them down in this way, even when they lack programming knowledge.

I hope there&#8217;s at least something that you can take away from this post and apply it to your existing test suite in order to make it more readable. You&#8217;ll be doing yourself, your clients and your fellow team members a huge favour.