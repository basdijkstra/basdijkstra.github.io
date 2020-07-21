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

However, as the tester and automation engineer in that team, I was tasked with writing some automated checks that could be made part of the build pipeline, so I decided that it would be a good opportunity to pick up some new skills and tools and write those checks in the same language. Now, Go is a relatively young language, first appearing in 2009. This also meant that the tool set around Go isn't as mature as in other languages like Java or C#. Despite that, I was able to get these tests up and running fairly quickly.

Recently, I stumbled upon Go again (can't really remember when and where, not that it's important) and thought it would be a good idea to revisit the language. In this blog post, I'd like to show you some examples of how to write and run tests against a REST API in Go. The API I'm using for this is once again the <a href="http://api.zippopotam.us/" target="_blank" rel="noreferrer noopener" aria-label="Zippopotam.us API (opens in a new tab)">Zippopotam.us API</a>.

Now, Go has support for testing built-in, so unlike languages like Java or C#, you don't need to add a unit testing framework to your project. All you need to do is import the `testing` library in your code, have your Go file name end with `_test.go`, write a method with a name starting with `Test`, run

`go test`

and you're good to go. For setting up requests and capturing, parsing and checking the response, I found the <a rel="noreferrer noopener" aria-label="Resty (opens in a new tab)" href="https://github.com/go-resty/resty" target="_blank">Resty</a> library to be very useful. It's pretty similar to the requests library in Python, or RestSharp for C#. Here's what a first test, checking the response status code for a correctly formatted GET requests to our API, looks like in Go with Resty:

{% highlight go %}
func Test_GetUs90210_StatusCodeShouldEqual200(t *testing.T) {

	client := resty.New()

	resp, _ := client.R().Get("http://api.zippopotam.us/us/90210")

	if resp.StatusCode() != 200 {
		t.Errorf("Unexpected status code, expected %d, got %d instead", 200, resp.StatusCode())
	}
}
{% endhighlight %}

Note that I'm not going to explain all the nuts and bolts of Go and its syntax here. If you're interested to learn more, I'd recommend <a rel="noreferrer noopener" aria-label="this book (opens in a new tab)" href="https://www.amazon.com/Programming-Language-Addison-Wesley-Professional-Computing/dp/0134190440" target="_blank">this book</a> or taking the <a rel="noreferrer noopener" aria-label="Tour of Go (opens in a new tab)" href="https://tour.golang.org/welcome/1" target="_blank">Tour of Go</a>.

If we run this test using the `go test` command, you'll see that it passes:

![go test console output](https://basdijkstra/github.io/images/go_test_console_output.png "Go test console output")

Each test method takes an argument of type `T` (from the `testing` library), which is used to manage test state. What might strike you as odd (it did for me) is that there's no assertion in this test like you would expect if you're familiar with unit testing frameworks like JUnit, NUnit or pytest. The reason is simple: they don't exist in the Go `testing` library. The people behind Go have their reasons for it, but this is something I don't really like. I prefer using assertions, because they make my test code easier to read, plus it saves me from writing all these if-then-else statements myself.

Fortunately, other people thought the same, so there are third party libraries available that let you write assertions. I chose to use <a rel="noreferrer noopener" aria-label="Testify (opens in a new tab)" href="https://github.com/stretchr/testify" target="_blank">Testify</a>, because it not only provides assertions, it also allows you to create test suites and use setup and teardown methods in a way similar to other languages. Here's what the same test looks like, but now using an assertion provided by Testify:

{% highlight go %}
func Test_GetUs90210_StatusCodeShouldEqual200(t *testing.T) {

	client := resty.New()

	resp, _ := client.R().Get("http://api.zippopotam.us/us/90210")

	assert.Equal(t, 200, resp.StatusCode())
}
{% endhighlight %}

Much better, in my opinion. Now, let's see if we can also check a response header value:

{% highlight go %}
func Test_GetUs90210_ContentTypeShouldEqualApplicationJson(t *testing.T) {

	client := resty.New()

	resp, _ := client.R().Get("http://api.zippopotam.us/us/90210")

	assert.Equal(t, "application/json", resp.Header().Get("Content-Type"))
}
{% endhighlight %}

That is pretty straightforward. Next, how about extracting and checking a response body value? The easiest way to do that in Go is to unmarshal (deserialize) the response body into a struct (a data structure in Go). If we want to do that, we first have to define the struct like this:

{% highlight go %}
type LocationResponse struct {
	Country string `json:"country"`
}
{% endhighlight %}

This defines a struct `LocationResponse` with a single element `Country`. The `json:"country"` tag tells Go that it should populate this element with the value of the `country` element in the JSON response body. We don't have to worry about the other elements in the response, those simply will not be mapped (unless you need them in a check, then you'll need to add them to the struct, too).

Now, we can write a test that maps the response body to a struct of type `LocationResponse` and then check the value that has been assigned to the `Country` element:

{% highlight go %}
func Test_GetUs90210_CountryShouldEqualUnitedStates(t *testing.T) {

	client := resty.New()

	resp, _ := client.R().Get("http://api.zippopotam.us/us/90210")

	myResponse := LocationResponse{}

	err := json.Unmarshal(resp.Body(), &myResponse)

	if err != nil {
		fmt.Println(err)
		return
	}

	assert.Equal(t, "United States", myResponse.Country)
}
{% endhighlight %}

As a final example, I'd like to show you how to create a setup method for creating an initial state for all the tests in a suite. Here's how to do that using Testify and its `suite` module:

{% highlight go %}
type ZippopotamUsTestSuite struct {
	suite.Suite
	ApiClient *resty.Client
}

func (suite *ZippopotamUsTestSuite) SetupTest() {
	suite.ApiClient = resty.New()
}

func (suite *ZippopotamUsTestSuite) Test_GetUs90210_StatusCodeShouldEqual200() {
	resp, _ := suite.ApiClient.R().Get("http://api.zippopotam.us/us/90210")

	assert.Equal(suite.T(), 200, resp.StatusCode())
}
{% endhighlight %}

First, we create a struct `ZippopotamUsTestSuite` that contains all the objects shared by all tests. In this case, all I need is a Client (a class in Resty) called `ApiClient` (in Go the variable name comes before the data type when you declare a new variable). We can then write a method called `SetupTest()` that does the setup for all of our tests, and we use `(suite *ZippopotamUsTestSuite)` to make our existing test methods part of the suite we defined.

To actually run our test suite, we need to create a 'regular' test method and pass the suite we have created to the `suite.Run()` method of Testify:

{% highlight go %}
func TestZippopotamUsSuite(t *testing.T) {
	suite.Run(t, new(ZippopotamUsTestSuite))
}
{% endhighlight %}

If you omit this step, `go test` will not run the test suite!

All in all, given my experience is mostly with Java, C# and Python, I've found writing tests in Go to be a little more cumbersome than in those languages. However, with the right tool set, it is perfectly possible to write readable and well-structured tests in Go, as the examples in this blog post hopefully demonstrated.

I'm keen to further explore writing tests (and other software) with Go, so I've recently started learning the language through <a rel="noreferrer noopener" aria-label="this (opens in a new tab)" href="https://www.coursera.org/specializations/google-golang" target="_blank">this</a> Coursera course series. It's mainly targeted towards those wanting to become a Go developer, and there isn't much talk of testing, but I'm looking forward to it anyway. I'll try to share some more tips and tricks on writing tests in Go in the near future.

If you want to read more about writing tests in Go, I recommend you reading <a rel="noreferrer noopener" aria-label="this article (opens in a new tab)" href="https://blog.alexellis.io/golang-writing-unit-tests/" target="_blank">this article</a> by Alex Ellis or going through <a href="https://quii.gitbook.io/learn-go-with-tests/" target="_blank" rel="noreferrer noopener" aria-label="this tutorial (opens in a new tab)">this tutorial</a>.

All code that I've used in this blog post can be found <a href="https://github.com/basdijkstra/ota-examples/tree/master/golang-resty" target="_blank" rel="noreferrer noopener" aria-label="here (opens in a new tab)">here</a>.