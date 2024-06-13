---
title: Using the client-test model in RestAssured.Net
layout: post
permalink: /using-the-client-test-model-in-rest-assured-net/
categories:
  - API testing
tags:
  - RestAssured.Net
  - C#
---
A couple of weeks ago, a blog post from fellow automation engineer [Kwo Ding](https://nl.linkedin.com/in/kwoding){:target="_blank"} appeared on my LinkedIn feed. In his blog post, Kwo proposed using what he refers to as a 'client-test' model to improve separation of concerns when writing tests for HTTP-based APIs in [REST Assured](https://rest-assured.io){:target="_blank"}. [You can find the original blog post here](https://testingboss.com/blog/client-test-model-for-rest-api-testing/){:target="_blank"}.

I really like the idea and model proposed by Kwo here, and I think it's a great step forward in improving readability and maintainability of your API test code. Being the developer of [RestAssured.Net](https://github.com/basdijkstra/rest-assured-net){:target="_blank"}, a C# port of REST Assured, I was also immediately curious to see if and how well this model can be used in this library, too.

Short answer: you can.

Longer answer: you can, and here's how.

I'm replicating Kwo's example pretty much verbatim here, so I'm writing a test that can be used to verify creation of a new `Contact`, which is a simple class representing a contact, with a first name, a last name and an email address as properties.

As in Kwo's example, we'll start building an abstract `ClientBase` that contains common configuration that can be shared with multiple clients:

{% highlight csharp %}
public abstract class ClientBase
{
    private Uri baseUri;

    protected ClientBase(Uri baseUri)
    {
        this.baseUri = baseUri; 
    }

    public RequestSpecification RequestSpec()
    {
        return new RequestSpecBuilder()
            .WithScheme(this.baseUri.Scheme)
            .WithHostName(this.baseUri.Host)
            .WithBasePath(this.baseUri.AbsolutePath)
            .WithPort(this.baseUri.Port)
            .WithRequestLogLevel(RequestLogLevel.All)
            .Build();
    }
}
{% endhighlight %}

This common base class can then be used to create a `ContactClient` that's specific for operations regarding contacts, e.g., to retrieve a specific contact using an HTTP GET to `/contact/{id}`, or to create a new contact with an HTTP POST to `/contact`:

{% highlight csharp %}
public class ContactClient : ClientBase
{
    private static readonly Uri BaseUri = new Uri("http://localhost:9876/api/v2");

    public ContactClient() : base(BaseUri)
    {
    }

    public VerifiableResponse CreateContact(Contact contact)
    {
        return Given()
            .Spec(base.RequestSpec())
            .ContentType("application/json")
            .Body(contact)
            .When()
            .Post("/contact");
    }

    public VerifiableResponse GetContact(string contactId)
    {
        return Given()
            .Spec(base.RequestSpec())
            .When()
            .Get($"/contact/{contactId}");
    }
}
{% endhighlight %}

With all the HTTP client configuration out of the way, our actual test method can focus on creating the required payload, performing the operation and verifying the response:

{% highlight csharp %}
private readonly ContactClient contactClient = new ContactClient();

[Test]
public void CanUseClientTestModel()
{
    Contact contact = new ContactBuilder()
        .WithFirstName("John")
        .WithLastName("Doe")
        .WithEmail("john@doe.com")
        .Build();

    this.contactClient
        .CreateContact(contact)
        .Then()
        .StatusCode(201);
}
{% endhighlight %}

From the above, we can conclude two things about the client-test model, as originally described by Kwo:

* it is a great way to separate concerns in your API tests and make them easier to read and to maintain
* it can be implemented using RestAssured.Net just as easily as in the Java REST Assured

P.S.: if you change the signature of the `CreateContact()` method in the `ContactClient` to

{% highlight csharp %}
public VerifiableResponse CreateContact(object contact)
{% endhighlight %}

you can also pass in an anonymous object instead of creating a strongly typed `Contact` instance:

{% highlight csharp %}
[Test]
public void CanUseClientTestModelWithAnonymousObject()
{
    var contact = new
    {
        FirstName = "John",
        LastName = "Doe",
        Email = "john@doe.com"
    };

    this.contactClient
        .CreateContact(contact)
        .Then()
        .StatusCode(201);
}
{% endhighlight %}

Pretty nice! What do you think?

All code snippets above can be found on [my GitHub](https://github.com/basdijkstra/rest-assured-net-client-test){:target="_blank"}.