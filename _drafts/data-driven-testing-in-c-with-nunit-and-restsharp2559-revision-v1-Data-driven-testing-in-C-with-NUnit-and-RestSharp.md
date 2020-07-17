---
id: 2568
title: 'Data driven testing in C# with NUnit and RestSharp'
date: 2019-09-23T09:57:15+02:00
author: Bas
layout: revision
guid: https://www.ontestautomation.com/2559-revision-v1/
permalink: /2559-revision-v1/
---
In [a previous post](https://www.ontestautomation.com/restful-api-testing-in-csharp-with-restsharp/), I gave some examples of how to write some basic tests in C# for RESTful APIs using NUnit and the <a href="http://restsharp.org/" target="_blank" rel="noreferrer noopener" aria-label="RestSharp (opens in a new tab)">RestSharp</a> library. In this post, I would like to extend on that a little by showing you how to make these tests data driven.

For those of you that do not know what I mean with &#8216;data driven&#8217;: when I want to run tests that exercise the same logic or flow in my application under test multiple times with various combinations of input values and corresponding expected outcomes, I call that data driven testing.

This is especially useful when testing RESTful APIs, since these are all about sending and receiving data as well as exposing business logic to other layers in an application architecture (such as a graphical user interface) or to other applications (consumers of the API).

As a starting point, consider these three tests, written using RestSharp and NUnit:

<pre class="EnlighterJSRAW" data-enlighter-language="csharp" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">[TestFixture]
public class NonDataDrivenTests
{
    private const string BASE_URL = "http://api.zippopotam.us";

    [Test]
    public void RetrieveDataForUs90210_ShouldYieldBeverlyHills()
    {
        // arrange
        RestClient client = new RestClient(BASE_URL);
        RestRequest request = 
            new RestRequest("us/90210", Method.GET);

        // act
        IRestResponse response = client.Execute(request);
        LocationResponse locationResponse =
            new JsonDeserializer().
            Deserialize&lt;LocationResponse&gt;(response);

        // assert
        Assert.That(
            locationResponse.Places[0].PlaceName,
            Is.EqualTo("Beverly Hills")
        );
    }

    [Test]
    public void RetrieveDataForUs12345_ShouldYieldSchenectady()
    {
        // arrange
        RestClient client = new RestClient(BASE_URL);
        RestRequest request =
            new RestRequest("us/12345", Method.GET);

        // act
        IRestResponse response = client.Execute(request);
        LocationResponse locationResponse =
            new JsonDeserializer().
            Deserialize&lt;LocationResponse&gt;(response);

        // assert
        Assert.That(
            locationResponse.Places[0].PlaceName,
            Is.EqualTo("Schenectady")
        );
    }

    [Test]
    public void RetrieveDataForCaY1A_ShouldYieldWhiteHorse()
    {
        // arrange
        RestClient client = new RestClient(BASE_URL);
        RestRequest request = 
            new RestRequest("ca/Y1A", Method.GET);

        // act
        IRestResponse response = client.Execute(request);
        LocationResponse locationResponse =
            new JsonDeserializer().
            Deserialize&lt;LocationResponse&gt;(response);

        // assert
        Assert.That(
            locationResponse.Places[0].PlaceName,
            Is.EqualTo("Whitehorse")
        );
    }
}</pre>

Please note that the _LocationResponse_ type is a custom type I defined myself, see <a rel="noreferrer noopener" aria-label="the GitHub repository for this post (opens in a new tab)" href="https://github.com/basdijkstra/ota-examples/tree/master/RestSharpDataDriven" target="_blank">the GitHub repository for this post</a> for its implementation.

These tests are a good example of what I wrote about earlier: I&#8217;m invoking the same logic (retrieving location data based on a country and zip code and then verifiying the corresponding place name from the API response) three times with different sets of test data.

This quickly gets very inefficient when you add more tests / more test data combinations, resulting in a lot of duplicated code. Luckily, NUnit provides several ways to make these tests data driven. Let&#8217;s look at two of them in some more detail.

### Using the [TestCase] attribute

The first way to create data driven tests is by using the _[TestCase]_ attribute that NUnit provides. You can add multiple _[TestCase]_ attributes for a single test method, and specify the combinations of input and expected output parameters that the test method should take.

Additionally, you can specify other characteristics for the individual test cases. One of the most useful ones is the _TestName_ property, which can be used to provide a legible and useful name for the individual test case. This name also turns up in the reporting, so I highly advise you to take the effort to specify one.

Here&#8217;s what our code looks like when we refactor it to use the _[TestCase]_ attribute:

<pre class="EnlighterJSRAW" data-enlighter-language="csharp" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">[TestFixture]
public class DataDrivenUsingAttributesTests
{
    private const string BASE_URL = "http://api.zippopotam.us";

    [TestCase("us", "90210", "Beverly Hills", TestName = "Check that US zipcode 90210 yields Beverly Hills")]
    [TestCase("us", "12345", "Schenectady", TestName = "Check that US zipcode 12345 yields Schenectady")]
    [TestCase("ca", "Y1A", "Whitehorse", TestName = "Check that CA zipcode Y1A yields Whitehorse")]
    public void RetrieveDataFor_ShouldYield
        (string countryCode, string zipCode, string expectedPlaceName)
    {
        // arrange
        RestClient client = new RestClient(BASE_URL);
        RestRequest request =
            new RestRequest($"{countryCode}/{zipCode}", Method.GET);

        // act
        IRestResponse response = client.Execute(request);
        LocationResponse locationResponse =
            new JsonDeserializer().
            Deserialize&lt;LocationResponse&gt;(response);

        // assert
        Assert.That(
            locationResponse.Places[0].PlaceName,
            Is.EqualTo(expectedPlaceName)
        );
    }
}</pre>

Much better! We now only have to define our test logic once, and NUnit takes care of iterating over the values defined in the _[TestCase]_ attributes:<figure class="wp-block-image">

<img src="https://www.ontestautomation.com/wp-content/uploads/2019/09/restsharp_datadriven_testcase_attribute.png" alt="NUnit test results for the data driven tests using [TestCase] attributes" class="wp-image-2566" srcset="https://www.ontestautomation.com/wp-content/uploads/2019/09/restsharp_datadriven_testcase_attribute.png 615w, https://www.ontestautomation.com/wp-content/uploads/2019/09/restsharp_datadriven_testcase_attribute-300x108.png 300w" sizes="(max-width: 615px) 100vw, 615px" /> </figure> 

There are some downsides to using the _[TestCase]_ attributes, though:

  * It&#8217;s all good when you just want to run a small amount of test iterations, but when you want to / have to test for larger numbers of combinations of input and output parameters, your code quickly gets messy (on a side note, if this is the case for you, try looking into [property-based testing](https://www.ontestautomation.com/an-introduction-to-property-based-testing-with-junit-quickcheck/) instead of the example-based testing we&#8217;re doing here).
  * You still have to hard code your test data in your code, which might give problems with scaling and maintaining your tests in the future.

This is where the _[TestCaseSource]_ attribute comes in.

### Using the [TestCaseSource] attribute

If you want to or need to work with larger numbers of combinations of test data and/or you want to be able to specify your test data outside of your test class, then using _[TestCaseSource]_ might be a useful option to explore.

In this approach, you specify or read the test data in a separate method, which is then passed to the original test method. NUnit will take care of iterating over the different combinations of test data returned by the method that delivers the test data.

Here&#8217;s an example of how to apply _[TestCaseSource]_ to our tests:

<pre class="EnlighterJSRAW" data-enlighter-language="csharp" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">[TestFixture]
public class DataDrivenUsingTestCaseSourceTests
{
    private const string BASE_URL = "http://api.zippopotam.us";

    [Test, TestCaseSource("LocationTestData")]
    public void RetrieveDataFor_ShouldYield
        (string countryCode, string zipCode, string expectedPlaceName)
    {
        // arrange
        RestClient client = new RestClient(BASE_URL);
        RestRequest request =
            new RestRequest($"{countryCode}/{zipCode}", Method.GET);

        // act
        IRestResponse response = client.Execute(request);
        LocationResponse locationResponse =
            new JsonDeserializer().
            Deserialize&lt;LocationResponse&gt;(response);

        // assert
        Assert.That(
            locationResponse.Places[0].PlaceName,
            Is.EqualTo(expectedPlaceName)
        );
    }

    private static IEnumerable&lt;TestCaseData&gt; LocationTestData()
    {
        yield return new TestCaseData("us", "90210", "Beverly Hills").
            SetName("Check that US zipcode 90210 yields Beverly Hills");
        yield return new TestCaseData("us", "12345", "Schenectady").
            SetName("Check that US zipcode 12345 yields Schenectady");
        yield return new TestCaseData("ca", "Y1A", "Whitehorse").
            SetName("Check that CA zipcode Y1A yields Whitehorse");
    }
}</pre>

In this example, we specify our test data in a separate method _LocationTestData()_, and then tell the test method to use that method as the test data source using the _[TestDataSource]_ attribute, which takes as its argument the name of the test data method.

For clarity, the test data is still hard coded in the body of the _LocationTestData()_ method, but that&#8217;s not mandatory. You could just as easily write a method that reads the test data from any external source, as long as the test data method is static and returns an object of type _IEnumerable_, or any object that implements this interface.

Also, since the _[TestCase]_ and _[TestCaseSource]_ attributes are features of NUnit, and not of RestSharp, you can apply the principles illustrated in this post to other types of tests just as well.

Beware, though, before you use them for user interface-driven testing with tools like Selenium WebDriver. Chances are that you&#8217;re falling for a classic case of &#8216;just because you can, doesn&#8217;t mean you should&#8217;. I find data driven testing with Selenium WebDriver to be a test code smell: if you&#8217;re going through the same screen flow multiple times, and the only variation is in the test data, there&#8217;s a high chance that there&#8217;s a more efficient way to test the same underlying business logic (for example by leveraging APIs).

<a rel="noreferrer noopener" aria-label="Chris McMahon (opens in a new tab)" href="https://twitter.com/chris_mcmahon/" target="_blank">Chris McMahon</a> explains this much more eloquently in <a rel="noreferrer noopener" aria-label="a blog post of his (opens in a new tab)" href="http://chrismcmahonsblog.blogspot.com/2017/11/ui-test-heuristic-dont-repeat-your-paths.html" target="_blank">a blog post of his</a>. I highly recommend you reading that.

For other types of testing (API, unit, &#8230;), data driven testing could be a very powerful way to make your test code better maintainable and more powerful.

All example code in this blog post can be found <a href="https://github.com/basdijkstra/ota-examples/tree/master/RestSharpDataDriven" target="_blank" rel="noreferrer noopener" aria-label="on this GitHub page (opens in a new tab)">on this GitHub page</a>.