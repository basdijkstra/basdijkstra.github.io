---
id: 1830
title: How I would approach creating automated user interface-driven tests
date: 2017-03-28T08:54:47+02:00
author: Bas
layout: revision
guid: http://www.ontestautomation.com/1827-revision-v1/
permalink: /1827-revision-v1/
---
One of the best, yet also most time-consuming parts of running a blog is answering questions from readers. Questions are asked here on the site (through comments), but also frequently via email. I love interacting with you, my readers, and I&#8217;ll do whatever I can to help you, but there&#8217;s one thing even better than answering questions: preventing them. Specifically, I get a lot of questions asking me how I&#8217;d approach creating a &#8216;framework&#8217; (<a href="http://www.ontestautomation.com/do-you-want-a-framework-or-a-solution/" target="_blank">I prefer &#8216;solution&#8217;</a>, but more on that at the end of this post) for automated end-to-end user interface-driven tests, or checks, as they should be called.

Also, I get a lot of questions related to blog posts I wrote a year or more ago, of which I do no longer support the solution or approach I wrote about at the time. I&#8217;ve put up an &#8216;old post&#8217; notice on some of them (example <a href="http://www.ontestautomation.com/create-your-own-html-report-from-selenium-tests/)" target="_blank">here</a>), but that doesn&#8217;t prevent readers from asking questions on that subject. Which is fine, but sometimes a little frustrating.

As a means of providing you all with the answers to some of the most frequently asked questions, I&#8217;ve spent some time creating a blueprint for a solution for automated user interface-driven tests for you to look at. It illustrates how I usually approach creating these tests, and how I deal with things like test specification and reporting. The entire solution is available for your reference <a href="https://github.com/basdijkstra/ota-solution" target="_blank">on my GitHub page</a>. Feel free to copy, share, read and maybe learn something from it.

Since I&#8217;m a strong believer in <a href="http://www.ontestautomation.com/on-asking-why-in-test-automation/" target="_blank">answering the &#8216;why?&#8217; in test automation as well as the &#8216;how?&#8217;</a>, I&#8217;ll explain my design and tool choices in this post too. That does not mean that you should simply copy my choices, or that they&#8217;re the right choices in the first place, but it might give you a little insight into the thought process behind them.

**Tools included**  
First of all, the language. Even though almost all the technical posts I&#8217;ve written on this blog so far have covered Java tools, this solution is written in C#. Why? Because I&#8217;ve come to like the .NET ecosystem over the last year and a half, and because doing so gives me a chance to broaden my skill set a little more. Don&#8217;t worry, Java and C# are pretty close to one another, especially on this level, so creating a similar setup in Java shouldn&#8217;t be a problem for you.

Now then, the tools:

  * For browser automation, I&#8217;ve decided to go with the obvious option: Selenium WebDriver. Why? Because it&#8217;s what I know. Also, Selenium has been the go-to standard for browser automation for a number of years now, and it doesn&#8217;t look like that&#8217;s going to change anytime soon.
  * For test and test data specification, I&#8217;ve chosen <a href="http://specflow.org/" target="_blank">SpecFlow</a>, which is basically Cucumber for .NET. Why? Because it gives you a means of describing what your tests do in a business readable format. Also, it comes with native support for data driven testing (through scenario outlines), which is among the first features I look for when evaluating any test automation tool. So, as you can see, even if you&#8217;re not doing BDD (which I don&#8217;t), using SpecFlow has its benefits. I prefer using SpecFlow (or Cucumber for that matter) over approaches such as <a href="http://www.ontestautomation.com/defining-test-steps-and-object-properties-in-an-excel-data-source-for-selenium/" target="_blank">specifying your tests in Excel</a>. Dealing with workbooks, sheets, rows and cells is just too cumbersome.
  * For running tests and performing assertions, I decided to go with <a href="http://nunit.org" target="_blank">NUnit</a>. Why? It&#8217;s basically the .NET sibling of JUnit for Java, and it integrates very nicely with SpecFlow. I have no real experience with alternatives (such as MSTest), so it&#8217;s not really a well-argumented decision, but it works perfectly, and that&#8217;s what counts.
  * Finally, for reporting, I&#8217;ve added <a href="http://extentreports.com/" target="_blank">ExtentReports</a>. Why? If you&#8217;ve been reading this blog for a while, you know that I&#8217;m a big fan of this tool, because it creates great-looking HTML reports with just a couple of lines of code. It has built-in support for including screenshots as well, which makes it perfectly suited for the user interface-driven tests I want to perform. Much better and easier than <a href="http://www.ontestautomation.com/create-your-own-html-report-from-selenium-tests/" target="_blank">trying to build your own reports</a>.

**Top level: SpecFlow feature and scenarios**  
Since we&#8217;re using SpecFlow here, a test is specified as a number of scenarios written in the Gherkin Given-When-Then format. These scenarios are grouped together in features, with every feature describing a specific piece of functionality or behavior of our application under test. I&#8217;m running my example tests against the Parasoft demo application <a href="http://parabank.parasoft.com/parabank/index.htm" target="_blank">ParaBank</a>, and the feature I&#8217;m using tests the login function of this application:

<pre class="brush: text; gutter: false">Feature: Login
	In order to access restricted site options
	As a registered ParaBank user
	I want to be able to log in

Scenario Outline: Login using valid credentials
	Given I have a registered user &lt;firstname&gt; with username &lt;username&gt; and password &lt;password&gt;
	And he is on the ParaBank home page
	When he logs in using his credentials
	Then he should land on the Accounts Overview page
	Examples:
	| firstname | username | password |
	| John      | john     | demo     |
	| Bob       | parasoft | demo     |
	| Alex      | alex     | demo     |

Scenario:  Login using incorrect password
	Given I have a registered user John with username john and password demo
	And he is on the ParaBank home page
	When he logs in using an invalid password
	Then he should see an error message stating that the login request was denied</pre>

**Level 2: Step definitions**  
Not only should features and scenarios be business readable, I think it&#8217;s a good idea that your automation code is as clean and as readable as possible too. A step definition, which is basically the implementation of a single step (line) in a scenario, should convey what it does through the use of fluent code and descriptive method names. As an example, here&#8217;s the implementation of the _When he logs in using his credentials_ step:

<pre class="brush: csharp; gutter: false">[When(@"he logs in using his credentials")]
public void WhenHeLogsInUsingHisCredentials()
{
    User user = ScenarioContext.Current.Get&lt;User&gt;();

    new LoginPage().
        Load().
        SetUsername(user.Username).
        SetPassword(user.Password).
        ClickLoginButton();
}</pre>

Quite readable, right? I retrieve a previously stored object of type User (the SpecFlow ScenarioContext and FeatureContext are really useful for this purpose) and use its Username and Password properties to perform a login action on the LoginPage. Voila: fluent code, clear method names and therefore readable, understandable and maintainable code.

**Level 3: Page Objects**  
I&#8217;m using the <a href="http://www.seleniumhq.org/docs/06_test_design_considerations.jsp#page-object-design-pattern" target="_blank">Page Object pattern</a> for reusability purposes. Every action I can perform on the page (setting the username, setting the password, etc.) has its own method. Furthermore, I have each method return a Page Object, which allows me to write the fluent code we&#8217;ve seen in the previous step. This is not always possible, though. The _ClickLoginButton_ method, for example, does not return a Page Object, since clicking the button might redirect the user to either of two different pages: the Accounts Overview page (in case of a successful login) or the Login Error page (in case something goes wrong). Here&#8217;s the implementation of the _SetUsername_ and _SetPassword_ methods:

<pre class="brush: csharp; gutter: false">public LoginPage SetUsername(string username)
{
    OTAElements.SendKeys(_driver, textfieldUsername, username);
    return this;
}

public LoginPage SetPassword(string password)
{
    OTAElements.SendKeys(_driver, textfieldPassword, password);
    return this;
}</pre>

In the next step, we&#8217;ll see how I&#8217;ve implemented the _SendKeys_ method as well as the reasoning behind using this wrapper method instead of calling the Selenium API directly. First, let&#8217;s take a look at how I identified objects. A commonly used method for this is the <a href="https://github.com/SeleniumHQ/selenium/wiki/PageFactory" target="_blank">PageFactory</a> pattern, but I chose not to use that. It&#8217;s not that useful to me, plus I don&#8217;t particularly like having to use two lines and possibly an additional third, blank line (for readability) for each element on a page. Instead, I simply declare the appropriate By locator at the top of the page so that I can pass it to my wrapper method:

<pre class="brush: csharp; gutter: false">private By textfieldUsername = By.Name("username");
private By textfieldPassword = By.Name("password");
private By buttonLogin = By.XPath("//input[@value=&#039;Log In&#039;]");</pre>

In this example, I also used the <a href="http://www.ontestautomation.com/using-the-loadablecomponent-pattern-for-better-page-object-handling-in-selenium/" target="_blank">LoadableComponent pattern</a> for better page loading and error handling. Again, whether or not you use it is entirely up to you, but I think it can be a very useful pattern in some cases. I don&#8217;t use it in my current project though, since there&#8217;s no need for it (yet). Feel free to copy it, or leave it out. Whatever works best for you.

**Level 4: Wrapper methods**  
As I said above, I prefer writing <a href="http://www.ontestautomation.com/using-wrapper-methods-for-better-error-handling-in-selenium/" target="_blank">wrapper methods</a> around the Selenium API, instead of calling Selenium methods directly in my Page Objects. Why? Because in that way, I can do all error handling and additional logging in a single place, instead of having to think about exceptions and reporting in each individual page object method. In the example above, you could see I created a class _OTAElements_, which conveniently contains wrapper methods for individual element manipulation. My _SendKeys_ wrapper implementation looks like this, for example:

<pre class="brush: csharp; gutter: false">public static void SendKeys(IWebDriver driver, By by, string valueToType, bool inputValidation = false)
{
    try
    {
        new WebDriverWait(driver, TimeSpan.FromSeconds(Constants.DefaultTimeout)).Until(ExpectedConditions.ElementIsVisible(by));
        driver.FindElement(by).Clear();
        driver.FindElement(by).SendKeys(valueToType);
    }
    catch (Exception ex) when (ex is NoSuchElementException || ex is WebDriverTimeoutException)
    {
        ExtentTest test = ScenarioContext.Current.Get&lt;ExtentTest&gt;();
        test.Error("Could not perform SendKeys on element identified by " + by.ToString() + " after " + Constants.DefaultTimeout.ToString() + " seconds", MediaEntityBuilder.CreateScreenCaptureFromPath(ReportingMethods.CreateScreenshot(driver)).Build());
        Assert.Fail();
    }
    catch (Exception ex) when (ex is StaleElementReferenceException)
    {
        // find element again and retry
        new WebDriverWait(driver, TimeSpan.FromSeconds(Constants.DefaultTimeout)).Until(ExpectedConditions.ElementIsVisible(by));
        driver.FindElement(by).Clear();
        driver.FindElement(by).SendKeys(valueToType);
    }
}</pre>

These wrapper methods are a little more complex, but this allows me to keep the rest of the code squeaky clean. And those other parts (the page objects, the step definitions) is where 95% of the maintenance happens once the wrapper method implementation is finished. All exception handling and additional logging (more on that soon) is done inside the wrapper, but once you&#8217;ve got that covered, creating additional page objects and step definitions is really, really straightforward.

I have also created wrapper methods for the various checks performed in my tests, for exactly the same reasons: keeping the code as clean as possible and perform additional logging when desired:

<pre class="brush: csharp; gutter: false">public static void AssertTrue(IWebDriver driver, ExtentTest extentTest, bool assertedValue, string reportingMessage)
{
    try
    {
        Assert.IsTrue(assertedValue);
        extentTest.Pass(reportingMessage);
    }
    catch (AssertionException)
    {
        extentTest.Fail("Failure occurred when executing check &#039;" + reportingMessage + "&#039;", MediaEntityBuilder.CreateScreenCaptureFromPath(ReportingMethods.CreateScreenshot(driver)).Build());
        throw;
    }
}</pre>

**Reporting**  
Even though NUnit produces its own test reports, I&#8217;ve fallen in love with <a href="http://extentreports.com/" target="_blank">ExtentReports</a> a while ago, and since then I&#8217;ve been using it in a lot of different projects. It integrates seamlessly with the solution setup described above, it&#8217;s highly configurable and its reports just look good. Especially the ability to include screenshots is a blessing for the user interface-driven tests we&#8217;re performing here. I&#8217;ve also included (through the _LogTraceListener_ class, which is called in the project&#8217;s _App.config_) the option of adding the current SpecFlow scenario step to the ExtentReports report. This provides a valuable link between test results and test (or rather, application behavior) specification. You can see the report generated by executing the Login feature <a href="http://www.ontestautomation.com/files/Login.html" target="_blank">here</a>.

**Extending the solution**  
In the weeks to come, I&#8217;ll probably add some tweaks and improvements to the solution described here. One major thing I&#8217;d like to add is including an example of testing a RESTful API. So far, I haven&#8217;t found a really elegant solution for C# yet. I&#8217;m looking for something similar to what <a href="http://rest-assured.io" target="_blank">REST Assured</a> does in Java. I&#8217;ve tried <a href="http://restsharp.org/" target="_blank">RestSharp</a>, but that&#8217;s a generic HTTP client, not a library written for testing, resulting in a little less readable code if you do want to use it for writing tests. I guess I&#8217;ll have to keep looking, and any hints and tips are highly welcome!

I&#8217;d also like to add some more complex test scenarios, to show you how this solution evolves when the number of features, scenarios, step definitions and page objects grows, as they tend to do in real life projects. I&#8217;ve used pretty much the same setup in actual projects before, so I know it holds up well, but the proof of the pudding.. You know.

**Wrapping up**  
As I&#8217;ve said at the beginning of this post, I&#8217;ve created this project to show you how I would approach the task of creating a solution for clients that want to use automated user interface-driven tests as part of their testing process. Your opinions on how I implemented things might differ, but that&#8217;s OK. I&#8217;d love to hear suggestions on how things can be improved and extended. I&#8217;d like to stress that this code is not a framework. Again, I can&#8217;t stand that word. This approach has proven to be a solution to real world problems for real world clients in the past. And it continues to do so in projects I&#8217;m currently involved in.

I wrote this code to show you (and I admit, my potential clients too) how I would approach creating these tests. I don&#8217;t claim it&#8217;s the best solution. But it works for me, and it has worked for my clients.

Again, you can find the project <a href="https://github.com/basdijkstra/ota-solution" target="_blank">on my GitHub page</a>. I hope it&#8217;ll prove useful to you.