---
title: Using data tables in Cucumber-JVM for better readable specifications
layout: post
permalink: /using-data-tables-in-cucumber-jvm-for-better-readable-specifications/
categories:
  - behaviour driven development
tags:
  - java
  - cucumber
  - data tables
---
If you have ever worked in a team that practiced BDD and used Cucumber or SpecFlow to create executable specifications, you'll know that writing scenarios that are readable is hard. As in: really, really hard.

In this blog post, I want to take a closer look at a feature of the Java Cucumber bindings that can help you write specs that are easy on the eye, and that I feel isn't talked about often enough: using data tables.

Data tables are tables that you can pass as an argument to an individual step. The data in this data table can then be processed in the underlying step definition. As such, data tables should not be confused with [example tables](https://cucumber.io/docs/gherkin/reference/#examples){:target="_blank"}, which are tables containing examples for entire scenarios. Data tables, on the other hand, allow you to use more complex data structures as an argument to an individual step.

Let's take a look at a couple of examples of the use of different forms of data tables, and how they compare to specifying the same data in more verbose textual format.

#### Example 1 - Key-value pairs
As a first example, let's consider the situation where you'll need to specify some sort of initial state that consists of multiple objects ('things') that can be modeled as a simple key-value pair. Some examples could be:

* currencies and their associated currency code (USD, EUR, etc.)
* airports and their IATA location identifier (LAX, JFK, etc.)
* employee names and their associated employee number

or, in the example below, football clubs and the stadium they play their home games at. Often, I see teams model these in their specifications like this:

{% highlight gherkin %}
Scenario: Listing football club stadiums - the verbose way
    Given Juventus play their home games at Allianz Stadium
    And AC Milan play their home games at San Siro
    And AS Roma play their home games at Stadio Olimpico
{% endhighlight %}

and the step definition associated with these steps:

{% highlight java %}
@Given("^(.*) play their home games at (.*)$")
public void club_play_their_home_games_at_stadium(String club, String stadium) {
    System.out.printf("%s play their home games at %s%n", club, stadium);
}
{% endhighlight %}

While this works, technically, I believe there are two inherent problems with this example. First, it is tedious to read because of the repeated text. Second, we're using three steps (a `Given` and two `And`s) to specify a single initial state, which to me feels counterintuitive. As a whole, this kind of specification reminds me a little too much of [this iconic movie scene](https://www.youtube.com/watch?v=CkdyU_eUm1U){:target="_blank"}..

There's a much better way to include the same data in your specification, and that is by using a data table. The same specification can be rewritten as

{% highlight gherkin %}
Scenario: Listing football club stadiums - the clear way
    Given the following clubs and their stadiums
      | Juventus | Allianz Stadium |
      | AC Milan | San Siro        |
      | AS Roma  | Stadio Olimpico |
{% endhighlight %}
<sup><sub>Please note that the first row does not contain table headers, even though the syntax highlighting suggests it does!</sub></sup>

and the associated step definition might look like this:

{% highlight java %}
@Given("the following clubs and their stadiums")
public void the_following_clubs_and_their_stadiums(Map<String, String> stadiums) {
    stadiums.forEach((club, stadium) ->
        System.out.printf("%s play their home games at %s%n", club, stadium)
    );
}
{% endhighlight %}

As you can see, Cucumber is able to automatically convert the data table that is passed as an argument to the `@Given` step to a `Map`, which is essentially a collection of key-value pairs. You can then iterate over the key-value pairs in the `Map` using a `forEach()` construct and process each entry as required for your acceptance test.

Running this example yields the following output:

```
Juventus play their home games at Allianz Stadium
AC Milan play their home games at San Siro
AS Roma play their home games at Stadio Olimpico
```

#### Example 2 - Multi-column tables
What about the situations where you want to model data entities that have more attributes, and therefore cannot be simply represented as a key-value pair? Think of entities such as:

* Addresses (a combination of a street name, a house number, a zip code, a city, ...)
* Flight information (a combination of a flight number, an airline, an airport of origin, a destination airport, ...)
* Bank transfers (a combination of a date, an amount, a source bank account, a destination bank account, ...)

or, in this example, data related to specific players of a football club. We could choose to specify this data as:

{% highlight gherkin %}
Scenario: Listing football squad players - the verbose way
    Given Cristiano Ronaldo of Portugal, born on 05-02-1985, plays for Juventus since the 2018/2019 season
    And Matthijs de Ligt of the Netherlands, born on 12-08-1999, plays for Juventus since the 2019/2020 season
    And Giorgio Chiellini of Italy, born on 14-08-1984, plays for Juventus since the 2005/2006 season
{% endhighlight %}

and implements these steps using a step definition method such as

{% highlight java %}
@Given("^(.*) of (.*), born on (.*), plays for Juventus since the (.*) season$")
public void name_of_country_born_on_date_plays_for_club_since_the_years_season(String name, String nationality, String dateOfBirth, String firstSeason) {
    System.out.printf("%s of %s, born on %s, plays for Juventus since the %s season%n", name, nationality, dateOfBirth, firstSeason);
}
{% endhighlight %}

This example, too, suffers from the same problems as the previous one. There's a lot of repetition, which makes the specification boring to read, and we need multiple steps to create an initial state. Let's do better!

The same state can be modeled with a data table that might look like this:

{% highlight gherkin %}
Scenario: Listing football squad players - the clear way
    Given the following Juventus players
      | name              | nationality     | dateOfBirth | atJuventusSince |
      | Cristiano Ronaldo | Portugal        | 05-02-1985  | 2018/2019       |
      | Matthijs de Ligt  | the Netherlands | 12-08-1999  | 2019/2020       |
      | Giorgio Chiellini | Italy           | 14-08-1984  | 2005/2006       |
{% endhighlight %}

Note that I decided to include table headers here, as this makes it much clearer which column corresponds to which attribute for a player. Using table headers is also really helpful in the associated step definition code, as we can see in this example implementing the step:

{% highlight java %}
@Given("the following Juventus players")
public void the_following_juventus_players(List<Map<String, String>> players) {
    for(Map<String, String> player : players) {
        System.out.printf(
            "%s of %s, born on %s, plays for Juventus since the %s season%n",
            player.get("name"),
            player.get("nationality"),
            player.get("dateOfBirth"),
            player.get("atJuventusSince")
        );
    }
}
{% endhighlight %}

Cucumber automatically converts the table structure we've seen in this example to an argument of type `List<Map<String, String>>`, or, in plain English, a `List` of `Map`s, where each `Map` represents a data entity (a football player, here), and each player has specific attributes, represented by the keys and their values in each `Map`.

To iterate over the `List`, we can use a `forEach()` loop again, as can be seen in the code snippet above. Each property value is retrieved using its corresponding key, which is equal to the header of the respective data table column in our specification.

Running this example yields the following output:

```
Cristiano Ronaldo of Portugal, born on 05-02-1985, plays for Juventus since the 2018/2019 season
Matthijs de Ligt of the Netherlands, born on 12-08-1999, plays for Juventus since the 2019/2020 season
Giorgio Chiellini of Italy, born on 14-08-1984, plays for Juventus since the 2005/2006 season
```

#### Example 3 - Tables with both row and column headers
As a final example, let's look at an even more intricate data structure: a table with both column and row headers. Example usages of such a table can be:

* Specifying availability per weekday and time of day for a delivery service
* Specifying train fare rates for combinations of seating class and age group for a railway company
* Specifying the distribution of gold, silver and bronze medals for a number of countries competing in the Olympics

or, in the example below, final scores for football matches. One way I've seen teams specify data similar to this:

{% highlight gherkin %}
Scenario: Listing historic football match results - the verbose way
    Given the final score of the Derby d'Italia played on 17-01-2021 was Internazionale 2, Juventus 0
    And the final score of the Derby d'Italia played on 08-03-2020 was Internazionale 0, Juventus 2
    And the final score of the Derby d'Italia played on 06-10-2019 was Internazionale 1, Juventus 2
{% endhighlight %}

implemented by

{% highlight java %}
@Given("^the final score of the Derby d'Italia played on (.*) was Internazionale (\\d+), Juventus (\\d+)$")
public void the_final_score_of_the_derby_dItalia_played_on_date_was_Internazionale_score_Juventus_score(String date, int interGoals, int juveGoals) {
    System.out.printf("The final score of the Derby d'Italia played on %s was Internazionale %d, Juventus %d", date, interGoals, juveGoals);
}
{% endhighlight %}

Once again, a lot of repetition in the specs, which makes them boring to read, you know the drill by now. Fortunately, there's a way out of this mess, too:

{% highlight gherkin %}
Scenario: Listing historic football match results - the clear way
    Given the following historic Derby d'Italia results
      |            | Internazionale | Juventus |
      | 17-01-2021 | 2              | 0        |
      | 08-03-2020 | 0              | 2        |
      | 06-10-2019 | 1              | 2        |
{% endhighlight %}
<sup><sub>Here, too, the syntax highlighting may be a little confusing: the first column in this table contains row headers.</sub></sup>

As you can see, the aforementioned state can be modeled using a table with both row and	colmun headers. Here, too, Cucumber is able to directly transform this into a suitable data structure:

{% highlight java %}
@Given("the following historic Derby d'Italia results")
public void the_following_historic_derby_dItalia_results(Map<String, Map<String, Integer>> results) {
    results.forEach((date, scores) ->
        System.out.printf(
            "The final score of the Derby d'Italia played on %s was Internazionale %d, Juventus %d%n",
            date, 
            scores.get("Internazionale"),
            scores.get("Juventus")
        )
    );
}
{% endhighlight %}

The data we specified is transformed into a `Map`, where the keys are the row headers (the dates on which the matches were played), and the values were `Map`s, too, with keys corresponding to the column headers (club names) and values containing the number of goals scored by each team.

In this example, too, we iterate over the outer `Map` to process each individual match using `forEach()`, and we use the column headers to get the values (the scores for each team) we want from the inner `Map` using `get()`.

Running this example yields the following output:

```
The final score of the Derby d'Italia played on 17-01-2021 was Internazionale 2, Juventus 0
The final score of the Derby d'Italia played on 08-03-2020 was Internazionale 0, Juventus 2
The final score of the Derby d'Italia played on 06-10-2019 was Internazionale 1, Juventus 2
```

In this article, we've seen some examples of how to use data tables to specify complex data more efficiently in Cucumber .feature files, how Cucumber automatically transforms these tables into specific data structures, and how we can iterate over and process that data in an efficient manner.

Effectively, what we've done is removing the iterative nature from our specifications to make them more readable, and move the iterating over the data to our step definitions. In a follow-up article, I'll dive deeper into using [data table transformers](https://github.com/cucumber/cucumber-jvm/tree/main/java#data-table-type){:target="_blank"} to handle complex data structures in an even more efficient way.

All the code shown in this blog post [can be found on GitHub](https://github.com/basdijkstra/ota-examples/tree/master/cucumber-data-tables){:target="_blank"}.