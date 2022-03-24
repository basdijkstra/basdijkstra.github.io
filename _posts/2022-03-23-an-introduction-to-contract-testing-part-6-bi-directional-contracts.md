---
title: An introduction to contract testing - part 6 - bi-directional contracts
layout: post
permalink: /an-introduction-to-contract-testing-part-6-bi-directional-contracts/
categories:
  - consumer driven contract testing
tags:
  - contract testing
  - pact
  - api
---
_In this series of articles, you'll be introduced to a (fictitious but realistic) use case for consumer-driven contract testing with [Pact](https://docs.pact.io?utm_source=partner&utm_campaign=on-test-automation&utm_content=blog-bidirectional-contracts){:target="_blank"} and [Pactflow](https://pactflow.io?utm_source=partner&utm_campaign=on-test-automation&utm_content=blog-bidirectional-contracts){:target="_blank"}. Over 6 articles, you'll read about:_

* _[The different parties that play a role in this use case and the challenges that integration and end-to-end testing pose for them](/an-introduction-to-contract-testing-part-1-meet-the-players/)_
* _[How contract testing can address these challenges](/an-introduction-to-contract-testing-part-2-introducing-contract-testing/)_
* _[How to use Pact for consumer-driven contract testing](/an-introduction-to-contract-testing-part-3-getting-started-with-pact/)_
* _[How to make contract testing an integral part of an automated development and delivery workflow](/an-introduction-to-contract-testing-part-4-automating-the-workflow/)_
* _[What the effect is of changes in the expectations and implementations of the consumer and provider parties](/an-introduction-to-contract-testing-part-5-adapting-to-changes/)_
* _How to invite new parties to the contract testing ecosystem and how bidirectional contracts can make this a smooth process (**this article**)_

_All code samples that are shown and referenced in these articles can be found [on GitHub](https://github.com/basdijkstra/introduction-to-contract-testing){:target="_blank"}._

In the previous blog posts in this series on consumer-driven contract testing and Pact, we were introduced to CDCT, saw how to use Pact to support CDCT, how to automate the process and make it part of a CI/CD pipeline, and saw what happened when expectations change on the consumer side.

In this sixth and final post, we are going to see how to add a new service to the CDCT process, and how an approach called bi-directional contract testing can make that much easier.

**Disclaimer**: _Bi-drectional contract testing is a feature that is exclusive to Pactflow and is not available in the OSS Pact Broker._

### Our new player: the payment provider service
To ensure a smooth and seamless order completion service, our sandwich shop has decided to enable customer to make payments for their orders online through a third party payment provider. This payment service is consumed by the order service we have already seen in the previous blog posts, meaning that our architecture now looks like this:

![contract testing players_updated](/images/blog/contract_testing_players_updated.png "A new player has been introduced to our contract testing case")

To ensure that the integration between the order service (consumer) and the payment service (provider) keeps working, the order service team would like to add contract tests for this integration, in the same fashion as is used in the integration with the address service.

However, the team responsible for developing and delivering the payment service is not keen to adopt Pact, as they feel it would be too intrusive to their current development and testing approach.

According to the team behind Pactflow, this is actually a common drawback for a lot of teams thinking about adopting CDCT: implementing Pact in the way we have seen in the previous articles requires significant effort on both the consumer and the provider end.

New dependencies need to be added to the code base, pact definitions and tests need to be written, additional steps need to be added to the build pipeline, and so on. This is what keeps a lot of teams from doing contract testing in the first place.

Recently, the Pactflow team launched a solution to overcome this challenge and make it easier for teams to get started with contract testing. Enter [bi-directional contract testing](https://pactflow.io/blog/bi-directional-contracts?utm_source=partner&utm_campaign=on-test-automation&utm_content=blog-bidirectional-contracts){:target="_blank"}.

### Bi-directional contract testing
In 'traditional' CDCT, a consumer generates a contract using Pact, then distributes that contract to a provider for verification using a Pact Broker. The provider takes the contract, runs the verifications and uploads the verification results back to the Pact Broker. Using [can-i-deploy](https://docs.pact.io/pact_broker/can_i_deploy?utm_source=partner&utm_campaign=on-test-automation&utm_content=blog-bidirectional-contracts){:target="_blank"}, both the consumer and the provider can then check whether or not it is safe to deploy a new version to production.

[Bi-directional contract testing](https://docs.pactflow.io/docs/bi-directional-contract-testing?utm_source=partner&utm_campaign=on-test-automation&utm_content=blog-bidirectional-contracts){:target="_blank"}, or BDCT, uses a different flow: in BDCT, both the consumer and the provider generate their own version of a contract, which they both upload to Pactflow, which then compares the two contracts for compatibility.

Once that is done, both the consumer and the provider can then use can-i-deploy again before they deploy to production to see if there aren't any potential integration issues.

And to make it easier to generate contracts, BDCT does not require a 'full' implementation of Pact. Instead, you can leverage existing tests, tools and specifications and upgrade them into a contract testing solution.

The [Pact documentation](https://docs.pactflow.io/docs/bi-directional-contract-testing?utm_source=partner&utm_campaign=on-test-automation&utm_content=blog-bidirectional-contracts){:target="_blank"} does a great job of explaining all the nuts and bolts, as well as listing all supported tools and technologies, so I'm not going to repeat all the details here. Instead, let's look at an example.

### The consumer side - generating a contract from WireMock mocks
To test the payment process for sandwich orders, the order service team already uses [WireMock](https://wiremock.org/){:target="_blank"} to mock out the payment service provider. Since WireMock is one of the tools that is already supported by the Pactflow team for BDCT purposes, the behaviour that is mocked with WireMock can be used to generate a contract on the consumer side.

In this example, we'll look at HTTP GET operations that retrieve payment details for a specific order ID. The process is the same for other operations (e.g., POST for submitting a payment for an order).

This is what the test looks like for a successful retrieval of payment details:

{% highlight java %}
private static final UUID ID = UUID.fromString("8383a7c3-f831-4f4d-a0a9-015165148af5");
private static final UUID ORDER_ID = UUID.fromString("228aa55c-393c-411b-9410-4a995480e78e");
private static final String STATUS = "payment_complete";
private static final int AMOUNT = 42;
private static final String DESCRIPTION = String.format("Payment for order %s", ORDER_ID);

@Autowired
private WireMockServer wireMockServer;

@Test
public void getPayment_validOrderId_shouldYieldExpectedPayment() {

	Payment payment = new Payment(ID, ORDER_ID, STATUS, AMOUNT, DESCRIPTION);

	String paymentAsJson = new Gson().toJson(payment);

	wireMockServer.stubFor(WireMock.get(WireMock.urlEqualTo(String.format("/payment/%s", ORDER_ID)))
			.willReturn(aResponse().withHeader("Content-Type", MediaType.APPLICATION_JSON_VALUE)
					.withBody(paymentAsJson)));

	Payment responsePayment = new PaymentServiceClient(wireMockServer.baseUrl())
            .getPaymentForOrder(ORDER_ID.toString());

	assertThat(responsePayment.getId()).isEqualTo(ID);
	assertThat(responsePayment.getOrderId()).isEqualTo(ORDER_ID);
	assertThat(responsePayment.getStatus()).isEqualTo(STATUS);
	assertThat(responsePayment.getAmount()).isEqualTo(AMOUNT);
	assertThat(responsePayment.getDescription()).isEqualTo(DESCRIPTION);
}
{% endhighlight %}

Similar tests exist for the situation where no payment is found for an order (this yields an HTTP 404 and no response body) and for the situation where the order ID supplied is invalid (this yields an HTTP 400, also without a response body).

To generate a BDCT consumer contract from these tests and mocks, the Pactflow team has provided the `wiremock-pact-generator` [library](https://docs.pactflow.io/docs/bi-directional-contract-testing/tools/wiremock?utm_source=partner&utm_campaign=on-test-automation&utm_content=blog-bidirectional-contracts){:target="_blank"}. After adding this as a dependency to the project, the only thing we need to change in our test code is adding the `WireMockPactGenerator` as a listener to the WireMock instance:

{% highlight java %}
@BeforeAll
void configureWiremockPactGenerator() {

	wireMockServer.addMockServiceRequestListener(
			WireMockPactGenerator
					.builder("order_consumer", "payment_provider")
					.build());
}
{% endhighlight %}

As you can see, when adding the `WireMockPactGenerator`, the only thing we need to supply is an identification for the consumer (`order_consumer`) and the provider (`payment_provider`) and we're good to go.

When we run the tests that invoke the WireMock instance with the Pact listener, a contract is generated and placed in the `/target/pacts` folder, just as we've seen in 'traditional' CDCT. The contract does look a little different, though, especially where it specifies the expectations around the response body in case of a successful retrieval of payment details:

{% highlight json %}
"response": {
    "status": 200,
    "headers": {
        "content-type": "application/json"
    },
    "body": {
        "id": "8383a7c3-f831-4f4d-a0a9-015165148af5",
        "orderId": "228aa55c-393c-411b-9410-4a995480e78e",
        "status": "payment_complete",
        "amount": 42,
        "description": "Payment for order 228aa55c-393c-411b-9410-4a995480e78e"
    }
}
{% endhighlight %}

As you can see, the contract does not explicitly specify that response body elements returned by the provider should match the examples given in the contract only on type (for example), where this is the case in 'traditional' CDCT contracts.

In BDCT, as explained before, the verification is done by Pactflow, not by the provider (as they upload their own contract), and schema-based verification (i.e., verification on data type or shape) is the _only_ type of verification that is performed.

In other words, Pactflow compares the shape of the expected response (provided by the consumer) with the shape of the actual response (provided by the provider) and reports on inconsistencies when it finds them.

This has a couple of interesting benefits (note that these are a direct quote from an email from [Matt](https://au.linkedin.com/in/digitalmatt){:target="_blank"} and the extremely helpful Pactflow team):

1. You can create more scenarios that you normally would (because more examples won't increase the burden on the provider test). This is helpful because we've found lots of people get a bit confused about the specific scope of contract tests, and sometimes get a little too hung up on it.
2. Consumers can add new expectations on a provider, and if they comply with the current known provider contract, they can deploy without waiting for a new provider verification. In fact, you can add a brand new consumer and if they only consume a subset of the provider API, they can do the whole thing without a provider even knowing!
3. Provider states disappear - whilst a powerful concept, it's probably the thing newbies struggle with the most. So this workflow seems to be much easier for people to grasp coming at it with fresh eyes.
4. Following from (2), CI pipelines are simpler. Because the provider simply uploads its API description, there is no need to trigger webhooks to get provider verification results and orchestrate pipelines. Triggering a provider build won't provide any new information.

Once the contract is generated from the WireMock-based tests on the consumer side, they can be uploaded in the same way as a 'traditional' contract:

`mvn pact:publish`

When we take a look at Pactflow, we can see that a new integration between the order_consumer and the payment_provider has been added, and that it is as of yet unverified:

![pact_broker_unverified_bdct_contract](/images/blog/pact_broker_unverified_bdct_contract.png "An unverified contract as uploaded to the Pact Broker while using the BDCT approach")

That is it from the consumer end. Let's now move to the provider side!

### The provider side - using an existing OAS as a contract
With BDCT now being an option for fast implementation of contract tests, providers can reuse existing service specifications as their contract for verification by Pactflow. Currently, only [OpenAPI specifications](https://docs.pactflow.io/docs/bi-directional-contract-testing/contracts/oas?utm_source=partner&utm_campaign=on-test-automation&utm_content=blog-bidirectional-contracts){:target="_blank"} (OAS) are supported, but as this is a very common specification standard, a lot of providers may already benefit from this option.

It is good to note that the provider will likely already have tests that verify that the provider endpoints fulfill the expectations in the API specification (the OAS). These test results can be uploaded together with the OAS as additional proof.

Typically, a provider CI pipeline will execute these functional tests before publishing the contract / the OAS, which means that failing tests will lead to no contract being published to Pactflow.

At the moment of writing this, uploading of the OAS to Pactflow has to be done using a somewhat arcane API call, but the Pactflow team is working on including this in the Pact CLI tools, which will undoubtedly make the process a lot easier.

Here's the PUT call currently required to upload the provider contract (as you can see, I used Postman, but cUrl or any other API client should work, too):

```
PUT /contracts/provider/<<PROVIDER_NAME>>/version/<<PROVIDER_VERSION>> HTTP/1.1
Content-Type: application/json
Authorization: Bearer <<YOUR TOKEN GOES HERE>>
User-Agent: PostmanRuntime/7.26.8
Accept: */*
Cache-Control: no-cache
Postman-Token: d2ea82f0-5335-485a-834b-01a01fa2d0e0
Host: ota.pactflow.io
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
Content-Length: 2569
{
    "content": "<<BASE64 ENCODED YML FILE GOES HERE>>",
    "contractType": "oas",
    "contentType": "application/yaml",
    "verificationResults": {
        "success": true,
        "content": "<<BASE64 ENCODED ADDITIONAL TEST RESULTS GO HERE>>",
        "contentType": "text/plain",
        "verifier": "verifier"
    }
}
```

If all went well, the response status code is HTTP 201, which indicates that the provider contract was successfully uploaded to Pactflow. When we refresh the screen, we can see that not only did Pactflow receive the contract, it also compared the consumer and the provider contract for us:

![pact_broker_successfully_verified_bdct_contract](/images/blog/pact_broker_successfully_verified_bdct_contract.png "A successfully verified contract in the Pact Broker using the BDCT approach")

In this case, the consumer contract (generated from WireMock mock API specifications) and the provider contract (the OAS) are compatible, meaning that the `order_consumer` and the `payment_provider` should be able to communicate with one another. Both the consumer and the provider can use can-i-deploy to determine the current contract verification status before deploying into production.

As you can see from this example, BDCT is a very powerful way of starting with contract testing. Because BDCT leverages existing tools and specifications, not a lot is needed to get your first contracts uploaded and verified, compared to traditional 'full-blown' CDCT. As more tools and specification standards will start to see support from Pact, BDCT will likely increase in popularity.

So, which approach should you choose? BDCT or CDCT?

As usual, the only sensible answer here is 'it depends'. I'll leave you with [this comparison](https://docs.pactflow.io/docs/bi-directional-contract-testing/#comparison-to-pact?utm_source=partner&utm_campaign=on-test-automation&utm_content=blog-bidirectional-contracts){:target="_blank"}, written by the Pactflow team itself.

This concludes the blog post series on CDCT and Pact. I'm pretty sure I'll keep diving in and learning more about CDCT, BDCT and Pact in the future, so there might be more blog posts on specific topics in this area coming up, but these six articles should give you a good introduction into the concepts of consumer-driven contract testing, bi-directional contract testing and the tooling that supports these approaches.

And as always, if you have any questions or remarks, leave a comment here or [contact me directly](/contact/).

All code you've seen in this blog post can be found [on GitHub](https://github.com/basdijkstra/introduction-to-contract-testing/tree/article6){:target="_blank"}.