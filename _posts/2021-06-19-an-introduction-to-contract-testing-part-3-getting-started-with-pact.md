---
title: An introduction to contract testing - part 3 - getting started with Pact
layout: post
permalink: /an-introduction-to-contract-testing-part-3-getting-started-with-pact/
categories:
  - consumer driven contract testing
tags:
  - contract testing
  - pact
  - api
---
_In this series of articles, you'll be introduced to a (fictitious but realistic) use case for consumer-driven contract testing with [Pact](https://docs.pact.io?utm_source=partner&utm_campaign=on-test-automation&utm_content=blog-getting-started-with-pact){:target="_blank"} and [Pactflow](https://pactflow.io?utm_source=partner&utm_campaign=on-test-automation&utm_content=blog-getting-started-with-pact){:target="_blank"}. Over 6 articles, you'll read about:_

* _[The different parties that play a role in this use case and the challenges that integration and end-to-end testing pose for them](/an-introduction-to-contract-testing-part-1-meet-the-players/)_
* _[How contract testing can address these challenges](/an-introduction-to-contract-testing-part-2-introducing-contract-testing/)_
* _How to use Pact for consumer-driven contract testing (**this article**)_
* _[How to make contract testing an integral part of an automated development and delivery workflow](/an-introduction-to-contract-testing-part-4-automating-the-workflow/)_
* _What the effect is of changes in the expectations and implementations of the consumer and provider parties_
* _How to invite new parties to the contract testing ecosystem and how bidirectional contracts can make this a smooth process_

_All code samples that are shown and referenced in these articles can be found [on GitHub](https://github.com/basdijkstra/introduction-to-contract-testing){:target="_blank"}._

In [the previous article](/an-introduction-to-contract-testing-part-2-introducing-contract-testing/), we introduced the concept of consumer-driven contract testing and explained how it can address the challenges that occur when you're tasked with integration- and end-to-end testing of distributed systems. In this article, you'll see how to get started with Pact to make consumer-driven contract testing a reality.

So, what is Pact anyway? As stated in the [Pact documentation](https://docs.pact.io?utm_source=partner&utm_campaign=on-test-automation&utm_content=blog-getting-started-with-pact){:target="_blank"}:

> Pact is a code-first tool for testing HTTP and message integrations using contract tests.

Rather than giving you a detailed theoretical background description of Pact, let's review the four steps of consumer-driven contract testing instead, and see how you can perform these with Pact for the parties we introduced in [the first article](/an-introduction-to-contract-testing-part-1-meet-the-players/).

The examples are written in Java, but since Pact is available for a range of other languages (including C#, Python, Go and others) as well, the concepts are applicable to those bindings as well. At some point I might decide to provide the same examples for other languages, too, but for now, we'll stick with Java and [Pact-JVM](https://github.com/pact-foundation/pact-jvm/){:target="_blank"}.

### Step 1: the consumer generates a contract containing their expectations about the behaviour of a provider
Pact enables you to define expectations a consumer has about the way in which the provider responds during their interactions. These expectations focus on the messages rather than the behaviour, i.e., Pact is not meant to be used to test the implementation of the provider, as that's the responsibility of the team developing the provider itself.

Let's take a look at the expectations that our Customer API might have about the way the Address API responds to its requests. As we have seen in [the first article in this series](/an-introduction-to-contract-testing-part-1-meet-the-players/), when the Customer API requests data for an address, the Address API will return a payload that looks like this:

{% highlight json %}
{
    "id": "87256abc-f6b3-4e91-9f60-3ca3f54863d5",
    "address_type": "billing",
    "street": "Main Street",
    "number": 123,
    "city": "Nothingville",
    "zip_code": 54321,
    "state": "Tennessee",
    "country": "United States"
}
{% endhighlight %}

Assuming that all the fields in this payload are required fields for the Customer API, and the data types should match those in the example above (the actual values returned by the provider may differ), you can express these expectations about the response body in Pact as follows:

{% highlight java %}
DslPart body = LambdaDsl.newJsonBody((o) -> o
    .uuid("id", ID)
    .stringType("addressType", ADDRESS_TYPE)
    .stringType("street", STREET)
    .integerType("number", NUMBER)
    .stringType("city", CITY)
    .integerType("zipCode", ZIP_CODE)
    .stringType("state", STATE)
    .stringType("country", COUNTRY)
).build();
{% endhighlight %}

The `LambdaDsl` class enables you to express expectations about the structure of a response body using a fluid DSL. The `stringType("addressType", ADDRESS_TYPE)` method adds the expectation that the response should contain a field called `address`, and its value should be a string value.

Likewise, the `integerType()` method can be used to express the expectation that a response contains a field with an integer value, and the `uuid()` method expresses that a field should not just contain any string, but one that matches the format of a UUID.

A complete list of available matcher methods can be found [here](https://docs.pact.io/implementation_guides/jvm/consumer#building-json-bodies-with-pactdsljsonbody-dsl?utm_source=partner&utm_campaign=on-test-automation&utm_content=blog-getting-started-with-pact){:target="_blank"}.

The values (`ID`, `ADDRESS_TYPE`, etc.) supplied as the second argument to all of these matcher methods are examples used by Pact to populate the mock responses used when it generates a mock provider that the consumer can use to test its implementation (we'll get to that part in a bit). They do not actually express the expectation that a field should always contain that specific value.

_As a side note: While there are matcher methods that can express expectations this strict (`stringValue()`, `numberValue()`, etc.), you should use these sparingly as they create very strict expectations about the way the provider works, and can lead to unnecessary coupling between consumer and provider._

Once you have defined the expectations about the response body structure, you can add that to a pact (a contract segment containing the consumer expectations regarding a specific interaction like this:

{% highlight java %}
@Pact(consumer = "customer_consumer")
public RequestResponsePact pactForGetExistingAddressId(PactDslWithProvider builder) {

	return builder.given(
		"Customer GET: the address ID matches an existing address")
		.uponReceiving("A request for address data")
		.path(String.format("/address/%s", ID))
		.method("GET")
		.willRespondWith()
		.status(200)
		.body(body)
		.toPact();
}
{% endhighlight %}

This pact defines that:

* It is an expectation of the consumer identified by the `customer_consumer` label (our Customer API)
* It is a pact for the provider state where a GET is performed, and the address ID points used in the request points to an existing address in the Address API
* The request is a GET request to `/address/{address_id}`
* The consumer expects a response with HTTP status code 200, and a response body as defined above (enclosed in the `body` variable here)

Similarly, we can add a pact for the interaction that happens when the Customer API requests data for an address ID that is correctly formatted, but for which there is no data present at the Address provider, resulting in an HTTP 404 response:

{% highlight java %}
@Pact(consumer = "customer_consumer")
public RequestResponsePact pactForGetNonExistentAddressId(PactDslWithProvider builder) {

	return builder.given(
		"Customer GET: the address ID does not match an existing address")
		.uponReceiving("A request for address data")
		.path("/address/00000000-0000-0000-0000-000000000000")
		.method("GET")
		.willRespondWith()
		.status(404)
		.toPact();
}
{% endhighlight %}

The [sample code for this blog post series](https://github.com/basdijkstra/introduction-to-contract-testing){:target="_blank"} contains pacts for even more interactions, including those for HTTP methods other than GET (DELETE, for example).

In order for Pact to create an actual contract containing the pacts for these interactions, you'll have to write unit tests that:

* verify that the consumer can process the mock responses that Pact generates from the expectations
* write the expectations to a contract that can be sent to and verified by the provider

For the GET operation we've seen earlier where data is retrieved for an address that is known to the Address provider, such a test might look like this:

{% highlight java %}
@PactVerification(fragment = "pactForGetExistingAddressId")
@Test
public void testFor_GET_existingAddressId_shouldYieldExpectedAddressData() {

	final Address address = addressServiceClient.getAddress(ID.toString());

	assertThat(address.getId()).isEqualTo(ID);
	assertThat(address.getAddressType()).isEqualTo(ADDRESS_TYPE);
	assertThat(address.getStreet()).isEqualTo(STREET);
	assertThat(address.getNumber()).isEqualTo(NUMBER);
	assertThat(address.getCity()).isEqualTo(CITY);
	assertThat(address.getZipCode()).isEqualTo(ZIP_CODE);
	assertThat(address.getState()).isEqualTo(STATE);
	assertThat(address.getCountry()).isEqualTo(COUNTRY);
}
{% endhighlight %}

Using the `@PactVerification` annotation, we 'import' the consumer expectations defined earlier and tie them to the REST call (i.e. the interaction) performed in this test.

Pact will generate a mock provider that responds in the way defined in the expectations, and its responses can then be used to verify if the consumer code works. Upon running the unit test, this interaction will be written to the contract in JSON format.

For the GET operation using a non-existent address ID, the test might look like this:

{% highlight java %}
@PactVerification(fragment = "pactForGetNonExistentAddressId")
@Test
public void testFor_GET_nonExistentAddressId_shouldYieldHttp404() {

	assertThatThrownBy(
		() -> addressServiceClient.getAddress("00000000-0000-0000-0000-000000000000")
	).isInstanceOf(HttpClientErrorException.class)
		.hasMessageContaining("404 Not Found");
}
{% endhighlight %}

The sample code on GitHub contains more tests, for both consumer parties, i.e., for the Customer API and for the Order API. In fact, the defined expectations and the tests are pretty much the same for both parties, meaning that *at this moment*, both consumers have the same expectations regarding the provider behaviour. We'll see what happens when this changes in the fifth article in this series.

### Step 2: the consumer publishes the contract for the provider to pick up
When you run the unit tests defined above on the consumer side, a contract will be generated in `/target/pacts` (the location differs when you're using Pact bindings other than Pact-JVM, for obvious reasons). An example contract can be found [here](https://github.com/basdijkstra/introduction-to-contract-testing/blob/main/address-provider/src/test/pacts/customer_consumer-address_provider.json){:target="_blank"}.

As you can see, a contract generated by Pact is an agreement (or rather an expression of expectations) between a single consumer and a single provider, and it contains an interaction segment with its respective expectations for all interactions defined in our consumer test code.

It's also good to know that while in this example, both the consumer and the provider are written in Java, this does not have to be the case to use Pact. As the contract generations are in JSON and are generated [according to a standardized format](https://github.com/pact-foundation/pact-specification){:target="_blank"}, contracts can be exchanged between parties written in different languages, as long as Pact bindings are available for those languages.

Our newly generated contract can then be published for the provider to pick up. For now, we are going to do this simply by copying the JSON file and placing it in the right folder in the provider code base. In the next article, we'll see a more efficient and fully automated way to distribute contracts.

### Step 3: the provider picks up the contract and checks that its current implementation meets the expectations expressed by the consumer in the contract
After both the Customer and the Order consumer APIs have run their tests and published their contracts, it's time for the Address provider to verify whether it can meet the expectations expressed in these contracts.

Please keep in mind that contract testing is an *asynchronous* approach to integration testing, as both the consumer and the provider can perform their due diligence regarding integration testing as befits their own development and deployment process and build cycle.

Also, since there's no need to have any (typically a large amount of) connected systems in place, contract tests with Pact are much faster than 'traditional' integration tests, both in setup and in execution.

On the provider side, we'll need to define verification points for each of the interactions listed in the contracts:

{% highlight java %}
@RunWith(SpringRestPactRunner.class)
@Provider("address_provider")
@PactFolder("src/test/pacts")
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class ContractTest {

    @TestTarget
    public final Target target = new SpringBootHttpTarget();

    // The 'as-is' service is used for all provider states, so no additional setup is needed

    @State("Customer GET: the address ID matches an existing address")
    public void addressSuppliedByCustomerGETExists() {
    }

    @State("Customer GET: the address ID does not match an existing address")
    public void addressSuppliedByCustomerGETDoesNotExist() {
    }
}
{% endhighlight %}

In our case, there's no need to perform any additional setup (our straightforward provider implementation is ready for contract verification as-is), but in case you'll need to do some additional setup to get the provider in the right state to verify a specific interaction, you can do so in the body of the respective `@State` method.

Note that currently, we specify the location where the provider can find the contracts using the `@PactFolder` annotation, as for now we're physically copying and pasting contracts from consumer to provider. Again, we'll see a more efficient way to deal with contract publication and distribution in the next article.

When we run these tests on the provider side, we'll see that currently, all expectations expressed by both the Customer and the Order consumer API can be fulfilled by the implementation of the Address API.

Here's an example output telling us that all is OK for the Customer consumer and Address provider, for the interaction where an existing address is requested by the Customer API:

{% highlight text %}
Verifying a pact between customer_consumer and address_provider
    [Using File src\test\pacts\customer_consumer-address_provider.json]
    Given Customer GET: the address ID matches an existing address
    A request for address data
        returns a response which
            has status code 200 (OK)
            has a matching body (OK)
{% endhighlight %}

In the fifth article in this series, we'll see what happens when the Customer API defines an expectation in its contract that cannot (yet) be fulfilled by the Address API.

### Step 4: the provider publishes the verification results to inform the consumer
To complete the entire consumer-driven contract testing process, the provider should inform all parties involved that all is OK from a contract verification point of view. In our current setup, there's no real way to do this, but we'll see how to close this information loop in the next article.

In this article, you've seen:

* how to use Pact-JVM to express a number of expectations about the way a provider communicates from the consumer standpoint
* how to formalize these into a contract and publish it for a provider to use, and
* how to verify an implementation against the expectations at the provider side

In the next article, you'll see how we can automate the entire CDCT process to allow us to integrate our contract tests into a CI/CD pipeline on both the consumer and the provider end.

All code used in this blog post can be found [here](https://github.com/basdijkstra/introduction-to-contract-testing){:target="_blank"}. 