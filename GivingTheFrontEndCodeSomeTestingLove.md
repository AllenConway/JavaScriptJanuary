# Giving the front end code some testing love

### Laying the foundation
Unit testing is something most developers nod their heads that should be done, but in reality from my experience has varying levels of following up that initiative *especially* in the front end code. In fairness I do believe this has occurred more with tenured server-side developers using technologies like ASP.NET where JavaScript took a back seat and was secondary in nature to the C# and Razor code more closely focused in development. With the proliferation of OSS and the resulting JavaScript libraries, frameworks, and advancements in the browser, the focus of web development has shifted heavily to the front-end code.

### Why do we need to write unit tests?
Here's the bottom line: we aren’t perfect. We need to verify our code does what it is intended to do without relying on an external dependency to integration test it for us as developers. The mentality of, "ah QA will find any bugs" is a very expensive stance to take. 

Let's look at the possibility of what can occur if we are void of unit tests. Take for example a service method in your web client call that's making a HTTP call to an API endpoint to retrieve data. A bug enhancement close to this code is assigned to a newly hired junior developer. Unfortunately he or she doesn't know how to configure the app properly or bypass the physical call which has latency, and decides to comment out the line of code making the call and replaces it with a hardcoded object collection.

```javascript
//I'll uncomment this later...
//this.customers = this.myCustomerService.getData();
this.customers = [{id:1, name:"Jane Smith"},{id:2, name:"John Smith"}];
```


As this was a team without unit tests in the 1st place, they also don't do thorough code reviews either so this code has now been committed directly to develop and off to QA. At this point the app builds, it runs, *and it displays data.* In this instance QA didn't realize the data returned was static, and so now this simple oversight has the potential to make it all the way to production.

This *could* be seen as an extreme case, but I'll tell you in my years of experience I've absolutely seen this happen. What could we have done to prevent this? With a unit test in place to test for the existence of the call being made, we could have caught this during development even before a code review. 

```javascript
it("myCustomerService.getData is called", function() {
    expect(myCustomerService.getData).toHaveBeenCalled();
});
```
With the above unit test in place and a team that embraces testing and running tests prior to committing code, this would have been found right away. As soon as the junior developer that made this oversight ran the tests, they would have those infamous red letters indicating a failure. Even if they didn't know exactly what happened, it would be a queue to ask for help. 

Once unit testing becomes part of the development and team culture there are other checks and balances that can be put in place to ensure quality. In CI/CD builds, unit tests can be executed as a part of the build and make it fail unless all unit tests have passed. Artifacts like code coverage reports can be output as well for interested parties to read and use it as input to gauge overall testing efforts and areas of weakness. 

### Challenges of Unit Testing
So if unit testing is such a great thing, then why isn't everyone doing it, and all code bases have 100% coverage? Well simply put it's more work. More work = more time = more money. This equation unfortunately is only looked at for the up front effort. This tweet from @tottinge a few years back always resonated well with me on this topic:

> "Not good messaging: "Don't _waste_ dev time writing tests, instead _spend_ your evenings, weekends, and holidays fixing buggy code"

The reality is that if a decision maker believes that not writing unit tests will *save* money this has been proven to be a mistake. In the long run the cost of buggy and error prone code will cost many times more than the upfront cost of writing tests. Even cost aside, we as engineers have a responsibility to the code we write and need to ensure it is doing exactly what we intended it to do. Some industries don't have a choice on testing. If you think about it you wouldn't want any software in aviation, manufacturing, medical, or the like to wait and be tested once in production. Regardless of the domain you work within or the type of code you create it should be unit tested.

### JavaScript Unit Testing Frameworks and Test Runners 
In JavaScript we have a plethora of unit testing frameworks and test runners available to us. In server-side code with say C# we would use NUnit, XUnit, MSTest, etc. as a testing framework. In client side code we need to choose a unit testing framework as well. The wonderful part of JavaScript are the almost limitless options because of what's available from the open source community. Some of the more popular frameworks include Jasmine, Mocha, and Jest. Some of the frameworks align closely with certain framework as they are configured out of the box using mainstream scaffolding tools (i.e. Jasmine and Angular via the CLI). 

Test runners are used to execute our unit tests. There are a slew of test runners available for JavaScript, and as with the testing frameworks, some test runners align more closely with popular frameworks and libraries like Karma for Angular, and Jest for React. The test runner will execute the tests against the browser targeted, so if you want to ensure the tests work in all browsers it will need to be tested as such. This is why using a 'headless' browser like the 'headless Chrome' browser for testing is an attractive option as we can run tests sans a physical browser. This can all be configured via our testing configuration.

### Unit Testing JavaScript code using Jasmine
Jasmine is an excellent choice for unit testing front-end code as it is an all in one solution that is both a framework and assertion library. It also has a test runner built in and trivial to configure. Jasmine is a Behavior Driven Development (BDD) framework in which we create tests or 'specs.' The framework reads nicely in a 'Given When Then' (GWT) format that is expressive to the end user. As Jasmine has everything we need to test our code, let's look at how to get setup and leverage 

#### Getting setup

#### Jasmine Matchers

#### Jasmine Spies

#### Unit Testing Async Code

### Moving forward
