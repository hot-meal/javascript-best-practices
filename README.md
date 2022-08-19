# Javascript: Best Practices

## Introduction

This is a short, simple guide to producing readable, reusable, and refactorable JavaScript.

These are guidelines and nothing more. Not every principle has to be strictly followed.


## **Variables**

## Use easy, short and readable variable and function names

We often come across variables like `abc1`, `bh2` or `xkohj` in JavaScript, or — on the other side — variable names like `incrementorTheForLoopWhichFromTenToTwenty` or `createNewMemberIfAgeOverThirty`.

Good names should be easy to understand and tell you what is going on, no more or less. 

Avoid combining values and functionality in names. A function called isLegalDrivingAge() makes more sense than isOverEighteen() as the legal driving age varies around the world, and there are many other things that are limited by age.

**Bad:**

```javascript
const yyyymmdstr = moment().format("YYYY/MM/DD");
```

**Good:**

```javascript
const currentDate = moment().format("YYYY/MM/DD");
```

**[⬆ top](#introduction)**

## Use Hungarian notation for variable naming 

If you have a variable called `firstName` and it is supposed to be a string, you would write it as `sfirstName` in “Hungarian”. 

An object called `isMember` would be `oisMember` for a Boolean.

It can seem like extra work for some, but it an easy but limited alternative for the lack of type checking natively in JavaScript.


**[⬆ top](#introduction)**

## Avoid polluting the global namespace (global variables)

Global variables and function names are a really bad idea. Every JavaScript file included in the page will run within the same scope. 

Which means, if you have global variables or functions in your code, any scripts included after yours that have the same names will overwrite yours.

There are several workarounds to avoid using globals — we’ll go through them one by one now. Say you have three functions and a variable like this:

**Bad:**

```javascript
let current = null;
function init(){...}
function change(){...}
function verify(){...}
```

**Good:**

```javascript
let myNameSpace = {
  current:null,
  init:function(){...},
  change:function(){...},
  verify:function(){...}
}
```


**[⬆ top](#introduction)**

### Use searchable names

We read more code than we write. So it's important that the code we write is easily readable and searchable.

**Bad:**

```javascript
// What is 96400000 for?
setTimeout(launch, 86400000);
```

**Good:**

```javascript
// Declare them as capitalized constants.
const MILLISECONDS_PER_DAY = 60 * 60 * 24 * 1000; //86400000;

setTimeout(setTimeout(launch, 86400000);
, MILLISECONDS_PER_DAY);
```

**[⬆ top](#introduction)**

## Use let and const for local variables

let and const allow you to create block-scoped local variables.

let can be re-declared (in a different scope) while const can’t. 

Unlike var, neither let or const becomes available as a property of the window object in (DOM programming), so they can’t be accessed, and therefore overridden, by other scripts.

You can define let with or without a value, but it can only read or written to after initialisation. However you cannot declare const without a value:

```javascript
let localVar;
let localVarText = "text string";
const LOCAL_CONST = 999;
```

**[⬆ top](#introduction)**

### Avoid Mental Mapping

Explicit is better than implicit.

**Bad:**

```javascript
const locations = ["Austin", "New York", "San Francisco"];
locations.forEach(l => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  // Wait, what is `l` for again?
  dispatch(l);
});
```

**Good:**

```javascript
const locations = ["Austin", "New York", "San Francisco"];
locations.forEach(location => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  dispatch(location);
});
```

**[⬆ top](#introduction)**

### Don't add unnecessary context

If your class/object name tells you something, don't repeat that in your variable name.

**Bad:**

```javascript
const Car = {
  carMake: "Honda",
  carModel: "Accord",
  carColor: "Blue"
};

function paintCar(car, color) {
  car.carColor = color;
}
```

**Good:**

```javascript
const Car = {
  make: "Honda",
  model: "Accord",
  color: "Blue"
};

function paintCar(car, color) {
  car.color = color;
}
```

**[⬆ top](#introduction)**

### Use default arguments instead of short circuiting or conditionals

Default arguments are often cleaner than short circuiting. Be aware that if you
use them, your function will only provide default values for `undefined`
arguments. Other "falsy" values such as `''`, `""`, `false`, `null`, `0`, and
`NaN`, will not be replaced by a default value.

**Bad:**

```javascript
function createMicrobrewery(name) {
  const breweryName = name || "Hipster Brew Co.";
  // ...
}
```

**Good:**

```javascript
function createMicrobrewery(name = "Hipster Brew Co.") {
  // ...
}
```

**[⬆ top](#introduction)**

## Use shorthands when you can

There are many shorthands that you use to reduce the verbosity of your code and increase the readability.


**Long:**

```javascript
if (value = true) { … }
```


**Short**

```javascript
if (value) { … }
```


**Long:**

```javascript
let add = function(a, b) {
  return a + b;
}
```

**Short**
```javascript
let add = (a,b) => a + b;
```


**[⬆ top](#introduction)**

## **Functions**

### Limit function arguments to two or more (ideally)

Limiting the amount of function parameters is important because it
makes testing your function easier.

Having more than three leads to a combinatorial explosion where you have to test many different cases with each separate argument.

One or two arguments is ideal, three should be avoided if possible.

Anything more than that should be consolidated. If you have
more than two arguments then probably your function is trying to do too much. 

JavaScript allows you to make objects on the fly, so you can use an object if you are finding yourself needing a lot of arguments.

To make it obvious what properties the function expects, you can use the destructuring syntax. This has a few advantages:

1. When someone looks at the function signature, it's immediately clear what
   properties are being used.
2. It can be used to simulate named parameters.
3. Destructuring also clones the specified primitive values of the argument
   object passed into the function. This can help prevent side effects. Note:
   objects and arrays that are destructured from the argument object are NOT
   cloned.
4. Linters can warn you about unused properties, which would be impossible
   without destructuring.

**Bad:**

```javascript
function createMenu(title, body, buttonText, cancellable) {
  // ...
}

createMenu("Foo", "Bar", "Baz", true);

```

**Good:**

```javascript
function createMenu({ title, body, buttonText, cancellable }) {
  // ...
}

createMenu({
  title: "Foo",
  body: "Bar",
  buttonText: "Baz",
  cancellable: true
});
```

**[⬆ top](#introduction)**

## Modularize - functions should do one thing only

This is a general programming good. practice.

Using functions that do one job at a time makes it easy for other developers to understand and change your code without having to read through all the code to work out what code block performs what function.

This also applies to creating utility functions for common tasks. 

If you find yourself repeating same thing in several different functions then it is time to create a generic helper function instead, and then reuse that function where it is needed.


**Bad:**

```javascript
function emailClients(clients) {
  clients.forEach(client => {
    const clientRecord = database.lookup(client);
    if (clientRecord.isActive()) {
      email(client);
    }
  });
}
```

**Good:**

```javascript
function emailActiveClients(clients) {
  clients.filter(isActiveClient).forEach(email);
}

function isActiveClient(client) {
  const clientRecord = database.lookup(client);
  return clientRecord.isActive();
}
```

**[⬆ top](#introduction)**

### Function names should say what they do

**Bad:**

```javascript
function addToDate(date, month) {
  // ...
}

const date = new Date();

// It's hard to tell from the function name what is added
addToDate(date, 1);
```

**Good:**

```javascript
function addMonthToDate(month, date) {
  // ...
}

const date = new Date();
addMonthToDate(1, date);
```

**[⬆ top](#introduction)**


## Enhance progressively

Progressive Enhancement is a somewhat forgotton development practice in modern JavaScript.

Graceful degradation versus progressive enhancement is essentially that you should write code that works regardless of available technology. 

With JavaScript, that means that when JavaScript is disabled your web apps should still allow users to accomplish their goal, you should not block them because of the lack of JavaScript which they can’t turn on, or don’t wish to.

Many times you will find a massively complex JavaScript solution for a problem that can be solved easily without it. 


**[⬆ top](#introduction)**

### Functions should have only one level of abstraction

If you have more than one level of abstraction, your function is usually doing too much. 

Splitting up functions leads to reusability and easier testing.

**Bad:**

```javascript
function parseBetterJSAlternative(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(" ");
  const tokens = [];
  REGEXES.forEach(REGEX => {
    statements.forEach(statement => {
      // ...
    });
  });

  const ast = [];
  tokens.forEach(token => {
    // lex...
  });

  ast.forEach(node => {
    // parse...
  });
}
```

**Good:**

```javascript
function parseBetterJSAlternative(code) {
  const tokens = tokenize(code);
  const syntaxTree = parse(tokens);
  syntaxTree.forEach(node => {
    // parse...
  });
}

function tokenize(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(" ");
  const tokens = [];
  REGEXES.forEach(REGEX => {
    statements.forEach(statement => {
      tokens.push(/* ... */);
    });
  });

  return tokens;
}

function parse(tokens) {
  const syntaxTree = [];
  tokens.forEach(token => {
    syntaxTree.push(/* ... */);
  });

  return syntaxTree;
}
```

**[⬆ top](#introduction)**

### Do Not Repeat Yourself (DRY)

Duplicate code is bad because it creates more than one place to change something if you need to update some logic.

**Bad:**

```javascript
function showDeveloperList(developers) {
  developers.forEach(developer => {
    const expectedSalary = developer.calculateExpectedSalary();
    const experience = developer.getExperience();
    const githubLink = developer.getGithubLink();
    const data = {
      expectedSalary,
      experience,
      githubLink
    };

    render(data);
  });
}

function showManagerList(managers) {
  managers.forEach(manager => {
    const expectedSalary = manager.calculateExpectedSalary();
    const experience = manager.getExperience();
    const portfolio = manager.getMBAProjects();
    const data = {
      expectedSalary,
      experience,
      portfolio
    };

    render(data);
  });
}
```

**Good:**

```javascript
function showEmployeeList(employees) {
  employees.forEach(employee => {
    const expectedSalary = employee.calculateExpectedSalary();
    const experience = employee.getExperience();

    const data = {
      expectedSalary,
      experience
    };

    switch (employee.type) {
      case "manager":
        data.portfolio = employee.getMBAProjects();
        break;
      case "developer":
        data.githubLink = employee.getGithubLink();
        break;
    }

    render(data);
  });
}
```

**[⬆ top](#introduction)**

### Use Object.assign to set default objects  

**Bad:**

```javascript
const menuConfig = {
  title: null,
  body: "Bar",
  buttonText: null,
  cancellable: true
};

function createMenu(config) {
  config.title = config.title || "Foo";
  config.body = config.body || "Bar";
  config.buttonText = config.buttonText || "Baz";
  config.cancellable =
    config.cancellable !== undefined ? config.cancellable : true;
}

createMenu(menuConfig);
```

**Good:**

```javascript
const menuConfig = {
  title: "Order",
  // User did not include 'body' key
  buttonText: "Send",
  cancellable: true
};

function createMenu(config) {
  let finalConfig = Object.assign(
    {
      title: "Foo",
      body: "Bar",
      buttonText: "Baz",
      cancellable: true
    },
    config
  );
  return finalConfig
  // config now equals: {title: "Order", body: "Bar", buttonText: "Send", cancellable: true}
  // ...
}

createMenu(menuConfig);
```

**[⬆ top](#introduction)**

### Avoid passing flags as function parameters

Flags indicate that  function does more than one thing. Functions should only do one thing. So use a bollean to split your functions if they are following different paths.

**Bad:**

```javascript
function createFile(name, temp) {
  if (temp) {
    fs.create(`./temp/${name}`);
  } else {
    fs.create(name);
  }
}
```

**Good:**

```javascript
function createFile(name) {
  fs.create(name);
}

function createTempFile(name) {
  createFile(`./temp/${name}`);
}
```

**[⬆ top](#introduction)**

### Avoid Using Side Effects 

A function produces a side effect if it does anything other than take a value in and return another value or values (a pure function). 

An example of a side effect would be writing to a file, changing a global variable, or modifying something outside of your app such as the browser title.

You might need to have side effects at times, like having to write to a file. You should try to centralise where you are doing this. Avoid having multiple functions and classes that write to a file. Have only one that that does it. 

The purpose of this is to avoid common mistakes such as sharing state between objects, using mutable data types that can be written to by anything. It will make your code less predictable.

**Bad:**

```javascript
// Global variable referenced by following function.
// If we had another function that used this name, now it'd be an array and it could break it.
let name = "Homer Simpson";

function splitIntoFirstAndLastName() {
  name = name.split(" ");
}

splitIntoFirstAndLastName();

console.log(name); // ['Ryan', 'McDermott'];
```

**Good:**

```javascript
function splitIntoFirstAndLastName(name) {
  return name.split(" ");
}

const name = "Homer Simpson";
const newName = splitIntoFirstAndLastName(name);

console.log(name); // 'Homer Simpson';
console.log(newName); // ['Homer', 'Simpson'];
```

**[⬆ top](#introduction)**

### Don't write to global functions

Polluting global namespace is considered a bad practice in JavaScript because you could conflict with another library. 

Let's say you wanted to extend JavaScript's native Array method to have a `diff` method that could show the difference between two arrays? 

You could extend the `Array.prototype`, but it could clash with another library that tried to do the same thing. 

What if that other library was using `diff` to find the difference between the first and last elements of an array? 

This is why it is better to use ES6 classes and extend the `Array` global.

**Bad:**

```javascript
Array.prototype.diff = function diff(comparisonArray) {
  const hash = new Set(comparisonArray);
  return this.filter(elem => !hash.has(elem));
};
```

**Good:**

```javascript
class SuperArray extends Array {
  diff(comparisonArray) {
    const hash = new Set(comparisonArray);
    return this.filter(elem => !hash.has(elem));
  }
}
```

**[⬆ top](#introduction)**

## Prefer for/of over for loops

Loops can have a performance hit when repeating the same operation over and over. 

JavaScript has some best practices for performant loops, like avoiding nesting, putting any DOM manipulation outside of loops, and using a separate variable for the length of the loop (e.g. let countriesCount = countries.length).

Using the for...of loop instead of for is a good performance practice also.

**Bad:**

```javascript
let cities = ["New York", "Paris", "Beijing", "Sao Paulo", "Auckland"];
let cityCount = cities.length;

for(let i = 0; i < cityCount; i++) {
  console.log( cities[i] );
}
```

**Good:**

```javascript
let cities = ["New York", "Paris", "Beijing", "Sao Paulo", "Auckland"];

for(city of cities) {
  console.log(city);
}
```

for/of loop  can be used to iterate over any iterable object, like arrays, strings, nodelists, maps, and more.

**[⬆ top](#introduction)**

### Choose functional over imperative programming

JavaScript isn't a pure functional language in the way that Haskell is, but it still has
a functional aspect to it. 

Favour this style of programming when you can.

**Bad:**

```javascript
const programmerOutput = [
  {
    name: "Uncle Bobby",
    linesOfCode: 500
  },
  {
    name: "Suzie Q",
    linesOfCode: 1500
  },
  {
    name: "Jimmy Gosling",
    linesOfCode: 150
  },
  {
    name: "Gracie Hopper",
    linesOfCode: 1000
  }
];

let totalOutput = 0;

for (let i = 0; i < programmerOutput.length; i++) {
  totalOutput += programmerOutput[i].linesOfCode;
}
```

**Good:**

```javascript
const programmerOutput = [
  {
    name: "Uncle Bobby",
    linesOfCode: 500
  },
  {
    name: "Suzie Q",
    linesOfCode: 1500
  },
  {
    name: "Jimmy Gosling",
    linesOfCode: 150
  },
  {
    name: "Gracie Hopper",
    linesOfCode: 1000
  }
];

const totalOutput = programmerOutput.reduce(
  (totalLines, output) => totalLines + output.linesOfCode,
  0
);
```

**[⬆ top](#introduction)**

### Encapsulate conditional statements

**Bad:**

```javascript
if (fsm.state === "fetching" && isEmpty(listNode)) {
  // ...
}
```

**Good:**

```javascript
function shouldShowSpinner(fsm, listNode) {
  return fsm.state === "fetching" && isEmpty(listNode);
}

if (shouldShowSpinner(fsmInstance, listNodeInstance)) {
  // ...
}
```

**[⬆ top](#introduction)**

### Avoid using negative conditionals

**Bad:**

```javascript
function isNodeNotPresent(node) {
  // ...
}

if (!isNodeNotPresent(node)) {
  // ...
}
```

**Good:**

```javascript
function isNodePresent(node) {
  // ...
}

if (isNodePresent(node)) {
  // ...
}
```

**[⬆ top](#introduction)**

### Avoid conditionals

This can seem like an impossible task at first. 

How can you do anything without an `if` statement? You can use polymorphism to accomplish the same goal in many cases. 

So why would you want to do that? 

Because a function should only do one thing. When you have functions that contain `if` statements, your function does more than one thing. Remember, just do one thing.

**Bad:**

```javascript
class Airplane {
  // ...
  getCruisingAltitude() {
    switch (this.type) {
      case "777":
        return this.getMaxAltitude() - this.getPassengerCount();
      case "Air Force One":
        return this.getMaxAltitude();
      case "Cessna":
        return this.getMaxAltitude() - this.getFuelExpenditure();
    }
  }
}
```

**Good:**

```javascript
class Airplane {
  // ...
}

class Boeing777 extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getPassengerCount();
  }
}

class AirForceOne extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude();
  }
}

class Cessna extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getFuelExpenditure();
  }
}
```

**[⬆ top](#introduction)**

### Don't over-optimize

Modern browsers are pretty well optimised. A lot of
times, if you are optimising then you may be wasting your time. 

**Bad:**

```javascript
// On old browsers, each iteration with uncached `list.length` would be costly
// because of `list.length` recomputation. In modern browsers, this is optimized.
for (let i = 0, len = list.length; i < len; i++) {
  // ...
}
```

**Good:**

```javascript
for (let i = 0; i < list.length; i++) {
  // ...
}
```

**[⬆ top](#introduction)**

## **Objects and Data Structures**

### Use getters and setters

Benefits of using getters and setters to access data within your objects:


- Makes validation simple when doing a `set`.
- Encapsulates the object
- Easy to add logging and error handling
- You can lazy load your object's properties, let's say getting it from a
  server.

**Bad:**

```javascript
function makeBankAccount() {
  // ...

  return {
    balance: 0
    // ...
  };
}

const account = makeBankAccount();
account.balance = 100;
```

**Good:**

```javascript
function makeBankAccount() {
  // this one is private
  let balance = 0;

  // a "getter", made public via the returned object below
  function getBalance() {
    return balance;
  }

  // a "setter", made public via the returned object below
  function setBalance(amount) {
    // ... validate before updating the balance
    balance = amount;
  }

  return {
    // ...
    getBalance,
    setBalance
  };
}

const account = makeBankAccount();
account.setBalance(100);
```

**[⬆ top](#introduction)**

## **Classes**

### Use method chaining

Method chaining allows your code to be more expressive, and less verbose.

In your class functions, simply return `this` at the end of every function,
and you can chain further class methods onto it.

**Bad:**

```javascript
class Car {
  constructor(make, model, color) {
    this.make = make;
    this.model = model;
    this.color = color;
  }

  setMake(make) {
    this.make = make;
  }

  setModel(model) {
    this.model = model;
  }

  setColor(color) {
    this.color = color;
  }

  save() {
    console.log(this.make, this.model, this.color);
  }
}

const car = new Car("Ford", "F-150", "red");
car.setColor("pink");
car.save();
```

**Good:**

```javascript
class Car {
  constructor(make, model, color) {
    this.make = make;
    this.model = model;
    this.color = color;
  }

  setMake(make) {
    this.make = make;
    // NOTE: Returning this for chaining
    return this;
  }

  setModel(model) {
    this.model = model;
    // NOTE: Returning this for chaining
    return this;
  }

  setColor(color) {
    this.color = color;
    // NOTE: Returning this for chaining
    return this;
  }

  save() {
    console.log(this.make, this.model, this.color);
    // NOTE: Returning this for chaining
    return this;
  }
}

const car = new Car("Ford", "F-150", "red").setColor("pink").save();
```

**[⬆ top](#introduction)**

### Prefer composition over inheritance

You should prefer composition over inheritance when you can. 

If your mind instinctively goes for inheritance, try to think if composition could model your problem better. 

When should you use inheritance? This is a list of when inheritance
makes more sense than composition:

1. Your inheritance represents an "is-a" relationship and not a "has-a"
   relationship (Human->Animal vs. User->UserDetails).
2. You can reuse code from the base classes (Humans can move like all animals).
3. You want to make global changes to derived classes by changing a base class.
   (Change the caloric expenditure of all animals when they move).

**Bad:**

```javascript
class Employee {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  // ...
}

// Bad because Employees "have" tax data. EmployeeTaxData is not a type of Employee
class EmployeeTaxData extends Employee {
  constructor(ssn, salary) {
    super();
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}
```

**Good:**

```javascript
class EmployeeTaxData {
  constructor(ssn, salary) {
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}

class Employee {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  setTaxData(ssn, salary) {
    this.taxData = new EmployeeTaxData(ssn, salary);
  }
  // ...
}
```

**[⬆ top](#introduction)**

## **SOLID**

### Single Responsibility Principle (SRP)

There should never be more than one reason for a class
to change. 

It's tempting to pack a class with a lot of functionality. The issue with this is that your class won't be cohesive, which will give it many reasons
to change. 

Minimizing the amount of times you need to change a class is important.

It's important because if too much functionality is in one class and you modify a piece of it, it can be difficult to understand how that will affect other dependent modules in your codebase.

**Bad:**

```javascript
class UserSettings {
  constructor(user) {
    this.user = user;
  }

  changeSettings(settings) {
    if (this.verifyCredentials()) {
      // ...
    }
  }

  verifyCredentials() {
    // ...
  }
}
```

**Good:**

```javascript
class UserAuth {
  constructor(user) {
    this.user = user;
  }

  verifyCredentials() {
    // ...
  }
}

class UserSettings {
  constructor(user) {
    this.user = user;
    this.auth = new UserAuth(user);
  }

  changeSettings(settings) {
    if (this.auth.verifyCredentials()) {
      // ...
    }
  }
}
```

**[⬆ top](#introduction)**

### Open/Closed Principle (OCP)

As stated by Bertrand Meyer, "software entities (classes, modules, functions,
etc.) should be open for extension, but closed for modification." 

What does that mean? 

This principle states you should allow users to add new behaviours without changing existing code. Extensability.

**Bad:**

```javascript
class AjaxAdapter extends Adapter {
  constructor() {
    super();
    this.name = "ajaxAdapter";
  }
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
    this.name = "nodeAdapter";
  }
}

class HttpRequester {
  constructor(adapter) {
    this.adapter = adapter;
  }

  fetch(url) {
    if (this.adapter.name === "ajaxAdapter") {
      return makeAjaxCall(url).then(response => {
        // transform response and return
      });
    } else if (this.adapter.name === "nodeAdapter") {
      return makeHttpCall(url).then(response => {
        // transform response and return
      });
    }
  }
}

function makeAjaxCall(url) {
  // request and return promise
}

function makeHttpCall(url) {
  // request and return promise
}
```

**Good:**

```javascript
class AjaxAdapter extends Adapter {
  constructor() {
    super();
    this.name = "ajaxAdapter";
  }

  request(url) {
    // request and return promise
  }
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
    this.name = "nodeAdapter";
  }

  request(url) {
    // request and return promise
  }
}

class HttpRequester {
  constructor(adapter) {
    this.adapter = adapter;
  }

  fetch(url) {
    return this.adapter.request(url).then(response => {
      // transform response and return
    });
  }
}
```

**[⬆ top](#introduction)**

## **Testing**

If you don't write tests, you're implicitly accepting that you may be shipping broken code.


### Single concept per test

**Bad:**

```javascript
import assert from "assert";

describe("MomentJS", () => {
  it("handles date boundaries", () => {
    let date;

    date = new MomentJS("1/1/2015");
    date.addDays(30);
    assert.equal("1/31/2015", date);

    date = new MomentJS("2/1/2016");
    date.addDays(28);
    assert.equal("02/29/2016", date);

    date = new MomentJS("2/1/2015");
    date.addDays(28);
    assert.equal("03/01/2015", date);
  });
});
```

**Good:**

```javascript
import assert from "assert";

describe("MomentJS", () => {
  it("handles 30-day months", () => {
    const date = new MomentJS("1/1/2015");
    date.addDays(30);
    assert.equal("1/31/2015", date);
  });

  it("handles leap year", () => {
    const date = new MomentJS("2/1/2016");
    date.addDays(28);
    assert.equal("02/29/2016", date);
  });

  it("handles non-leap year", () => {
    const date = new MomentJS("2/1/2015");
    date.addDays(28);
    assert.equal("03/01/2015", date);
  });
});
```

**[⬆ top](#introduction)**

## **Concurrency**

### Use Promises, not callbacks

Callbacks are confusing, it's easy to end up in callback hell. Prefer promises over callbacks when possible.

**Bad:**

```javascript
import { get } from "request";
import { writeFile } from "fs";

get(
  "https://en.wikipedia.org/wiki/Robert_Cecil_Martin",
  (requestErr, response, body) => {
    if (requestErr) {
      console.error(requestErr);
    } else {
      writeFile("article.html", body, writeErr => {
        if (writeErr) {
          console.error(writeErr);
        } else {
          console.log("File written");
        }
      });
    }
  }
);
```

**Good:**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

get("https://en.wikipedia.org/wiki/Robert_Cecil_Martin")
  .then(body => {
    return writeFile("article.html", body);
  })
  .then(() => {
    console.log("File written");
  })
  .catch(err => {
    console.error(err);
  });
```

**[⬆ top](#introduction)**

### Utilise Async/Await over Promises

async and await offer an even cleaner solution than promises. 

Prefix the `async` keyword to your functions, and then you can write your logic imperatively without a `then` chain. 

**Bad:**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

get("https://en.wikipedia.org/wiki/Robert_Cecil_Martin")
  .then(body => {
    return writeFile("article.html", body);
  })
  .then(() => {
    console.log("File written");
  })
  .catch(err => {
    console.error(err);
  });
```

**Good:**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

async function getCleanCodeArticle() {
  try {
    const body = await get(
      "https://en.wikipedia.org/wiki/Robert_Cecil_Martin"
    );
    await writeFile("article.html", body);
    console.log("File written");
  } catch (err) {
    console.error(err);
  }
}

getCleanCodeArticle()
```

**[⬆ top](#introduction)**

## **Error Handling**

Thrown errors are useful. Utilise them for when something goes wrong, and have a plan for how to deal with it.

### Don't ignore errors

Ignoring a caught error doesn't give you the option to  fix
or react to said error now or later.

Logging the error (`console.log`) isn't better as often it can get lost in a stream of unrelated logs. 

If you wrap any code in `try/catch` you should have a plan,
or a code path, for when it occurs.

**Bad:**

```javascript
try {
  functionThatMightThrow();
} catch (error) {
  console.log(error);
}
```

**Good:**

```javascript
try {
  functionThatMightThrow();
} catch (error) {
  // One option (more noisy than console.log):
  console.error(error);
  // Another option:
  notifyUserOfError(error);
  // Another option:
  reportErrorToService(error);
  // OR do all three!
}
```

### Do not ignore any rejected promises

Just as you shouldn't ignore caught errors from `try/catch`.

**Bad:**

```javascript
getdata()
  .then(data => {
    functionThatMightThrow(data);
  })
  .catch(error => {
    console.log(error);
  });
```

**Good:**

```javascript
getdata()
  .then(data => {
    functionThatMightThrow(data);
  })
  .catch(error => {
    // One option (more noisy than console.log):
    console.error(error);
    // Another option:
    notifyUserOfError(error);
    // Another option:
    reportErrorToService(error);
    // OR do all three!
  });
```

**[⬆ top](#introduction)**

## **Formatting**

### Use consistent capitalization

JavaScript is untyped, capitalisation tells you a lot about your variables,
functions, etc. 

These rules are subjective, so just be consistent.

**Bad:**

```javascript
const DAYS_IN_WEEK = 7;
const daysInMonth = 30;

const albums = ["Five Leaves Left", "Tin Drum", "Venus in Cancer"];
const Artists = ["Nick Drake", "David Sylvain", "Robbie Basho"];

function eraseDatabase() {}
function restore_database() {}

class animal {}
class Alpaca {}
```

**Good:**

```javascript
const DAYS_IN_WEEK = 7;
const DAYS_IN_MONTH = 30;

const ALBUMS = ["Five Leaves Left", "Tin Drum", "Venus in Cancer"];
const ARTISTS = ["Nick Drake", "David Sylvain", "Robbie Basho"];

function eraseDatabase() {}
function restoreDatabase() {}

class Animal {}
class Alpaca {}
```

**[⬆ top](#introduction)**

### Function callers and callees should be close

If a function calls or is related to another, keep those functions close in the source file. 

Ideally, keep them together. We read code from top-to-bottom. 

**Bad:**

```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee;
  }

  lookupPeers() {
    return db.lookup(this.employee, "peers");
  }

  lookupManager() {
    return db.lookup(this.employee, "manager");
  }

  getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  perfReview() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();
  }

  getManagerReview() {
    const manager = this.lookupManager();
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.perfReview();
```

**Good:**

```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee;
  }

  perfReview() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();
  }

  getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  lookupPeers() {
    return db.lookup(this.employee, "peers");
  }

  getManagerReview() {
    const manager = this.lookupManager();
  }

  lookupManager() {
    return db.lookup(this.employee, "manager");
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.perfReview();
```

**[⬆ top](#introduction)**

## **Comments**

### Only comment things that are not self evident

Good code should _mostly_ document itself.

**Bad:**

```javascript
function hashIt(data) {
  // The hash
  let hash = 0;

  // Length of string
  const length = data.length;

  // Loop through every character in data
  for (let i = 0; i < length; i++) {
    // Get character code.
    const char = data.charCodeAt(i);
    // Make the hash
    hash = (hash << 5) - hash + char;
    // Convert to 32-bit integer
    hash &= hash;
  }
}
```

**Good:**

```javascript
function hashIt(data) {
  let hash = 0;
  const length = data.length;

  for (let i = 0; i < length; i++) {
    const char = data.charCodeAt(i);
    hash = (hash << 5) - hash + char;

    // Convert to 32-bit integer
    hash &= hash;
  }
}
```

**[⬆ top](#introduction)**

### Don't leave commented out code in your codebase

Leave old code in your history.

**Bad:**

```javascript
doStuff();
// doOtherStuff();
// doSomeMoreStuff();
// doSoMuchStuff();
```

**Good:**

```javascript
doStuff();
```

**[⬆ top](#introduction)**

### Don't have journal comments

There's no need for dead code, commented code,
or any journal comments. Use `git log` for history.

**Bad:**

```javascript
/**
 * 2016-12-20: Removed monads, didn't understand them (RM)
 * 2016-10-01: Improved using special monads (JP)
 * 2016-02-03: Removed type-checking (LI)
 * 2015-03-14: Added combine with type-checking (JR)
 */
function combine(a, b) {
  return a + b;
}
```

**Good:**

```javascript
function combine(a, b) {
  return a + b;
}
```

**[⬆ top](#introduction)**

### Avoid positional markers

Positional markers add noise. 

Let the function and variable names, indentation and formatting give  visual structure to your code instead.

**Bad:**

```javascript
////////////////////////////////////////////////////////////////////////////////
// Scope Model Instantiation
////////////////////////////////////////////////////////////////////////////////
$scope.model = {
  menu: "foo",
  nav: "bar"
};

////////////////////////////////////////////////////////////////////////////////
// Action setup
////////////////////////////////////////////////////////////////////////////////
const actions = function() {
  // ...
};
```

**Good:**

```javascript
$scope.model = {
  menu: "foo",
  nav: "bar"
};

const actions = function() {
  // ...
};
```

**[⬆ top](#introduction)**

