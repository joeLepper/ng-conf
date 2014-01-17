# Keynote

Misko Hevery & Brad Green

* Started in 2009
* 'Angular' because of angle brackets in HTML
* First used in Google for Google Feedback
* Rewrote a 6mo x 3dev project (17000LOC) in 3 weeks x 1dev (1500LOC)
* "You are all liars" -Urs Hoelzle (Google SVP of engineering)
* Google Web Toolkit was its early internal competitor
* Doubleclick conversion from .NET was one of the first projects to prove it out vs. GWT
* "Constructive disobedience"
* By volume of search traffic, 2013 was a HUGE year for Angular
* BreezeJS, _salesforce adapters_, KendoUI, AngularUI
* Firebase becoming the defacto backend prototyping database?
* Angular 2.0 is about the browsers of today
* Angular 1.0 was about the browsers of yesterday
* Team is working on 1.3 but focusing on 2.0
* AngularDart will get to 1.0 before AngularJS gets to 2.0 as learning
* Making Angular more modular, i.e. breaking out the DI functionality
* Angular is going to become an umbrella project building all of these different libraries which make up the Angular of today
* Looking into: benchmark framework, lazy-loading DI/ES6 modules, faster change detection via polyfilling Object.observe
* MOBILE: Building-in offline support, active animations (drag and drop on mobile, sounds like), reduce initial load, improve speed, build flagship app (internal, community-built app)
* API complexity reduction: simplify directives, DI, Type annotations
ES6: Classes and modules, but no annotations
* Adding annotations allows for data and typing (future technologies)
* Angular 2.0 will allow you to write in ES6 syntax and compile to ES5, or just write new ES5 syntax
* _Polymer_: taking advantage of Shadow DOM, Object.observe, and DOM mutation observer polyfills
* Angular 2.0 will be built on top of Polymer
* Debuggability: Zone.js allows for longer stack traces and ignores the $digest / $apply cycles
* diary.js: console and server logging, the sorts of tooling that we'd like to implement

# Angular in 20ish Minutes

Dan Wahlin @DanWahlin

* Going to touch on the key pieces of an app
* Laregely remedial

# Building Massive AngularJS Apps

Google Doubleclick team

* Doubleclick Campaign Manager is our example
* One of the oldest (Nov. 2010) and largest Angular apps
* 20+ JS engineers
* 130 controllers, 137 directives, 59 services (interesting that there's more idrectives than controllers!)

## Authorization and conditional features

* return Active User profile for every active user. Return an empty object for non-authed users
* Used to do everything on the client for auth
* This worked when the app was small, but wasted a lot of time as it grew and bootstrapping time increased (you're bootstrapping an app for a user that's not authed, then kicking them out)
* This meant that the app scope and its children were tightly coupled (bad for tests)
* No good way to pass this profile to services
* solved this by server-side rendering index.html only and putting the active user model into the index.html on the server (saves a round-trip)
* make the active user model an injectable service


## Conditional Features (Feature Flags)

* conditional features are taken care of by the server, add a script tag for each conditional feature
* Limiting access to controllers is handled by injecting the active user into a conditional controller and letting the controller decide if the user has access
* remove conditional HTML by custom omit and keep using the response interceptor to strip the HTML out of any response from the server
* This method won't work with $templateCache - need to ask this guy about that
* Doubleclick uses $templateCache in the client instead of bundling everything into it on the server on the initial load

## Code Organization Patterns

* avoid overly-large controllers
* prevent copypaste
* single responsibility
* easy to locate

* inheritance: create a base controller that other controllers can inherit from, can become overly complex
* can do inheritance via Angular by `$injector.invoke()`
* mixins: `angular.extend()` use sparingly to add a specific behavior to a controller, very tightly coupled to their controllers, possible naming collisions
* composition with JS object: use with simple objects and when performance is critical, but cannot use DI with this method

```
var sorter = function(){//methods}

function myCtrl($scope){
  $scope.sorter = new sorter()
}
```
### preferred methods
* services: common functionality that needs to be shared, fully DI and testable
* composition with "helper controllers": like a regular object, but invoked with `$controller`: use when need to access other Angular-provided objects, allows for __multiple instances__

```
function dogWalker(){//methods}
function myCtrl($controller, $scope){
  $scope.sorter = $controller(dogWalker);
}
```
* to share state between page and the helper controllers, you can create a child scope via `$scope.new()`
* prefer composition over inheritance
* minimize mixins
* frameworks help, but don't forget OO principles

## Going beyond Built-in Services

* use a provider in your config block to do REST-api-specific setup for your app
* we can work with logical entities instead of raw server data
* we can use promises to automatically wrap response data in instantiated objects
* we can use `$injector.instantiate` so that the models are able to be injected
Very useful if the response data is in a stupid format and the server is outside of your control
* we can add useful methods on our model objects (like `afterLoad` or `beforeSave`) that can be automatically invoked
* lets you marshal and massage your data outside of your controllers _so you can keep your controllers skinny_
* can simplify our tests as URLs get longer and uglier by wrapping httpBackend
* this method helps by being more readable and less boiler-platey, as well as being able to granularly assert expectations about AJAX
* Angular provides you the basics to build your own tools, identify where you need new tooling and don't forget about test tooling

# Going Postal with Promises

[Christian Lilley](christianlilley.com)

* Promises are about uncertainty caused by async
* Helps us to avoid callback Hell / pyramid of doom
* Allows for error handling and simpler chaining of async operations
* Promises are a design patter that comes to us from functinoal programming
* allows us to reason about async programs more efficiently
* Allows us to go from imperative asyc to declarative async
```
step1.
  then(step2).
  then(step3).
  then(step4)
```

OR, more awesomely:
```
var a = step1
var b = a.then(step2)
var c = b.then(step3)
```

OR
```
$Q.all([a, b, c])
  .then(step4)
```

* at its root, a promise is a mailbox - a standard way to stash the output from an operation
* If a promise is a mailbox, then deferred is the postman
* the deferred is the only one that is allowed to assign new mailboxes, put packages in the mailbox, or alert you to the mail's arrival
* promises aren't _just_ for async requests
* using promises we can use interfaces that are API driven

## How to start:

* consume promises returned by built-in services like `$http` et al
* use `$resource` which now returns a resource object that _has_ a promise
* `$route:resolve` __THE ROUTE WON'T CHANGE UNTIL YOU HAVE EVERYTHING YOU NEED__
* Automatic unwrapping in views is gone in 1.2, but was sort of cool (use resolve or manual unwrapping)
* start creating your own promises with `$Q`
* third-party libraries use promises, check them out
* wrap a promise in an Angular service to create __lazy promises__
* http interceptors are awesome and let you grab every HTTP as it goes out or comes into the browser, can be used to intercept and create promises

## Deep Dive Into Directives

* use a custom prefix to avoid clobbering
* where to use directives: to make custom components (to be replaced by custom elements in the browsers)
* this guy suggests wrapping up jQuery widgets in elements - but I hate that idea
* directives are the only place you should manipulate the DOM, if you feel like testing
* if your directives are in custom modules, it's better for testing
* link is where we'll do all of our mouse / touch / user interaction behavior definitions, it is called once for each instance of the directive on the page
* directives can be behavioral, or widgets
* isolated scopes allow for reuse, clean interfaces, and avoids guilty knowledge about the directives environmnet, plus makes it testable
* @ allows the parent scope to use double curlies when it's handing a string in. Those curlies will be interpolated before the string is handed off
* the attrs object of the directive definition object allows us to pass attrs, interpolated in the parent environment which can be interpolated by the directive using `$eval`
* compile and link are very rarely used, but could be used for some sort of lazy-load for perf improvement
* lazy-loading directive saved a lot of pain from page load time - adds DOM and then destroys itself

# Typescript and Angular

* typescript allows you to have static typing in Javascript
* install the typescript compiler via NPM
* like a compiled language, the compiler gives you some error-checking support so you don't have to debug at runtime
* Going to work with ES6 as the standards land
* ALlows us to think about data models more clearly
* build a data type or interface
* Means that we can write more functionally and reason clearly aobut inputs and outputs
* Need to include a "definition file" to work with Angular github.com/definitelyTyped

# Firebase and Angular

* Proposal: that Angular is the pinnacle of web dev (kind of silly)
* And that firebase is the pinnacle of PAAS
* "Realtime Application Platform"
* An API to store and sync data to subscribed clients in real time
* simple login service to auth users (generally through Facebook or Twitter, but also through email/pass)
* flexible security system for data (declarative security rules)
* I wonder if this works via sockets or long-polling
* announces Firebase hosting beta: firebaseapp.com
* Available for free right now
* A lot like Heroku, wrapper around git
* uses firebase CLI

# Directives that Scale

* Lessons learned from building Kendo UI
* Declarative initialization
* If you have to write one directive for every widget in a massive app you'll have a hrd time
* I actually find this talk somewhat less than useful (not as advertised)

## What if Our Directives Could Build Themselves?

* Widgets share a common API (value and change)
* Don't build directives (just build one directive that builds other directives)
* iterate through the namespace and build the directives that are present on the kendo namespace
* iterate through each widget in the namespace and create a k-attribute
* Use Angular responsibly

# Angular and Dart

* scalable web app engineering
* Google is using Dart
* Dart is a language and ecosystem trying to solve the problem of large webapps

# [Zone.js](https://github.com/btford/zone.js/tree/master)
@briantford


* zones are like domains in node
* Dart has zones
* zone.js is a port of Dart's zones to JS
* Like open-heart surgery on the web browser
* A zone is an extension context

Consider this:
```
a()
setTimeout(b, 0)
setTimeout(c, 0)
d()
```
* zones are like a meta-monkey patch
* helps us to compose behaviors
* zones can be useful for debugging, testing and mocking, profiling...
* a stack trace is only so useful, natively, since it goes back to the place that the async call originated, from the queue, not what it was caused by
* zone.js lets us to log this sort of stack trace to the server
* seems like a useful tool
* only seems to work on IE10 and Chrome32

# AngularJS and Browserify

* Browserify allows node-style requires
* commonJS is synchronous, but browserify fixes that for us
* We can give Browserify an entry file and walk the require tree, it then bundles it into a single file
* supports source maps

## why browserify?

* the syntax is simple
* allows you to use npm modules in the browser
* include Node's eventEmitter if you want
* quick build step so you minimize env differences

## Transforms

* deAMDify, debowerify, coffeeify
* hbsfy: precompiles handlebars templates for you
* es6ify: transpiles es6 modules to ES5 for you

## Angular Implications

* Efficient way to write client-side code, regardless of framework
* Gives you access to a package.json that lets you install from git via npm
* You could write a module that is pure javascript (say a directive definition) and then just pass that in as a require
* still strikes me as a little awkward
* I might like requreJS better
