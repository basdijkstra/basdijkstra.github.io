---
id: 2628
title: An introduction to REST API testing in Go with Resty
date: 2020-01-24T10:37:54+01:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=2628
permalink: /an-introduction-to-rest-api-testing-in-go-with-resty/
spay_email:
  - ""
categories:
  - API testing
tags:
  - api testing
  - go
  - golang
  - resty
---
Just over a year ago, I was working in a team that, among other things, was responsible for developing and running some microservices that delivered data to a front end. It turned out those services were written in <a href="https://en.wikipedia.org/wiki/Go_(programming_language)" target="_blank" rel="noreferrer noopener" aria-label="Go (opens in a new tab)">Go</a>, a programming language that I had vaguely heard of before but never worked with.

However, as the tester and automation engineer in that team, I was tasked with writing some automated checks that could be made part of the build pipeline, so I decided that it would be a good opportunity to pick up some new skills and tools and write those checks in the same language. Now, Go is a relatively young language, first appearing in 2009. This also meant that the tool set around Go isn&#8217;t as mature as in other languages like Java or C#. Despite that, I was able to get these tests up and running fairly quickly.

Recently, I stumbled upon Go again (can&#8217;t really remember when and where, not that it&#8217;s important) and thought it would be a good idea to revisit the language. In this blog post, I&#8217;d like to show you some examples of how to write and run tests against a REST API in Go. The API I&#8217;m using for this is once again the <a href="http://api.zippopotam.us/" target="_blank" rel="noreferrer noopener" aria-label="Zippopotam.us API (opens in a new tab)">Zippopotam.us API</a>.

Now, Go has support for testing built-in, so unlike languages like Java or C#, you don&#8217;t need to add a unit testing framework to your project. All you need to do is import the _testing_ library in your code, have your Go file name end with __test.go_, write a method with a name starting with _Test_, run

<pre class="wp-block-preformatted">go test</pre>

and you&#8217;re good to go. For setting up requests and capturing, parsing and checking the response, I found the <a rel="noreferrer noopener" aria-label="Resty (opens in a new tab)" href="https://github.com/go-resty/resty" target="_blank">Resty</a> library to be very useful. It&#8217;s pretty similar to the requests library in Python, or RestSharp for C#. Here&#8217;s what a first test, checking the response status code for a correctly formatted GET requests to our API, looks like in Go with Resty:

<pre class="EnlighterJSRAW" data-enlighter-language="cpp" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">func Test_GetUs90210_StatusCodeShouldEqual200(t *testing.T) {

	client := resty.New()

	resp, _ := client.R().Get("http://api.zippopotam.us/us/90210")

	if resp.StatusCode() != 200 {
		t.Errorf("Unexpected status code, expected %d, got %d instead", 200, resp.StatusCode())
	}
}</pre>

Note that I&#8217;m not going to explain all the nuts and bolts of Go and its syntax here. If you&#8217;re interested to learn more, I&#8217;d recommend <a rel="noreferrer noopener" aria-label="this book (opens in a new tab)" href="https://www.amazon.com/Programming-Language-Addison-Wesley-Professional-Computing/dp/0134190440" target="_blank">this book</a> or taking the <a rel="noreferrer noopener" aria-label="Tour of Go (opens in a new tab)" href="https://tour.golang.org/welcome/1" target="_blank">Tour of Go</a>.

If we run this test using the _go test_ command, you&#8217;ll see that it passes:<figure class="wp-block-image size-large">

<img src="https://www.ontestautomation.com/wp-content/uploads/2020/01/go_test_console_output.png" alt="" class="wp-image-2630" srcset="https://www.ontestautomation.com/wp-content/uploads/2020/01/go_test_console_output.png 582w, https://www.ontestautomation.com/wp-content/uploads/2020/01/go_test_console_output-300x47.png 300w" sizes="(max-width: 582px) 100vw, 582px" /> </figure> 

Each test method takes an argument of type _T_ (from the _testing_ library), which is used to manage test state. What might strike you as odd (it did for me) is that there&#8217;s no assertion in this test like you would expect if you&#8217;re familiar with unit testing frameworks like JUnit, NUnit or pytest. The reason is simple: they don&#8217;t exist in the Go _testing_ library. The people behind Go have their reasons for it, but this is something I don&#8217;t really like. I prefer using assertions, because they make my test code easier to read, plus it saves me from writing all these if-then-else statements myself.

Fortunately, other people thought the same, so there are third party libraries available that let you write assertions. I chose to use <a rel="noreferrer noopener" aria-label="Testify (opens in a new tab)" href="https://github.com/stretchr/testify" target="_blank">Testify</a>, because it not only provides assertions, it also allows you to create test suites and use setup and teardown methods in a way similar to other languages. Here&#8217;s what the same test looks like, but now using an assertion provided by Testify:

<pre class="EnlighterJSRAW" data-enlighter-language="cpp" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">func Test_GetUs90210_StatusCodeShouldEqual200(t *testing.T) {

	client := resty.New()

	resp, _ := client.R().Get("http://api.zippopotam.us/us/90210")

	assert.Equal(t, 200, resp.StatusCode())
}</pre>

Much better, in my opinion. Now, let&#8217;s see if we can also check a response header value:

<pre class="EnlighterJSRAW" data-enlighter-language="cpp" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">func Test_GetUs90210_ContentTypeShouldEqualApplicationJson(t *testing.T) {

	client := resty.New()

	resp, _ := client.R().Get("http://api.zippopotam.us/us/90210")

	assert.Equal(t, "application/json", resp.Header().Get("Content-Type"))
}</pre>

That is pretty straightforward. Next, how about extracting and checking a response body value? The easiest way to do that in Go is to unmarshal (deserialize) the response body into a struct (a data structure in Go). If we want to do that, we first have to define the struct like this:

<pre class="EnlighterJSRAW" data-enlighter-language="cpp" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">type LocationResponse struct {
	Country string `json:"country"`
}</pre>

This defines a struct _LocationResponse_ with a single element _Country_. The _json:&#8221;country&#8221;_ tag tells Go that it should populate this element with the value of the _country_ element in the JSON response body. We don&#8217;t have to worry about the other elements in the response, those simply will not be mapped (unless you need them in a check, then you&#8217;ll need to add them to the struct, too).

Now, we can write a test that maps the response body to a struct of type _LocationResponse_ and then check the value that has been assigned to the _Country_ element:

<pre class="EnlighterJSRAW" data-enlighter-language="cpp" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">func Test_GetUs90210_CountryShouldEqualUnitedStates(t *testing.T) {

	client := resty.New()

	resp, _ := client.R().Get("http://api.zippopotam.us/us/90210")

	myResponse := LocationResponse{}

	err := json.Unmarshal(resp.Body(), &myResponse)

	if err != nil {
		fmt.Println(err)
		return
	}

	assert.Equal(t, "United States", myResponse.Country)
}</pre>

As a final example, I&#8217;d like to show you how to create a setup method for creating an initial state for all the tests in a suite. Here&#8217;s how to do that using Testify and its _suite_ module:

<pre class="EnlighterJSRAW" data-enlighter-language="cpp" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">type ZippopotamUsTestSuite struct {
	suite.Suite
	ApiClient *resty.Client
}

func (suite *ZippopotamUsTestSuite) SetupTest() {
	suite.ApiClient = resty.New()
}

func (suite *ZippopotamUsTestSuite) Test_GetUs90210_StatusCodeShouldEqual200() {
	resp, _ := suite.ApiClient.R().Get("http://api.zippopotam.us/us/90210")

	assert.Equal(suite.T(), 200, resp.StatusCode())
}</pre>

First, we create a struct _ZippopotamUsTestSuite_ that contains all the objects shared by all tests. In this case, all I need is a Client (a class in Resty) called _ApiClient_ (in Go the variable name comes before the data type when you declare a new variable). We can then write a method called _SetupTest()_ that does the setup for all of our tests, and we use _(suite *ZippopotamUsTestSuite)_ to make our existing test methods part of the suite we defined.

To actually run our test suite, we need to create a &#8216;regular&#8217; test method and pass the suite we have created to the _suite.Run()_ method of Testify:

<pre class="EnlighterJSRAW" data-enlighter-language="cpp" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">func TestZippopotamUsSuite(t *testing.T) {
	suite.Run(t, new(ZippopotamUsTestSuite))
}</pre>

If you omit this step, _go test_ will not run the test suite!

All in all, given my experience is mostly with Java, C# and Python, I&#8217;ve found writing tests in Go to be a little more cumbersome than in those languages. However, with the right tool set, it is perfectly possible to write readable and well-structured tests in Go, as the examples in this blog post hopefully demonstrated.

I&#8217;m keen to further explore writing tests (and other software) with Go, so I&#8217;ve recently started learning the language through <a rel="noreferrer noopener" aria-label="this (opens in a new tab)" href="https://www.coursera.org/specializations/google-golang" target="_blank">this</a> Coursera course series. It&#8217;s mainly targeted towards those wanting to become a Go developer, and there isn&#8217;t much talk of testing, but I&#8217;m looking forward to it anyway. I&#8217;ll try to share some more tips and tricks on writing tests in Go in the near future.

If you want to read more about writing tests in Go, I recommend you reading <a rel="noreferrer noopener" aria-label="this article (opens in a new tab)" href="https://blog.alexellis.io/golang-writing-unit-tests/" target="_blank">this article</a> by Alex Ellis or going through <a href="https://quii.gitbook.io/learn-go-with-tests/" target="_blank" rel="noreferrer noopener" aria-label="this tutorial (opens in a new tab)">this tutorial</a>.

All code that I&#8217;ve used in this blog post can be found <a href="https://github.com/basdijkstra/ota-examples/tree/master/golang-resty" target="_blank" rel="noreferrer noopener" aria-label="here (opens in a new tab)">here</a>.