# Giving the front end code some testing love

### Laying the foundation
Unit testing is something most developers nod their heads that *should* be done, but in reality from my experience has varying levels of following up that initiative *especially* in the front end code. In fairness, I do believe this has occurred more with tenured server-side developers using technologies like ASP.NET where JavaScript took a back seat and was secondary in nature to the C# and Razor code more closely focused in development. With the proliferation of OSS and the resulting JavaScript libraries, frameworks, and advancements in the browser, the focus of web development has shifted heavily to the front-end code.

### Why do we need to write unit tests?
Here's the bottom line: we aren’t perfect. We need to verify our code does what it is intended to do without relying on an external dependency to integration test it for us as developers. The mentality of, "ah QA will find any bugs" is a very expensive stance to take. 

Let's look at the possibility of what can occur if we are void of unit tests. Take for example a service method in your web client code that's making a HTTP call to an API endpoint to retrieve data. A bug enhancement close to this code is assigned to a newly hired junior developer. Unfortunately he or she doesn't know how to configure the app properly or bypass the physical call which has latency, and decides to comment out the line of code making the API call and replaces it with a hardcoded object collection.

```javascript
//I'll uncomment this later...
//this.customers = this.myCustomerService.getData();
this.customers = [{id:1, name:"Jane Smith"},{id:2, name:"John Smith"}];
```


As this was a team without unit tests in the 1st place, they also don't do thorough code reviews either, so this code has now been committed directly to develop and off to QA. At this point the app builds, it runs, *and it displays data.* In this instance QA didn't realize the data returned was static, and now this simple oversight has the potential to make it all the way to production.

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
In JavaScript we have a plethora of unit testing frameworks and test runners available to us. In server-side code with for example C#, we would use NUnit, XUnit, MSTest, etc. as a testing framework. In client side code we need to choose a unit testing framework as well. The wonderful part of JavaScript are the almost limitless options because of what's available from the open source community. A few of the more popular testing frameworks include Jasmine, Mocha, and Jest. Some of the testing frameworks align closely with their SPA framework counterpart as they are configured out of the box using mainstream scaffolding tools (i.e. Jasmine and Angular via the CLI). 

Test runners are used to execute our unit tests. There are a slew of test runners available for JavaScript, and as with the testing frameworks, some test runners align more closely with popular frameworks and libraries like Karma for Angular, and Jest for React. The test runner will execute the tests against the browser targeted, so if you want to ensure the tests work in all browsers it will need to be tested as such. This is why using a 'headless' browser like the <a href="https://developers.google.com/web/updates/2017/04/headless-chrome" target="_blank">Headless Chrome</a> browser for testing is an attractive option as we can run tests sans a physical browser. This can all be setup via our testing configuration.

### Unit Testing JavaScript code using Jasmine
Jasmine is an excellent choice for unit testing front-end code as it is an all in one solution that is both a framework and assertion library. It also has a test runner built in and trivial to configure. Jasmine is a Behavior Driven Development (BDD) framework in which we create tests or 'specs.' The framework reads nicely in a 'Given When Then' (GWT) format that is expressive to the end user. As Jasmine has everything we need to test our code, let's look at how to get setup and leverage 

#### Getting setup
The easiest way to get started is to download Jasmine from npm using the following command:

```powershell
npm install --save-dev jasmine
```


Next we'll want to initialize Jasmine in our project. This can be done targeting the local or global installation of Jasmine. I find it easiest to have Jasmine installed globally 1st using the following npm command:

```powershell
npm install -g jasmine
```


To initialize use the following command:

```powershell
jasmine init
```


If you prefer not to install Jasmine globally, you can run the same command targeting the local installation of Jasmine from the node_modules folder:

```powershell
node node_modules/jasmine/bin/jasmine init
```


This will create a jasmine.json configuration file under spec/support within your application. You can modify the location of your spec files for Jasmine to include and exclude by updating the "spec_files" property. 

```json
{
  "spec_dir": "tests",
  "spec_files": [
    "js/jasmineBasic.spec.js",
    "js/jasmineMatchers.spec.js"
  ],
  "helpers": [
    "../../src/js/*.js"
  ],
  "stopSpecOnExpectationFailure": false,
  "random": true
}
```

Update the "scripts" section in package.json to 'jasmine' (the command used to execute/run our tests):

```json
"scripts": { "test": "jasmine" }
```

This will allow us to use our npm commands as follows to run our tests:

```powershell
npm test
```

If you have a smaller project, you may be able to use the Jasmine test runner directly. The easiest way to setup the spec runner is to download the Jasmine standalone package from https://github.com/jasmine/jasmine/releases and copy the SpecRunner.html file similar to the one below into your project and update as needed. The main required sections are the Jasmine files, the source files, and the spec files. When you want to run your tests, you can simply open the SpecRunner.html file in the browser and the tests will execute. However, for most applications running the Jasmine tests using a npm command, 'npm test' will be the primary way used throughout development. It is nice to see the Jasmine output from the test runner though using this .html file. Other test runners like Karma have flexible configurations to output reports, code coverage, and visuals as well on test results, so this isn't the only setup possible.

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Jasmine Spec Runner v3.3.0</title>

    <!-- jasmine required files -->
    <link rel="shortcut icon" type="image/png" href="../../node_modules/jasmine-core/images/jasmine_favicon.png">
    <link rel="stylesheet" type="text/css" href="../../node_modules/jasmine-core/lib/jasmine-core/jasmine.css">
    <script src="../../node_modules/jasmine-core/lib/jasmine-core/jasmine.js"></script>
    <script src="../../node_modules/jasmine-core/lib/jasmine-core/jasmine-html.js"></script>
    <script src="../../node_modules/jasmine-core/lib/jasmine-core/boot.js"></script>

    <!-- include source files here... -->
    <script src="src/js/myJavaScriptCode.js"></script>

    <!-- include spec files here... -->
    <script src="src/js/myJavaScriptCode.spec.js"></script>

</head>
<body>
</body>
</html>
```

It's a good time to mention that this raw setup will work well on a Node.js or other JavaScript project. If you're using a framework or library like Angular or React and are dealing with JavaScript modules, the setup can be much more involved, and it's recommended to use either the Angular CLI or Create React App tooling to generate and configure testing. The reason is that in order to execute your tests, the test runner needs to be configured to compile and have access to the needed modules referenced in a test. This area is outside the focus of this article where we'll focus directly on how to write the Jasmine tests against our JavaScript code.


#### Jasmine Matchers

To create some basic Jasmine tests we need to write our specs. For a BDD test you **describe** your code and tell the test what **it** should be doing. Then you **expect** the unit of code to do something. We use matchers in Jasmine to know if our expectation is true or false and if the test has passed or failed. There are a suite of matchers that come out of the box in Jasmine, and custom matchers can be created for your individual needs as well.

Let's begin with the most basic of tests. This test uses the **toBe** matcher to check for equality (a '===' in JavaScript):

```javascript
describe('simple matchers', function() {
    it("should be true", function () {
        expect(true).toBe(true);
    });
});
```

If you're familiar with server side testing, then the concept of setup and tear-down methods, in Jasmine this can be can be done with a beforeEach and afterEach.

```javascript
describe("using beforeEach and afterEach", function () {
    let personTest;
    let Person = function () {
        this.name = "";
    };
    beforeEach(function () {
        personTest = new Person();
        personTest.name = "Allen Conway";
    });
    it("the Person object should not be null", function () {
        expect(personTest).not.toBe(null);
    });
    it("the Person name should be Allen Conway", function () {
        expect(personTest.name).toEqual("Allen Conway");
    });
    afterEach(function () {
        //Some required cleanup
        personTest = null;
    });
});
```
Here are some additional tests to exercise how the different built in matchers work in Jasmine:

```javascript
describe("jasmine built in matchers unit tests", function () {

    beforeEach(function () {
        this.boolTest = true;
        this.person = {
            firstName: "Allen",
            lastName: "Conway"
        };
    });
    
    it("should be equal", function () {
        expect('Hello').toEqual('Hello');
    });
    
    it("boolTest should be true", function () {
        expect(this.boolTest).toBe(true);
    });
    
    it("should be null", function () {
        var obj = null;
        expect(obj).toBeNull();
    });
    
    it("testx should be greater than testz", function () {
        var testx = 5;
        var testz = 1;
        expect(testx).toBeGreaterThan(testz);
    });
    
    it("should contain element with value 2 in the array", function() {
        var myArray = [1,2,3,4,5];
        expect(myArray).toContain(2);
    });
    
    it("should not contain element with value 10 in the array", function () {
        var myArray = [1, 2, 3, 4, 5];
        expect(myArray).not.toContain(10);
    });
    
    it("should have person matching return data", function () {
        var returnData = {
            firstName: "Allen",
            lastName: "Conway"
        };
        expect(returnData).toEqual(this.person);
    });
    
    it("should be close to value with specified precision", function () {
        //2nd value is the precision after decimal; the default is '2'
        expect(5.1349).toBeCloseTo(5.135, 3);
    });

});
```
This is just a sampling of matchers in Jasmine, and the comprehensive list can be found at the following link: https://github.com/jasmine/jasmine/tree/master/src/core/matchers 

#### Jasmine Spies
Jasmine spies are used in unit testing for mocking and isolating code from dependencies. If you haven't done unit testing and are just getting your feet wet, ease into mocking. It might seem a tad confusing or intimidating at 1st, but after writing a few unit tests you’ll have the light bulbs go on and it’s a powerful tool within unit tests. In fact it's hard to get decent code coverage without mocking. If we don’t use a mocking framework and mocking in unit tests, we will ultimately be writing integration tests which are separate tests (also knows as E2E or end-to-end tests created with a framework like <a href="https://www.protractortest.org/#/" target="_blank">Protractor</a>).

We need to focus on the *unit* of code at hand and isolate dependencies. The dependencies we may need to isolate via a mock could be any of the following:

* Network connections
* Services
* External files
* External objects
* Database calls
* 3rd party libraries

A spy creates a proxy object that takes the place of the real object. Here is some sample code in a service that is being called from upstream code:

```javascript
let CarServiceJs = (function () {
    let my = this;
    my.getCars = function () {
        //In reality some longer runnign HTTP call is made here to get data
        return [
            { year: 2001, make: "Ford", model: "Mustang COBRA SVT", rearWheelHorsepower: 0, flywheelHorsepower: 0, weight: 3630, et: 13.7 },
            { year: 2014, make: "Ford", model: "Mustang Shelby GT500", rearWheelHorsepower: 0, flywheelHorsepower: 0, weight: 3845, et: 11.6 },
            { year: 2015, make: "Dodge", model: "Challenger Hellcat", rearWheelHorsepower: 0, flywheelHorsepower: 0, weight: 4449, et: 12.1 },
            { year: 2015, make: "Chevy", model: "Corvette Z06", rearWheelHorsepower: 0, flywheelHorsepower: 0, weight: 3524, et: 11.3 },
            { year: 2015, make: "Ford", model: "Camaro ZL1", rearWheelHorsepower: 0, flywheelHorsepower: 0, weight: 4120, et: 12.4 }
        ];
    };
    my.getCarsByYear = function (year) {
        return [
            //Logic would be here to get a collection of cars by year
        ];
    };
    return my;
}());
```

We need to leverage Jasmine spies for mocking so that when unit testing the code from the service, we aren't physically calling that code. Again that's a dependency to a network connection and HTTP API call that we don't want to occur. Running unit tests should be done in isolation and be fast in execution. If we actually made that API call and the network was slow, our unit tests (at that point really integration tests) are going to make running the suite of tests take entirely too long. 

Let's look at several examples using spies. To create a spy we use the **spyOn()** method, passing it the object and method. This 1st unit test uses the toHaveBeenCalled() matcher to see if the spy was called:

```javascript
it("should call carService using spyOn", function () {
    //Given
    let carSrvSpy = spyOn(CarServiceJs, "getCars");
    //When
    let result = CarServiceJs.getCars();
    //Then
    expect(carSrvSpy).toHaveBeenCalled();
});
```

This time we want to spy on the object and dictate its return value so that our unit of code being tested can have some valid information. The 1st step is to setup the return data, and then use the returnValue() method off of the spy:

```javascript
it("should call carService using spyOn and return mock data", function () {
    const getCarsMock = [
        { year: 2016, make: "Test", model: "Car", rearWheelHorsepower: 0, flywheelHorsepower: 0, weight: 1000, et: 10 }
    ];
    let carSrvSpy = spyOn(CarServiceJs, "getCars").and.returnValue(getCarsMock);
    let result = CarServiceJs.getCars();
    expect(result[0].year).toEqual(2016);
});
```

Moving a bit further, we want to more than just be able to dictate the return value and also fake the functionality of the spied upon object method. We can create our own function and use the callFake() method off of the spy:

```javascript
it("should call carService using spyOn with a fake implementation", function () {
    const getCarsFake = function () {
        return [
            { year: 2016, make: "Test", model: "Car", rearWheelHorsepower: 0, flywheelHorsepower: 0, weight: 1000, et: 10 }
        ];
    };
    let carSrvSpy = spyOn(CarServiceJs, "getCars").and.callFake(getCarsFake);
    let result = CarServiceJs.getCars();
    expect(result[0].year).toEqual(2016);
});
```

We can also spy on a method and inspect the parameters passed making expectations on the value for our test by using the toHaveBeenCalledWith() matcher:

```javascript
it("should call carService using spyOn toHaveBeenCalledWith matcher", function () {
    let carSrvSpy = spyOn(CarServiceJs, "getCarsByYear");
    let result = CarServiceJs.getCarsByYear(2001);
    expect(carSrvSpy).toHaveBeenCalledWith(2001);
});
```

Jasmine's spyOn() can only be used when the method already exist on the function.  We can use createSpy() to create a "bare" spy or empty implementation when there is no function to spy on, or we want to completely replace the call all together. In this instance we might need to pass our carService into another class that calls it's methods, but we want a stubbed out dummy function with no implementation behind it:

```javascript
it("should call carService using createSpy", function () {
    let carService = { getCars: jasmine.createSpy() };
    let engineCalc = new EngineCalculationsJs(carService);
    //calculateCarData() calls getCars from within:
    let result = engineCalc.calculateCarData();
    expect(carService.getCars).toHaveBeenCalled();
});
```

Lastly, we can chain together our created spy and still dictate the return value if needed using returnValue() off of the spy:

```javascript
it("should return mock data from spied service using createSpy", function () {
    let getCarsMock = [
        { year: 2016, make: "Test", model: "Car", rearWheelHorsepower: 0, flywheelHorsepower: 0, weight: 1000, et: 10 }
    ];
    let carService = { getCars: jasmine.createSpy() };
    //By chaining the spy with and.returnValue, all calls to the function will return a specific value
    carService.getCars.and.returnValue(getCarsMock);
    let engineCalc = new EngineCalculationsJs(carService);
    let result = engineCalc.calculateCarData();
    expect(carService.getCars).toHaveBeenCalled();
    expect(result[0].year).toEqual(2016);
});
```

### Moving forward
This has only scraped the surface of the rich functionality Jasmine offers for unit testing your JavaScript code. These building blocks shown above are the basis for the majority of tests you will write. For developers that have a tenured codebase and haven't done any unit testing, you can get started immediately and begin to build up your test suite. For all developers doing unit testing, here are a few final thoughts and things to keep in mind:

* 1..n tests > 0 tests (Some tests are better than *no* tests)
* Strive toward writing and refactoring to testable code 
* Minimize distractions and just start writing tests! (leave worrying about BDD, TDD, LMNOP for another day if it's getting in the way)
* Even a poorly written, tightly coupled system can be unit tested so don’t take even that as a barrier to writing tests (The tests might actually help expose weaknesses in design and provide ideas for refactoring later)

Need more information? The Jasmine docs have additional resources at the following: https://jasmine.github.io/index.html








    
