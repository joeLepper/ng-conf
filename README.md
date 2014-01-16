Keynote
=======
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

Angular in 20ish Minutes
========================
Dan Wahlin @DanWahlin

* Going to touch on the key pieces of an app
* Laregely remedial

BUilding Massive AngularJS Apps
===============================
Google Doubleclick team

* Doubleclick Campaign Manager is our example
* One of the oldest (Nov. 2010) and largest Angular apps
* 20+ JS engineers
* 130 controllers, 137 directives, 59 services (interesting that there's more idrectives than controllers!)
Authorization and conditional features
--------------------------------------
* return Active User profile for every active user. Return an empty object for non-authed users
* Used to do everything on the client for auth
* This worked when the app was small, but wasted a lot of time as it grew and bootstrapping time increased (you're bootstrapping an app for a user that's not authed, then kicking them out)
* This meant that the app scope and its children were tightly coupled (bad for tests)
* No good way to pass this profile to services
* solved this by server-side rendering index.html only and putting the active user model into the index.html on the server (saves a round-trip)
* make the active user model an injectable service


Conditional Features (Feature Flags)
------------------------------------
