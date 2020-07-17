---
id: 2690
title: 'On no longer wanting to depend on ExpectedConditions in my C# Selenium WebDriver tests'
date: 2020-02-25T14:12:34+01:00
author: Bas
layout: revision
guid: https://www.ontestautomation.com/2688-revision-v1/
permalink: /2688-revision-v1/
---
Those of you that have been writing tests with Selenium in C# for a while might have noticed that as of version 3.11, some often-used things have been marked as deprecated, most notably the _PageFactory_ implementation and the _ExpectedConditions_ class. For those of you that have not read the motivation behind this, <a rel="noreferrer noopener" aria-label="here (opens in a new tab)" href="http://jimevansmusic.blogspot.com/2018/03/deprecating-parts-of-seleniums-net.html" target="_blank">here</a> is the rationale behind it, as written by Jim Evans, core contributor to the Selenium C# bindings.

These implementations have been moved to <a rel="noreferrer noopener" aria-label="a new location (opens in a new tab)" href="https://github.com/DotNetSeleniumTools/DotNetSeleniumExtras" target="_blank">a new location</a> and have been made available as separate NuGet packages (_DotNetSeleniumExtras.PageObjects_ and _DotNetSeleniumExtras.WaitHelpers_, respectively). As of the time of writing this blog post, however, there&#8217;s still no new maintainer for this GitHub repository, meaning that there&#8217;s no support available and no certainty as to its future.<figure class="wp-block-image size-full">

<img src="https://www.ontestautomation.com/wp-content/uploads/2020/02/deprecated.png" alt="Notice of ExpectedConditions class being deprecated, as visible in Visual Studio" class="wp-image-2689" srcset="https://www.ontestautomation.com/wp-content/uploads/2020/02/deprecated.png 1012w, https://www.ontestautomation.com/wp-content/uploads/2020/02/deprecated-300x32.png 300w, https://www.ontestautomation.com/wp-content/uploads/2020/02/deprecated-768x83.png 768w" sizes="(max-width: 1012px) 100vw, 1012px" /> </figure> 

And while that is no problem yet, since you can still use the deprecated classes from the Selenium bindings or use the _DotNetSeleniumExtras_ packages, I think it&#8217;s a risk to keep on relying on code that is effectively unsupported.

In my case, for _PageFactory_, that&#8217;s not a problem, since I don&#8217;t typically use it anyway. For _ExpectedConditions_, however, it&#8217;s a different story. When I&#8217;m writing and teaching Selenium automation, I&#8217;m pretty much still following the general approach I described a couple of years ago in [this post](https://www.ontestautomation.com/how-i-would-approach-creating-automated-user-interface-driven-tests/). It still works, it&#8217;s readable, easy to maintain and explain and has proven to be effective in many different projects I&#8217;ve worked on over the years. 

However, it also relies on the use of _ExpectedConditions_, and that&#8217;s something that is bothering me more and more often.

So, while preparing for a training session I delivered recently, I started looking for an alternative way of writing my [wrapper methods](https://www.ontestautomation.com/using-wrapper-methods-for-better-error-handling-in-selenium/) in C#. And it turns out that C# offers an elegant way of doing so, out of the box, in the form of lambda expressions.

Lambda expressions (also known as <a rel="noreferrer noopener" aria-label="anonymous functions (opens in a new tab)" href="https://en.wikipedia.org/wiki/Anonymous_function" target="_blank">anonymous functions</a>) is a function that&#8217;s not bound to an identifier, but instead can be passed as an argument to a higher-order function. The concept stems from functional programming (it has been a long while since I&#8217;ve done any of that, by the way) but has been part of C# for a while. By the way, lambda expressions are available in Java too, from Java 8 onwards, for those of you not working with C#.

Let&#8217;s look at an example. Here&#8217;s my implementation for a wrapper function around the default Selenium _SendKeys()_ method that takes care of synchronization and exception handling using _ExpectedConditions_:

<pre class="EnlighterJSRAW" data-enlighter-language="csharp" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">public void SendKeys(By by, string valueToType)
{
    try
    {
      new WebDriverWait(_driver, TimeSpan.FromSeconds(Constants.DEFAULT_TIMEOUT)).Until(ExpectedConditions.ElementToBeClickable(by));
        _driver.FindElement(by).Clear();
        _driver.FindElement(by).SendKeys(valueToType);
    }
    catch (Exception ex) when (ex is NoSuchElementException || ex is WebDriverTimeoutException)
    {
         Assert.Fail($"Exception occurred in SeleniumHelper.SendKeys(): element located by {by.ToString()} could not be located within {Constants.DEFAULT_TIMEOUT} seconds.");
    }
}</pre>

Here&#8217;s the same wrapper, but now passing a lambda expression of our own as an argument to the _Until()_ method instead of a method in _ExpectedConditions_:

<pre class="EnlighterJSRAW" data-enlighter-language="csharp" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">public void SendKeys(By by, string valueToType)
{
    WebDriverWait wait = new WebDriverWait(_driver, TimeSpan.FromSeconds(Constants.DEFAULT_TIMEOUT));
            
    try
    {
        IWebElement myElement = wait.Until&lt;IWebElement&gt;(driver =&gt;
        {
            IWebElement tempElement = _driver.FindElement(by);
            return (tempElement.Displayed &amp;&amp; tempElement.Enabled) ? tempElement : null;
        }
        );
        myElement.Clear();
        myElement.SendKeys(valueToType);
    }
    catch (WebDriverTimeoutException)
    {
        Assert.Fail($"Exception in SendKeys(): element located by {by.ToString()} not visible and enabled within {Constants.DEFAULT_TIMEOUT} seconds.");
    }
}</pre>

From the Selenium docs, we learn that the _Until()_ method repeatedly (with a default but configurable polling interval of 500 milliseconds) evaluates the lambda expression, until one of these conditions applies:

  * The expression returns neither null nor false;
  * The function throws an exception that is not in the list of ignored expressions;
  * The specified timeout expires

This explains why I created the lambda expression like I did. Before clicking on an element, I want to wait until it is clickable, which I define as it being both visible (expressed through the _Displayed_ property) and enabled (the _Enabled_ property). If both are true, I return the element, otherwise null. If this happens within the timeout set, I&#8217;ll clear the text field and use the standard _SendKeys()_ method to type the specified value. If a _WebDriverTimeOutException_ occurs, I&#8217;ll handle it accordingly, in this case by failing the test with a readable message.

Yes, implementing my helper methods like this makes my code a little more verbose, but you could argue that this is a small price to pay for not having to rely on (part of) a library that has been deprecated and of which the future is uncertain. Plus, because I define all these helpers in a single class, there&#8217;s really only one place in my code that is affected. The Page Objects and my test code remain wholly unaffected.

<a rel="noreferrer noopener" aria-label="This GitHub repository (opens in a new tab)" href="https://github.com/basdijkstra/ui-automation-example-csharp" target="_blank">This GitHub repository</a> contains a couple of runnable example tests that are written against the <a rel="noreferrer noopener" aria-label="ParaBank (opens in a new tab)" href="http://parabank.parasoft.com" target="_blank">ParaBank</a> demo application. This is an updated version of the solution described [here](https://www.ontestautomation.com/how-i-would-approach-creating-automated-user-interface-driven-tests/). An updated version of this last blog post is coming soon, by the way, so that it reflects some opinions and preferences of mine that have changed since I wrote that one. No longer having to rely on _ExpectedConditions_, as I discussed here, is but one of those changes.