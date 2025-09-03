---
title: Mutation testing - not just for unit tests
layout: post
permalink: /mutation-testing-not-just-for-unit-tests/
categories:
  - Mutation testing
tags:
  - java
  - pit
  - rest-assured
---
I wrote about mutation testing [a few](/improving-the-tests-for-rest-assured-net-with-mutation-testing-and-stryker-net/) [times on](/testing-your-tests/) [this blog](/an-introduction-to-mutation-testing-and-pit/), and I even have [a mutation testing workshop](/training/mutation-testing/) that I run on a pretty frequent basis.

One misconception that pops up sometimes, either explicitly in a question or implicitly when I hear others talk about mutation testing, is that mutation testing only works for unit tests. That's not true. You can use mutation testing to find out more about the quality of other types of tests, too.

In this blog post, I'll show you an example using a code base that I'm using in my mutation testing workshops as well. I'll link to the codebase at the end of this blog post, so you can have a look and try it out for yourself, too.

Keep in mind that while mutation testing isn't _just_ for unit tests, it works best for tests that run quickly. Your test suite will be run for every mutation in the product code that your mutation testing tool generates, so if you don't want to wait hours or even days for your mutation testing results, you'd be wise to use it with tests that run _fast_, that is, with an execution time measured in milliseconds, maybe a second or two tops.

### Our application under test

To demonstrate that mutation testing works for other types of tests, too, I wrote an API in Java using Spring Boot, complete with a controller, service and repository layers, connecting to [an H2 in-memory database](https://www.baeldung.com/spring-boot-h2-database){:target="_blank"}.

The API allows you to create, retrieve and delete bank account instances, as well as perform different types of transactions: deposit into an account, withdraw from an account and add interest to an account balance. Here's the implementation of the deposit operation in the controller class and the corresponding service layer to give you an idea what that looks like:

{% highlight java %}
// From AccountController.java
@PostMapping(path = "/{id}/deposit/{amount}", consumes = MediaType.APPLICATION_JSON_VALUE, produces = MediaType.APPLICATION_JSON_VALUE)
public ResponseEntity<Account> depositToAccount(@PathVariable("id") Long id, @PathVariable("amount") double amount) {

    return ResponseEntity.status(HttpStatus.OK).body(accountService.processDeposit(id, amount));
}
{% endhighlight %}

{% highlight java %}
// From AccountService.java
@Transactional
public Account processDeposit(Long id, double amount) {

    if (amount <= 0) {
        throw new BadRequestException("Amount must be greater than 0, but was " + amount);
    }

    var accountPersisted = getAccount(id);

    accountPersisted.setBalance(accountPersisted.getBalance() + amount);
	
    return accountRepository.save(accountPersisted);
    }
{% endhighlight %}

Of course, there's a corresponding set of tests to verify the implementation of this API. Again, because I want to demonstrate that mutation testing doesn't just work for unit tests, I'm using a set of acceptance tests written using [REST Assured](https://rest-assured.io){:target="_blank"}. As these tests spin up a local instance of the API, including the database, in memory on the machine that is running the tests, test execution time is in the order of milliseconds.

Here's an example of such a test. Note that I've abstracted away the raw REST Assured code in a client to make the tests easier to read, write and maintain.

{% highlight java %}
@Test
public void depositIntoCheckingAccount_whenRetrieved_shouldShowUpdatedBalance() {

    // Create a new checking account
    AccountDto account = new AccountDto(AccountType.CHECKING);
    int accountId = this.accountClient.createAccount(account);

    // Deposit 10 dollars / euros / smurfs into the account
    Response response = this.accountClient.depositToAccount(accountId, 10);

    // Check that the deposit is processed correctly and that the balance is updated
    Assertions.assertEquals(200, response.getStatusCode());
    Assertions.assertEquals(10.0F, (Float) response.path("balance"));
}
{% endhighlight %}

For clarity, the `createAccount()` method in the client class making the actual HTTP POST call to create a new account looks like this:

{% highlight java %}
public int createAccount(AccountDto account) {

    return given()
        .spec(super.requestSpec())
        .body(account)
        .post("/account")
        .then()
        .statusCode(201)
        .extract().path("id");
}
{% endhighlight %}

In a similar way, the `depositToAccount()` method makes another HTTP call using REST Assured. Again, feel free to have a look at the codebase yourself after reading this blog post to better understand the structure of the code.

### Running the tests and running mutation testing

In this example, I'm using [PIT](https://pitest.org/){:target="_blank"} as the mutation testing tool. First, I'm running the tests to verify that they all pass, as there's no point in finding out the mutation score of failing tests. The command I use in this example is `mvn clean test`.

{% highlight console %}
[INFO] Tests run: 9, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 7.847 s -- in com.ontestautomation.mutationbank.MutationBankApplicationTests
[INFO]
[INFO] Results:
[INFO]
[INFO] Tests run: 9, Failures: 0, Errors: 0, Skipped: 0
[INFO]
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  13.518 s
[INFO] Finished at: 2025-09-03T15:31:22+02:00
[INFO] ------------------------------------------------------------------------
{% endhighlight %}

Next, we run mutation testing using PIT, using the command `mvn org.pitest:pitest-maven:mutationCoverage`:

{% highlight console %}
================================================================================
- Statistics
  ================================================================================
>> Line Coverage (for mutated classes only): 51/59 (86%)
>> Generated 55 mutations Killed 35 (64%)
>> Mutations with no coverage 6. Test strength 71%
>> Ran 66 tests (1.2 tests per mutation)

[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  56.060 s
[INFO] Finished at: 2025-09-03T15:35:34+02:00
[INFO] ------------------------------------------------------------------------
{% endhighlight %}

As you can see, our mutation testing run took less than a minute, which is very acceptable. Granted, this is a very small codebase, but nonetheless our acceptance test suite (9 tests) were run no less than 55 times in less than a minute, which I think proves my point that mutation testing can be useful outside of unit tests, too.

### Looking at the reports

If we look at the mutation testing results and focus on the logic in the `processDeposit()` method and the mutants created by PIT therein, we see this:

![mutation_testing_before](/images/blog/mutation_testing_results_before.png "Mutation testing results - before")

Apparently, there's some business logic here that we're not adequately covering in our tests. More specifically, we're not testing that trying to deposit a non-positive amount into an account throws a `BadRequestException` (which in turn returns an HTTP 400).

### Adding a new test to improve our mutation testing score

To kill the mutants that so far survived, let's write a test that tries to deposit a non-positive amount into an account. I also want to cover the boundary value here, so I'll add a test that tries to deposit 0 dollars into the account:

{% highlight java %}
@Test
public void depositZeroIntoAccount_shouldReturnHttp400() {

    AccountDto account = new AccountDto(AccountType.CHECKING);
    int accountId = this.accountClient.createAccount(account);

    Response response = this.accountClient.depositToAccount(accountId, 0);

    Assertions.assertEquals(400, response.getStatusCode());

    Response getResponse = this.accountClient.getAccount(accountId);
    Assertions.assertEquals(0.0F, (Float) getResponse.path("balance"));
}
{% endhighlight %}

When we run the tests to confirm that this new test also passes (it does) and then run mutation testing again, we see that we're still under a minute for the entire mutation testing run, even though we now have more tests, 10 instead of 9 to be exact:

{% highlight console %}
================================================================================
- Statistics
  ================================================================================
>> Line Coverage (for mutated classes only): 52/59 (88%)
>> Generated 55 mutations Killed 37 (67%)
>> Mutations with no coverage 6. Test strength 76%
>> Ran 64 tests (1.16 tests per mutation)

[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  57.084 s
[INFO] Finished at: 2025-09-03T15:53:37+02:00
[INFO] ------------------------------------------------------------------------
{% endhighlight %}

Looking at the report, we can see that our mutation score for this part of our API code improved, too:

![mutation_testing_after](/images/blog/mutation_testing_results_after.png "Mutation testing results - after")

Definitely a step in the right direction, as we now have a test that does not just increase our code coverage, but we also know that it will fail when a developer accidentally changes the deposit logic and allow for depositing a zero or a negative amount into an account. Sweet!

### Try it out for yourself!

If you'd like to try out mutation testing using the codebase I used in this blog post yourself, it is available on GitHub. Everything is included: the API under test, the acceptance tests, and PIT, the mutation testing tool I used here.

If you'd rather have me come into your company or event to run a workshop based on what you've seen here, I'm happy to talk options. I can run the workshop in Java, but we can use C#, too.