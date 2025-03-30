---
title: Building and improving Page Objects, one step at a time
layout: post
permalink: /building-and-improving-page-objects-one-step-at-a-time/
categories:
  - UI automation
tags:
  - Page Objects
  - object-oriented programming
  - Playwright
---
A few weeks ago, I ran a pair programming / mentoring session with someone who reached out to me because they felt they could use some support. When I first saw the code they wrote, I was pretty impressed.

Sure, there were some things I would have done differently, but most of that was personal preference, not a matter of my way being better than their way objectively. Instead of working on their code directly, instead, we therefore decided to build up some test code together from zero, discussing and applying good programming principles and patterns along the way.

As the tests were using Playwright in TypeScript, and were heavily oriented towards using the graphical user interface, we decided to start building a Page Object-based structure for a key component in their application.

This component was a UI component that enabled an end user to create a report in the system. The exact type of system or even the domain itself isn't really important for the purpose of this blog post, by the way. The component looked somewhat like this, heavily simplified:

![report_page](/images/blog/page_object_report_structure.png "A basic sketch of the design of the report creation page")

At the top, there was a radiobutton with three options that selected different report layouts. Every report layout consists of multiple form fields, and most form fields are text areas plus lock buttons that open a dropdown-like structure where you can edit the permissions for that field by selecting one or more roles that can view the contents of that text field (this is a privacy feature). And of course, there's a save button to save the report, as well as a print button.

The actual UI component had a few other types of components, but for the sake of brevity, let's stick to these for now.

### Iteration 0 - creating an initial Page Object

My approach whenever I start from scratch, either on my own or when working with someone else, is to take small steps and gradually introduce complexity. It might be tempting to immediately create a Page Object containing fields for all the elements and methods to interact with them, but that is going to get messy very quickly.

Instead, we started with the simplest Page Object we could think of: one that allowed us to create a standard report, without considering the lock buttons to set permissions. Let's assume that a standard report consists of only a title and a summary text field. The first iteration of that Page Object turned out to look something like this:

{% highlight typescript %}
export class StandardReportPage {

    readonly page: Page;
    readonly radioSelectStandard: Locator;
    readonly textfieldTitle: Locator;
    readonly textfieldSummary: Locator;
    readonly buttonSaveReport: Locator;
    readonly buttonPrintReport: Locator;

    constructor(page: Page) {
        this.page = page;
        this.radioSelectStandard = page.getByLabel('Standard report');
        this.textfieldTitle = page.getByPlaceholder('Title');
        this.textfieldSummary = page.getByPlaceholder('Summary');
        this.buttonSaveReport = page.getByRole('button', { name: 'Save' });
        this.buttonPrintReport = page.getByRole('button', { name: 'Print' });
    }

    async select() {
        await this.radioSelectStandard.click();
    }

    async setTitle(title: string) {
        await this.textfieldTitle.fill(title);
    }
    
    async setSummary(summary: string) {
        await this.textfieldSummary.fill(summary);
    }
    
    async save() {
        await this.buttonSaveReport.click();
    }
	
    async print() {
        await this.buttonPrintReport.click();
    }
}
{% endhighlight %}

which makes the test using this Page Object look like this:

{% highlight typescript %}
test('Creating a standard report', async ({ page } ) => {

    const standardReportPage = new StandardReportPage(page);
	
    await standardReportPage.select();
    await standardReportPage.setTitle('My new report title');
    await standardReportPage.setSummary('Summary of the report');
    await standardReportPage.save();

    await expect(page.getByTestId('standard-report-save-success')).toBeVisible();
});
{% endhighlight %}

### Iteration 1 - grouping element interactions

My first question after we implemented and used this Page Object was: 'how do you feel about the readability of this test?'. Of course, we just wrote this code, and it's a small example, but imagine you're working with Page Objects that are all written like this, and offer many more element interactions.

This will quickly lead to very procedural test code 'enter this, enter that, click here, check there' that doesn't show the intent of the test very clearly. In other words, this coding style does not really do a great job of hiding the implementation of the page (even when it hides the locators) and focusing _only_ on behaviour.

To improve this, I suggested grouping element interactions that form a logical end user interaction together in a single method and expose that. When I read or write a test, I'm not particularly interested in the sequence of individual element interactions I need to execute to perform a higher-level action. I'm not interested in 'filling a text field' or 'clicking a button', I'm interested in 'creating a standard report'.

This led us to refactor the Page Object into this:

{% highlight typescript %}
export class StandardReportPage {

    readonly page: Page;
    readonly radioSelectStandard: Locator;
    readonly textfieldTitle: Locator;
    readonly textfieldSummary: Locator;
    readonly buttonSaveReport: Locator;
    readonly buttonPrintReport: Locator;

    constructor(page: Page) {
        this.page = page;
        this.radioSelectStandard = page.getByLabel('Standard report');
        this.textfieldTitle = page.getByPlaceholder('Title');
        this.textfieldSummary = page.getByPlaceholder('Summary');
        this.buttonSaveReport = page.getByRole('button', { name: 'Save' });
        this.buttonPrintReport = page.getByRole('button', { name: 'Print' });
    }

    async select() {
        await this.radioSelectStandard.click();
    }

    async create(title: string, summary: string) {
        await this.textfieldTitle.fill(title);
        await this.textfieldSummary.fill(summary);
        await this.buttonSaveReport.click();
    }
    
    async print() {
        await this.buttonPrintReport.click();
    }
}
{% endhighlight %}

which in turn made the test look like this:

{% highlight typescript %}
test('Creating a standard report', async ({ page } ) => {

    const standardReportPage = new StandardReportPage(page);
    await standardReportPage.select();
    await standardReportPage.create('My new report title', 'Summary of the report');

    await expect(page.getByTestId('standard-report-save-success')).toBeVisible();
});
{% endhighlight %}

Much better already when it comes to readability and 'expose behaviour, hide implementation'. Doing exactly this is not something that is unique to UI automation, or even to test automation in general, by the way. This principle is called  [encapsulation](https://en.wikipedia.org/wiki/Encapsulation_(computer_programming)){:target="_blank"}, and it is one of the fundamental principles of object-oriented programming. It is a principle that is very useful to know when you're writing test code, if you want to keep your code readable, that is.

### Iteration 2 - Adding the ability to set permissions on a form field

For our next step, we decided to introduce the ability to set the access permissions for every text field. As explained and shown in the graphical representation of the form at the top of this post, every form field in the standard form has an associated lock button that opens a small dialog where the user can select which user roles can and cannot see the report field.

Our initial idea was to simply add additional fields in the Page Object representing the standard report. However, that would lead to a lot of repetitive work and to the standard report having a lot of fields containing element locators. So, we decided to see if we could consider the combination of a report text field and the associated permission lock button as a Page Component, i.e., a separate class that encapsulates the behaviour of a group of related elements on a specific page.

Setting this up in a reusable manner will be a lot easier when the HTML for these Page Components has the same structure across the entire application. The good news is that this is often the case, especially when frontend designers and developers design and implement frontends using tools like [Storybook](https://storybook.js.org/){:target="_blank"}.

So, the relevant part of the HTML for the standard form might look like this (again, simplified):

{% highlight html %}
<div id="standard_form">
  <div data-testid="form_field_subject">
    <div data-testid="form_field_subject_textfield"></div>
	<div data-testid="form_field_subject_lock"></div>
  </div>
  <div data-testid="form_field_summary">
    <div data-testid="form_field_summary_textfield"></div>
	<div data-testid="form_field_summary_lock"></div>
  </div>
</div>
{% endhighlight %}

An example reusable Page Component class might then look something like this:

{% highlight typescript %}
export class ReportFormField {

    readonly page: Page;
    readonly textfield: Locator;
    readonly buttonLockPermissions: Locator;

    constructor(page: Page, formFieldName: string) {
        this.page = page;
        this.textfield = page.getByTestId(`${formFieldName}_textfield`);
        this.buttonLockPermissions = page.getByTestId(`${formFieldName}_lock`);
    }

    async complete(text: string, roles: string[]) {
        await this.textfield.fill(text);
        await this.buttonLockPermissions.click();
        // handle setting permissions for the form field
    }
}
{% endhighlight %}

Note how the constructor of this Page Component class uses (in fact, relies on) the predictable, repetitive structure of the component in the application and the presence of `data-testid` attributes. If your components do not have these, find a way to add them, or find another generic way to locate the individual elements in the component on the page.

Now that we have defined our Page Component class, we need to define the relationship between these Page Components and the Page Object that contains them. In the past, my choice would default to creating base Page classes that would contain the reusable Page Components, as well as other utility methods. The more specific Page Object would then inherit from these base Pages, allowing them to use the methods defined in the parent base Page class.

Almost invariably, at some point that would lead to very messy base Page classes, with lots of fields and methods in it that were only tangentially related, at best. The cause of this mess? Me not thinking clearly about the _type of the relation_ between different Page Objects and Components.

You see, creating base classes and using [inheritance](https://en.wikipedia.org/wiki/Inheritance_(object-oriented_programming)){:target="_blank"} for reusability creates 'is-a' relations. These are useful when the relation between objects is of an 'is-a' nature. However, in our case, there is no 'is-a' relation, there is a 'has-a' relation. A Page Object _has a_ certain Page Component.

In other words, we need to define the relationship a different way, and that's by [using composition instead of inheritance](https://en.wikipedia.org/wiki/Composition_over_inheritance){:target="_blank"}. We define Page Components as components of our Page Objects, which makes for a far more natural relationship between the two, and for code that is way more clearly structured:

{% highlight typescript %}
export class StandardReportPage {

    readonly page: Page;
    readonly radioSelectStandard: Locator;
    readonly reportFormFieldTitle: ReportFormField;
    readonly reportFormFieldSummary: ReportFormField;
    readonly buttonSaveReport: Locator;
    readonly buttonPrintReport: Locator;

    constructor(page: Page) {
        this.page = page;
        this.radioSelectStandard = page.getByLabel('Standard report');
        this.reportFormFieldTitle = new ReportFormField(this.page, 'title');
        this.reportFormFieldSummary = new ReportFormField(this.page, 'summary');
        this.buttonSaveReport = page.getByRole('button', { name: 'Save' });
        this.buttonPrintReport = page.getByRole('button', { name: 'Print' });
    }

    async select() {
        await this.radioSelectStandard.click();
    }

    async create(title: string, summary: string, roles: string[]) {
        await this.reportFormFieldTitle.complete(title, roles);
        await this.reportFormFieldSummary.complete(summary, roles);
        await this.buttonSaveReport.click();
    }
    
    async print() {
        await this.buttonPrintReport.click();
    }
}
{% endhighlight %}

Reading this code feels far more natural than cramming everything into one or more parent classes c.q. base page objects. Lesson learned here: the way objects are related in your code should reflect the relationship between these objects in real life, that is, in your application.

### Iteration 3 - What about the other report types?

The development and refactoring steps we have gone through so far led us to a point where we were pretty happy with the code. However, we still only have Page Objects for a single type of form, and as you have seen in the sketch at the top of this blog post, there are different types of forms. How do we deal with those? Especially when we know that these forms share _some_ components and behaviour, but not _all_ of them?

It is tempting to immediately jump to conclusions and start throwing patterns and structures at the problem, but in pair programming sessions like this, I typically try and avoid finding and implementing the 'final' solution right away. Why? Because the best learning is done when you see (or create, in this case) a suboptimal situation, discuss the problems with that situation, investigate potential solutions and only then implement them.

Sure, it will take longer, initially, but this is made up for in spades with a much better understanding of what suboptimal code looks like and how to improve it.

So, first we create separate classes for individual report types, each similar to the implementation for the standard report we created before. Here is an example for an extended report, containing more form fields (well, just one more, but you get the idea):

{% highlight typescript %}
export class ExtendedReportPage {

    readonly page: Page;
    readonly radioSelectExtended: Locator;
    readonly reportFormFieldTitle: ReportFormField;
    readonly reportFormFieldSummary: ReportFormField;
    readonly reportFormFieldAdditionalInfo: ReportFormField;
    readonly buttonSaveReport: Locator;
    readonly buttonPrintReport: Locator;

    constructor(page: Page) {
        this.page = page;
        this.radioSelectExtended = page.getByLabel('Extended report');
        this.reportFormFieldTitle = new ReportFormField(this.page, 'title');
        this.reportFormFieldSummary = new ReportFormField(this.page, 'summary');
        this.reportFormFieldAdditionalInfo = new ReportFormField(this.page, 'additionalInfo');
        this.buttonSaveReport = page.getByRole('button', { name: 'Save' });
        this.buttonPrintReport = page.getByRole('button', { name: 'Print' });
    }

    async select() {
        await this.radioSelectExtended.click();
    }

    async create(title: string, summary: string, additionalInfo: string, roles: string[]) {
        await this.reportFormFieldTitle.complete(title, roles);
        await this.reportFormFieldSummary.complete(summary, roles);
        await this.reportFormFieldAdditionalInfo.complete(additionalInfo, roles);
        await this.buttonSaveReport.click();
    }

    async print() {
        await this.buttonPrintReport.click();
    }
}
{% endhighlight %}

Obviously, there's a good amount of duplication between this class and the Page Object for the standard report. What to do with them? Contrary to the situation with the Page Components, it _is_ a good idea to reduce the duplication by creating a base report Page Object here. We're talking about creating an 'is-a' relationship (inheritance) here, not a 'has-a' relation (composition). A standard report _is a_ report.

That means that in this case, we can, and we should, create a base report Page Object, move some (or maybe even all) of the duplicated code there, and have the specific report Page Objects derive from that base report class.

My recommendation here is to make the base report Page Object an [abstract class](https://khalilstemmler.com/blogs/typescript/abstract-class/){:target="_blank"} to prevent people from instantiating it directly. This leads to more expressive and clear code, as we can only instantiate the concrete report subtype, which will make it immediately clear to the reader of the code what type of report they're dealing with.

In the abstract class, we declare the elements that are shared between all reports. This applies to methods, but also to web elements that appear in all report types. This is what the abstract base class might look like:

{% highlight typescript %}
export abstract class ReportBasePage {

    readonly page: Page;
    readonly reportFormFieldTitle: ReportFormField;
    readonly reportFormFieldSummary: ReportFormField;
    readonly buttonSaveReport: Locator;
    readonly buttonPrintReport: Locator;

    abstract readonly radioSelect: Locator;

    protected constructor(page: Page) {
        this.page = page;
        this.reportFormFieldTitle = new ReportFormField(this.page, 'title');
        this.reportFormFieldSummary = new ReportFormField(this.page, 'summary');
        this.buttonSaveReport = page.getByRole('button', { name: 'Save' });
        this.buttonPrintReport = page.getByRole('button', { name: 'Print' });
    }

    async select() {
        await this.radioSelect.click();
    }

    async print() {
        await this.buttonPrintReport.click();
    }
}
{% endhighlight %}

and a concrete class for the standard report, implementing the abstract class now looks like this:

{% highlight typescript %}
export class ExtendedReportPage extends ReportBasePage {

    readonly page: Page;
    readonly radioSelect: Locator;
    readonly reportFormFieldAdditionalInfo: ReportFormField;

    constructor(page: Page) {
        super(page);
        this.page = page;
        this.radioSelect = page.getByLabel('Extended report');
        this.reportFormFieldAdditionalInfo = new ReportFormField(this.page, 'additionalInfo');
    }

    async create(title: string, summary: string, additionalInfo: string, roles: string[]) {
        await this.reportFormFieldTitle.complete(title, roles);
        await this.reportFormFieldSummary.complete(summary, roles);
        await this.reportFormFieldAdditionalInfo.complete(additionalInfo, roles);
        await this.buttonSaveReport.click();
    }
}
{% endhighlight %}

The abstract class takes care of the methods that are shared between all reports, such as the `print()` and the `select()` methods, It also defines what elements and methods should be implemented by the implementing concrete classes. For now, that's only the `radioSelect` locator.

Note that at the moment, because the data required for the different types of reports is not the same, we cannot yet add an `abstract select(): void` method requirement, that all report Page Objects should implement, to our abstract class. This is a temporary drawback and one that we will address in a moment.

Also note that the test code doesn't change, but we can now create both a standard report and an extended report that, behind the scenes, share a significant amount of code. Definitely a step in the right direction.

### Iteration 4 - Dealing with test data

Our tests already look pretty good. They are easy to read, and the way the code is structured aligns with the structure of the parts of the application they're representing. Are we done yet? Well, maybe. As a final improvement to our tests, let's have a look at the way we handle our test data.

Right now, the test data we use in our test methods is simply an unstructured collection of strings, integers, boolean and so on. For small tests and a simple domain that is easy to understand, you might get away with this, but as soon as your test suite grows and your domain becomes more complex, this will get confusing. What does that string value represent exactly? Why is that variable a boolean and what happens if it is set to `true` (or `false`)?

This is where test data objects can help out. Test data objects are simple classes, often nothing more fancy than a [Data Transfer Object (DTO)](https://en.wikipedia.org/wiki/Data_transfer_object){:target="_blank"}, that represent a domain entity. In this situation, that domain entity might be a report, for example. Having types that represent domain entities greatly improves the readability of our code, it will make it much easier to understand what exactly we're doing here.

The implementation of these test data objects is often straightforward. In TypeScript, we can use a simple interface for this purpose. I chose to create one `ReportContent` class that contains the data for all of our report types. As they diverge, I might choose to refactor these into separate interfaces, but for now, this is fine.

Also, defining this test data object has the additional benefit of allowing me to move the definition of the `create()` method for the different report Page Objects to the abstract base class, a step that we were unable to perform previously. This is what my interface looks like:

{% highlight typescript %}
export interface ReportContent {

    title: string;
    summary: string;
    additionalInfo?: string;
    roles: string[];
}
{% endhighlight %}

The `additionalInfo` field is marked as optional, as it only appears in an extended report, not in a standard report.

In some cases, to further improve flexibility and readability of our code, we might add a builder or a factory that helps us create instances of our test data objects using a fluent syntax. This also allows us to set sensible default values for properties to avoid having to assign the same values to these properties in every test. In this specific case, that's not really necessary, because object creation based on an interface in TypeScript is really straightforward, and our `ReportContent` object is small, anyway. Your mileage may vary.

Now that we have defined a type for our report data, we can change the signature and the implementation of the `create()` methods in our Page Objects to use this type. Here's an example for the extended report:

{% highlight typescript %}
async create(report: ReportContent) {
    
    await this.reportFormFieldTitle.complete(report.title, report.roles);
    await this.reportFormFieldSummary.complete(report.summary, report.roles);
    await this.reportFormFieldAdditionalInfo.complete(report.additionalInfo, report.roles);
    await this.buttonSaveReport.click();
}
{% endhighlight %}

and we can now add the following line to the abstract `ReportBasePage` class:

{% highlight typescript %}
abstract create(report: ReportContent): void;
{% endhighlight %}

to enforce all report Page Objects to implement a `create()` method that takes an argument of type `ReportContent`.

We can do the same for other test data objects. Oh, and if you're storing your tests in the same repository as your application code, these test data objects might even exist already, in which case you might be able to reuse them. This is definitely worth checking, because why would we reinvent the wheel?

That was a lot of work, but it has led to code that is, in my opinion, well-structured and easy to read and maintain. As this blog post has hopefully shown, it is very useful to have a good working knowledge of common object-oriented programming principles and patterns when you're writing test code. This is especially true for UI automation, but many of the principles we have seen in this blog post can be applied to other types of test automation, too.

There are many other patterns out there to explore. This blog post is not an attempt to list them all, nor does it show 'the one true way' of writing Page Objects. Hopefully, though, it has shown you my thought process when I write test automation code, and how understanding fundamentals of object-oriented programming helps me do this better.

A massive 'thank you' to [Olena](https://www.linkedin.com/in/olena-pasko/){:target="_blank"} for participating in the pair programming session I discussed and for reviewing this blog post. I really appreciate it.