---
title: Using data table types in Cucumber-JVM
layout: post
permalink: /using-data-table-types-in-cucumber-jvm/
categories:
  - behaviour driven development
tags:
  - java
  - cucumber
  - data tables
---
_In a blog post I wrote a while ago, [I gave some examples](/using-data-tables-in-cucumber-jvm-for-better-readable-specifications/) on how to specify data in Cucumber feature files in tables to make your specifications easier to read, and showed you how to parse the data in different table formats._

_At the end of that blog post, I promised to write a follow-up post to introduce the concept of data table transformers to deal with table data structures in Cucumber-JVM in a different and arguably more powerful way. It took me a while to get to it, but here goes._

I'm a big fan of [SpecFlow](https://specflow.org/){:target="_blank"}, the BDD framework for .NET. One of the features in SpecFlow that I like most are the [SpecFlow.Assist helpers](https://docs.specflow.org/projects/specflow/en/latest/Bindings/SpecFlow-Assist-Helpers.html){:target="_blank"}, which allow you to quickly transform tables in your specifications to (lists of) instances of C# objects, as well as to compare (lists of) objects to tables, all with a single call to a SpecFlow.Assist helper method.

In this blog post, I'll show you how to do something similar in Cucumber-JVM through the use of data table transformers.

### Our Gherkin specification and Java object definition
As an example, let's consider the following specification snippet, listing some details of well-known books:

{% highlight gherkin %}
Scenario: Listing book details
  Given the following books
    | title                           | author              | yearOfPublishing |
    | To kill a mockingbird           | Harper Lee          | 1960             |
    | The catcher in the rye          | J.D. Salinger       | 1951             |
    | The great Gatsby                | F. Scott Fitzgerald | 1925             |
{% endhighlight %}

We also need to define a Java object that represents a book, which we are going to call `Book`:

{% highlight java %}
@Data
@NoArgsConstructor
@AllArgsConstructor
public class Book {

    private String title;
    private String author;
    private int yearOfPublishing;
}
{% endhighlight %}

_Note that I'm using [Lombok](https://projectlombok.org/){:target="_blank"} annotations here to automatically generate getters and setters, as well as no-argument and all-argument constructors. Saves me a lot of typing. If you're not a Lombok fan, that's OK. Just add the constructor and getters and setters yourself._

### Transforming our table contents into Java objects
Since POJOs (Plain Old Java Objects) and Java beans are easier to deal with in code than the generic collection types we saw in the previous blog post (`Map<String, Map<String, Integer>>`, anyone?), it would be neat to have a mechanism available that could transform table rows in Gherkin specifications into Java objects, in a reusable and flexible way.

Enter the [data table type](https://github.com/cucumber/cucumber-jvm/tree/main/java#data-table-type){:target="_blank"}, accessed through the `@DataTableType` annotation. Here's an example `@DataTableType` definition that transforms the table rows in our Gherkin specification into instances of the Book class as defined above:

{% highlight java %}
@DataTableType
public Book bookEntryTransformer(Map<String, String> row) {

	return new Book(
		row.get("title"),
		row.get("author"),
		Integer.parseInt(row.get("yearOfPublishing"))
	);
}
{% endhighlight %}

Now that we've defined our custom data table type, we can use it to automatically transform the table in our specification into a list of `Book` instances:

{% highlight java %}
@Given("the following books")
public void theFollowingBooks(List<Book> books) {

	for(Book book: books) {	
	   System.out.printf(
			   "'%s', published in %d, was written by %s\n",
			   book.getTitle(),
			   book.getYearOfPublishing(),
			   book.getAuthor()
	   );
	}
}
{% endhighlight %}

As you can see, we no longer have to use the cumbersome `List<Map<String, String>>` data type we saw in the previous blog post, which makes it much easier to iterate over and process our table row entries.

Running our specification produces the following output, demonstrating that all data was read from the table and processed as expected:

```
'To kill a mockingbird', published in 1960, was written by Harper Lee
'The catcher in the rye', published in 1951, was written by J.D. Salinger
'The great Gatsby', published in 1925, was written by F. Scott Fitzgerald
```

### A side note: dealing with empty cells
Sometimes, while writing specifications in Gherkin, you want to explicitly specify that a cell contains an empty value. However, Cucumber-JVM by default interprets empty cells as `null` values, not as empty strings.

Luckily, there's a way around this: we can specify a placeholder value that is translated to an empty string when we define our `@DataTableType`, by passing it in using `replaceWithEmptyString` parameter:

{% highlight java %}
@DataTableType(replaceWithEmptyString = "[anonymous]")
public Book bookEntryTransformer(Map<String, String> row) {

	// The method body does not change, empty string conversion happens automatically
}
{% endhighlight %}

A Gherkin table row specified like this:

{% highlight gherkin %}
Scenario: Listing book details
  Given the following books
    | title                           | author              | yearOfPublishing |
    | The life of Lazarillo de Tormes | [anonymous]         | 1544             |
{% endhighlight %}

will produce the following output:

```
'The life of Lazarillo de Tormes', published in 1544, was written by
```

In other words, exactly what we expected (the author value is an empty string).

### Comparing Gherkin tables to Java object instances
Now that we have seen how to convert Gherkin tables to lists of Java objects, I'd like to briefly discuss another common way of using tables in Gherkin and the underlying automation code: comparing table data to a list of object instances. To demonstrate that, I've extended the scenario as follows:

{% highlight gherkin %}
Scenario: Listing book details
  Given the following books
    | title                           | author              | yearOfPublishing |
    | To kill a mockingbird           | Harper Lee          | 1960             |
    | The catcher in the rye          | J.D. Salinger       | 1951             |
    | The great Gatsby                | F. Scott Fitzgerald | 1925             |
    | The life of Lazarillo de Tormes | [anonymous]         | 1544             |
  When I do nothing
  Then I expect to have the following books
    | title                           | author              | yearOfPublishing |
    | The life of Lazarillo de Tormes | [anonymous]         | 1544             |
    | The great Gatsby                | F. Scott Fitzgerald | 1925             |
    | To kill a mockingbird           | Harper Lee          | 1960             |
    | The catcher in the rye          | J.D. Salinger       | 1951             |
{% endhighlight %}

Let's see if we can compare the two tables. As lists are unordered in Java, the fact that I have swapped around the order in which the books appear in the list should not make a difference in the comparison.

Here's a possible implementation of the _Then_ step (I think you can guess what happens in the _When_ step from the text itself...):

{% highlight java %}
@Then("I expect to have the following books")
public void iExpectToHaveTheFollowingBooks(List<Book> expectedBooks) {

	Assert.assertTrue(CollectionUtils.isEqualCollection(expectedBooks, books));
}
{% endhighlight %}

The only thing we need to do to compare all the data is compare the `expectedBooks` list, passed to the _When_ step, with the actual lists of books `books` that we created earlier in the _Given_ step.

I chose to use the third-party `commons-collections4` [library](https://search.maven.org/artifact/org.apache.commons/commons-collections4/4.4/jar){:target="_blank"} to do the comparison for me. There are other ways to compare lists of Java objects for equality, and some alternatives to this approach [are listed here](https://www.techiedelight.com/compare-two-lists-for-equality-java/){:target="_blank"}. Please keep in mind that not all of them work on lists of complex objects.

When we run our scenario, the test passes, meaning that our tables are seen as equal. Indeed, changing one of the cell values in one of the tables (but not the other) makes the test fail.

Unfortunately, other than an `AssertionError`, this approach does not give more specific details about the row and the specific cell that was the culprit, but other comparison approaches might give you the details you're looking for (which happens out of the box [in SpecFlow.Assist](https://blog.testproject.io/2019/11/13/working-effectively-with-specflow-tables/){:target="_blank"}, by the way).

As usual, all the code shown in this blog post [can be found on GitHub](https://github.com/basdijkstra/ota-examples/tree/master/cucumber-data-tables){:target="_blank"}.