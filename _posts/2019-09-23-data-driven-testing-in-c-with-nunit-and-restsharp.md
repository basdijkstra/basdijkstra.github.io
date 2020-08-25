---
title: 'Data driven testing in C# with NUnit and RestSharp'
layout: post
permalink: /data-driven-testing-in-c-with-nunit-and-restsharp/
categories:
  - API testing
  - General test automation
tags:
  - data driven testing
  - nunit
  - restsharp
---
In [a previous post](https://www.ontestautomation.com/restful-api-testing-in-csharp-with-restsharp/), I gave some examples of how to write some basic tests in C# for RESTful APIs using NUnit and the <a href="http://restsharp.org/" target="_blank" rel="noreferrer noopener" aria-label="RestSharp (opens in a new tab)">RestSharp</a> library. In this post, I would like to extend on that a little by showing you how to make these tests data driven.

For those of you that do not know what I mean with 'data driven': when I want to run tests that exercise the same logic or flow in my application under test multiple times with various combinations of input values and corresponding expected outcomes, I call that data driven testing.

This is especially useful when testing RESTful APIs, since these are all about sending and receiving data as well as exposing business logic to other layers in an application architecture (such as a graphical user interface) or to other applications (consumers of the API).

As a starting point, consider these three tests, written using RestSharp and NUnit:

{% highlight csharp %}
[TestFixture]
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
}
{% endhighlight %}

Please note that the `LocationResponse` type is a custom type I defined myself, see <a rel="noreferrer noopener" aria-label="the GitHub repository for this post (opens in a new tab)" href="https://github.com/basdijkstra/ota-examples/tree/master/RestSharpDataDriven" target="_blank">the GitHub repository for this post</a> for its implementation.

These tests are a good example of what I wrote about earlier: I'm invoking the same logic (retrieving location data based on a country and zip code and then verifiying the corresponding place name from the API response) three times with different sets of test data.

This quickly gets very inefficient when you add more tests / more test data combinations, resulting in a lot of duplicated code. Luckily, NUnit provides several ways to make these tests data driven. Let's look at two of them in some more detail.

### Using the `[TestCase]` attribute

The first way to create data driven tests is by using the `[TestCase]` attribute that NUnit provides. You can add multiple `[TestCase]` attributes for a single test method, and specify the combinations of input and expected output parameters that the test method should take.

Additionally, you can specify other characteristics for the individual test cases. One of the most useful ones is the `TestName` property, which can be used to provide a legible and useful name for the individual test case. This name also turns up in the reporting, so I highly advise you to take the effort to specify one.

Here's what our code looks like when we refactor it to use the `[TestCase]` attribute:

{% highlight csharp %}
[TestFixture]
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
}
{% endhighlight %}

Much better! We now only have to define our test logic once, and NUnit takes care of iterating over the values defined in the `[TestCase]` attributes:

![restsharp data driven testcase](/images/blog/restsharp_datadriven_testcase_attribute.png "Data driven test cases using RestSharp and the TestCase attribute") 

There are some downsides to using the `[TestCase]` attributes, though:

  * It's all good when you just want to run a small amount of test iterations, but when you want to / have to test for larger numbers of combinations of input and output parameters, your code quickly gets messy (on a side note, if this is the case for you, try looking into [property-based testing](https://www.ontestautomation.com/an-introduction-to-property-based-testing-with-junit-quickcheck/) instead of the example-based testing we're doing here).
  * You still have to hard code your test data in your code, which might give problems with scaling and maintaining your tests in the future.

This is where the `[TestCaseSource]` attribute comes in.

### Using the `[TestCaseSource]` attribute

If you want to or need to work with larger numbers of combinations of test data and/or you want to be able to specify your test data outside of your test class, then using `[TestCaseSource]` might be a useful option to explore.

In this approach, you specify or read the test data in a separate method, which is then passed to the original test method. NUnit will take care of iterating over the different combinations of test data returned by the method that delivers the test data.

Here's an example of how to apply `[TestCaseSource]` to our tests:

{% highlight csharp %}
[TestFixture]
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
}
{% endhighlight %}

In this example, we specify our test data in a separate method `LocationTestData()`, and then tell the test method to use that method as the test data source using the `[TestDataSource]` attribute, which takes as its argument the name of the test data method.

For clarity, the test data is still hard coded in the body of the `LocationTestData()` method, but that's not mandatory. You could just as easily write a method that reads the test data from any external source, as long as the test data method is static and returns an object of type `IEnumerable`, or any object that implements this interface.

Also, since the `[TestCase]` and `[TestCaseSource]` attributes are features of NUnit, and not of RestSharp, you can apply the principles illustrated in this post to other types of tests just as well.

Beware, though, before you use them for user interface-driven testing with tools like Selenium WebDriver. Chances are that you're falling for a classic case of 'just because you can, doesn't mean you should'. I find data driven testing with Selenium WebDriver to be a test code smell: if you're going through the same screen flow multiple times, and the only variation is in the test data, there's a high chance that there's a more efficient way to test the same underlying business logic (for example by leveraging APIs).

<a rel="noreferrer noopener" aria-label="Chris McMahon (opens in a new tab)" href="https://twitter.com/chris_mcmahon/" target="_blank">Chris McMahon</a> explains this much more eloquently in <a rel="noreferrer noopener" aria-label="a blog post of his (opens in a new tab)" href="http://chrismcmahonsblog.blogspot.com/2017/11/ui-test-heuristic-dont-repeat-your-paths.html" target="_blank">a blog post of his</a>. I highly recommend you reading that.

For other types of testing (API, unit, ...), data driven testing could be a very powerful way to make your test code better maintainable and more powerful.

All example code in this blog post can be found <a href="https://github.com/basdijkstra/ota-examples/tree/master/RestSharpDataDriven" target="_blank" rel="noreferrer noopener" aria-label="on this GitHub page (opens in a new tab)">on this GitHub page</a>.