---
title: An introduction to contract testing - part 5 - adapting to changes
layout: post
permalink: /an-introduction-to-contract-testing-part-5-adapting-to-changes/
categories:
  - consumer driven contract testing
tags:
  - contract testing
  - pact
  - api
---
_In this series of articles, you'll be introduced to a (fictitious but realistic) use case for consumer-driven contract testing with [Pact](https://docs.pact.io?utm_source=partner&utm_campaign=on-test-automation&utm_content=blog-adapting-to-changes){:target="_blank"} and [Pactflow](https://pactflow.io?utm_source=partner&utm_campaign=on-test-automation&utm_content=blog-adapting-to-changes){:target="_blank"}. Over 6 articles, you'll read about:_

* _[The different parties that play a role in this use case and the challenges that integration and end-to-end testing pose for them](/an-introduction-to-contract-testing-part-1-meet-the-players/)_
* _[How contract testing can address these challenges](/an-introduction-to-contract-testing-part-2-introducing-contract-testing/)_
* _[How to use Pact for consumer-driven contract testing](/an-introduction-to-contract-testing-part-3-getting-started-with-pact/)_
* _[How to make contract testing an integral part of an automated development and delivery workflow](/an-introduction-to-contract-testing-part-4-automating-the-workflow/)_
* _What the effect is of changes in the expectations and implementations of the consumer and provider parties (**this article**)_
* _How to invite new parties to the contract testing ecosystem and how bidirectional contracts can make this a smooth process_

_All code samples that are shown and referenced in these articles can be found [on GitHub](https://github.com/basdijkstra/introduction-to-contract-testing){:target="_blank"}._

When we wrapped up [the previous article](/an-introduction-to-contract-testing-part-4-automating-the-workflow/), all was fine on the integration testing front: our Address provider was able to fulfill expectations from both the Customer and the Order consumers. We then successfully made generating the contracts and publishing them part of the consumer pipeline. We also made downloading and verifying them and publishing the verification results part of the provider pipeline.

As you know, however, systems are always changing, with new requirements and features added frequently. In this article, we'll see two different requirements being implemented on the consumer side and then see how this affects contract verification results on the provider side, as well as a suggestion on how to deal with any integration issues coming to light through contract testing.

### Adding a new field: support for PO Boxes
Customers of our online sandwich store have requested that they can list a PO Box as their billing address. For obvious reasons, these will not be used as delivery addresses! In other words, the addresses that our Customer consumer expects now also have a PO Box field. This field should be an integer, with a value of 0 if no PO Box number is known.

Remember: we don't really care about the actual value of the number, as that is implementation of business logic and therefore should be covered by the tests on the provider side, and the ability to process these values should be covered by the tests on the consumer side. Contract testing plays no role in this, only in verifying that the field is actually there and that its value is an integer.

The updated address payload should now look similar to this:

{% highlight json %}
{
    "id": "87256abc-f6b3-4e91-9f60-3ca3f54863d5",
    "address_type": "billing",
    "street": "Main Street",
    "number": 123,
    "poBox": 9876,
    "city": "Nothingville",
    "zip_code": 54321,
    "state": "Tennessee",
    "country": "United States"
}
{% endhighlight %}

and the updated response body expectations in Pact like this:

{% highlight java %}
DslPart body = LambdaDsl.newJsonBody((o) -> o
    .uuid("id", ID)
    .stringType("addressType", ADDRESS_TYPE)
    .stringType("street", STREET)
    .integerType("number", NUMBER)
    .integerType("poBox", POBOX)  // <-- this one is new
    .stringType("city", CITY)
    .integerType("zipCode", ZIP_CODE)
    .stringType("state", STATE)
    .stringType("country", COUNTRY)
).build();
{% endhighlight %}

Naturally, we also update the unit test that verifies that our Customer consumer can correctly process the updated response. We do so by adding this assertion to the existing test:

{% highlight java %}
assertThat(address.getPoBox()).isEqualTo(POBOX);
{% endhighlight %}

That's it, from the perspective of our Customer consumer. Running the tests again shows that all tests pass, and when we expect the updated contract generated, we see that an additional expectation is added for the new `poBox` field:

{% highlight json %}
"$.poBox": {
  "combine": "AND",
  "matchers": [
    {
      "match": "integer"
    }
  ]
}
{% endhighlight %}

When we publish the new contract for the Address provider to verify their implementation against, we see that it cannot fulfill the new expectation:

```
[ERROR]   Run 3: ContractTest
Failures:

1) A request for address data has a matching body

   1.1) body: $ Actual map is missing the following keys: poBox

        {
          "addressType": "billing",
          "city": "Nothingville",
          "country": "United States",
          "id": "8aed8fad-d554-4af8-abf5-a65830b49a5f",
          "number": 123,
          "poBox": 9876,
          "state": "Tennessee",
          "street": "Main Street",
          "zipCode": 54321
        }
```

As you can see, the Pact output clearly states that the actual response returned by the provider does not included the expected `poBox` field, telling us that there's an integration conflict.

After some discussion in Team Address (the team responsible for developing, testing and running the Address provider service), they decide to add the `poBox` field to the response sent back to the Customer and Order consumers. This makes the contract verification tests pass again:

```
[INFO] Results:
[INFO]
[INFO] Tests run: 10, Failures: 0, Errors: 0, Skipped: 0
[INFO]
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  15.919 s
[INFO] Finished at: 2021-11-03T10:31:27+01:00
[INFO] ------------------------------------------------------------------------
```

All is well again in contract testing land. At least, for a while...

### Expanding the market: support for Canadian zip codes
Due to an overwhelming demand for their sandwiches in Canada, our web shop decides to start supporting Canadian delivery addresses. For tax reasons, billing addresses are (for now) restricted to US addresses only.

To support this, the Order consumer team changes their expectations for the `zipCode` field to be a `String` field rather than an integer field, as Canadian zip codes are alphanumeric rather than numeric. They decide against using a regex matcher, to accommodate for future zip code types beyond the US and Canada:

{% highlight java %}
DslPart body = LambdaDsl.newJsonBody((o) -> o
    .uuid("id", ID)
    .stringType("addressType", ADDRESS_TYPE)
    .stringType("street", STREET)
    .integerType("number", NUMBER)
    .stringType("city", CITY)
    .stringType("zipCode", ZIP_CODE)  // <-- this one changed
    .stringType("state", STATE)
    .stringType("country", COUNTRY)
).build();
{% endhighlight %}

They update their unit tests accordingly and run them to produce an updated contract.

When we publish the new contract again for the Address provider to verify their implementation against, we see that it once more cannot fulfill the new expectation:

```
[ERROR]   Run 3: ContractTest
Failures:

1) A request for address data has a matching body

   1.1) body: $.zipCode Expected 54321 (Integer) to be the same type as "54321" (String)
```

If we automatically publish the contracts and the verification results to our [Pactflow Pact Broker](https://pactflow.io/?utm_source=partner&utm_campaign=on-test-automation&utm_content=blog-adapting-to-changes){:target="_blank"}, as we've seen in the previous article (LINK), we can see the same error appearing in the Pact Broker, too:

![broken contracts](/images/blog/pact_broker_displaying_integration_error.png "The Pactflow Broker displaying the integration error")

Unlike the previous change, however, the Address provider cannot simply update its implementation to fulfill the expectations of both the Customer and the Order consumers. One expects the zip code to be in a String format, the other one expects an integer, but the Address provider can really only return one of them, not both!

This means that a discussion needs to be started, involving all three parties, about what is the best way forward. This is a prime example of the types of integration issues, originating in one team but affecting other teams, that contract testing can bring to light.

In the sixth and final article, we'll have a look at introducing another party to the contract testing process, and how the Pact and Pactflow ecosystem make adopting consumer-driven contract testing easier by means of [bidirectional contracts](https://pactflow.io/blog/bi-directional-contracts/?utm_source=partner&utm_campaign=on-test-automation&utm_content=blog-adapting-to-changes){:target="_blank"}.

All code used in this blog post can be found [here](https://github.com/basdijkstra/introduction-to-contract-testing/tree/article5){:target="_blank"}.