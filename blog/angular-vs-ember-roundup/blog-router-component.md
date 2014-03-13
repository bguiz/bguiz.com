---
title: Routers and Components in AngularJs and EmberJs
slug: angularjs-vs-emberjs-router-component
author: bguiz
date: 2014-03-11
template: article.jade
tags:
- angularjs
- emberjs
- single-page-apps
- comparison
- router
- component
---

## Routing

Routing is a mechanism through which keeps the state of the application in sync with the URL.
That is, when the URL changes, the application is notified to change to a new state,
and conversely, when the application changes to a new state,
it updates the URL.

If this sounds like familiar territory, that is because it is!
Whenever we use a traditional web site -
where the page refreshes upon each navigation -
this was the only paradigm that was available.
When you navigate to a new page, you expected the URL to change.

Why is routing important, and why does it continue to be needed in single page apps?

- It is what we are used to - when the app goes to a different "page" we expect the URL to change
- Browser history - we expect to be able to use back and forwardbuttons in order tobe able to navigate within the app
- URL sharing - we have an expectation that copy-pasting the URL and sharing it with someone else will bring them to a similar state within the app. This is known as deep linking.
- Deep link within packaged apps - single page web apps can be packaged using tools like PhoneGap into native apps. Deep linking within these apps is also possible, if they use routing too.

Let us take a look at how routing is implmented in AngularJs and EmberJs.

### AngularsJs Routing

In an AngularJs app, you can set up routing very quickly and easily. Here is the syntax:

		App.config(function($routeProvider) {
			$routeProvider
				.when('/foo', {
					controller: 'FooCtrl',
					templateUrl: 'foo.template.html'
				})
				.when('/bar', {
					controller: 'BarCtrl',
					templateUrl: 'bar.template.html'
				})
				.otherwise({
					redirectTo: '/foo'
				});
		});

It is so simple, that it is self-explanatory, really, but we shall just go through the key bits.
AngularJs comes with a built-in module `$routeProvider` through which provides routing capabilty.
This module merely needs to be configured by the developer.
At the bottom, we provide a "catch-all" or default route that will redirect to one of the known routes.
We provide a mapping between URLs and states that correspond to them.
Here each state is defined as a controller and a template.
Note that the state is actually represented by a model, a view, and a controller - 
essentially an MVC state -
in the router we name the controller,
and the template (which is effectively the view),
and we know that the controller contains logic that will obtain the model.

#### URLs in single-page apps

In single-page apps, the URL is technically always the same.
That is because, we are *technically* staying on the same page,
and merely *pretending* to navigate between different pages by using lots of Javascript.

However, this would means that we would lose all the benefits of our app being URL-driven,
and thus we need a way to overcome this.
Thankfully there are a couple of ways to do so -
one which works on all browsers,
and another which will only work with browsers that support HTML5

The **first method** is using hash fragments.

These have been around since the early days of the internet,
where you would have links that did not link to a different page, 
but rather a different position on the page.
When you clicked on them, your browser would merely scroll to a different position on the page.
The other thing you would observeis that the URL would get suffixed with '`#`', followed by the name of that link.
Browsers would store these changes in hash fragments in their history,
and thus it was possible to use the back and forward buttons to jump between the different scroll positions on the page.

Try it out for yourself!
Go to any article on [Wikipedia](http://en.wikipedia.org) and find citations in line with the text.
Many of these are hyperlink that will jump you down to the bottom of the page.
Click on a few of these in a row, and then use the back and forward buttons.
Neat right?

Well, when the developers of AJAX-heavy Web 2.0 sites,
and later on developers of single-page application frameworks saw this,
they thought so too.
They decided to use this as a means to store the identity of the app's current state,
thus enabling routing to occur, even though the user does not *technically* move away from the one page.
All they had to this idea to get full blown routing was instead of using single words or numbers for IDs,
instead simply use a string that was effectively the URL for the current route within the app,
e.g. "`#/foo/123`" or "`#/foo/123/edit`"

The **second method** is to use the history API.
The history API is a far more recent thing,
as it is part of the HTML5 specification.
Only newer browsers support this specification,
unlike hash fragments, which understood by pretty much every browser today.

It is not very much different from the hash fragment means of storing the application state in the URL.
All it really does, is provide a way to tell the server that this particular URL path is served by an app that is history API enabled,
and therefore, the rest of the string in the path that comes after it should be ignored by the server,
and merely passed on the page serving the app.
The app then obtains the remainder of the path that was passed by the server,
via the history API, and uses that just as it would the hash fragment URLs described earlier.

How is the end result different?
Well, the obvious difference is that the end user does not see the hash fragment -
the URLs that change when navigating between different states within a single-page app, 
they may as well be a traditional web site.
The end user will not perceive the difference.
There are a few other minor difference as well (which we will not go into).

- syntax router
- HTML5 history API support
- finite state machine
- ui-router

### EmberJs Routing

- syntax router
- syntax routes
- HTML5 history API support
- hierarchical state machine
- hierarchical routes demo

- What is the syntax used to express a router?
- Does the router support a flat or hierarchical series of routes?
- What additional libraries do you need to use the router?

## Components

- define compnonents
- web components spec

### AngularJs components

- building a DSL using directives
- no special syntax for components
- just use directives and isolate their scope
- syntax of a scope isolated, element restricted directive
- relation to web components spec

### EmberJs components

- syntax of Ember.Component
- learning curve
- relation to web components spec

- What is the syntax used to express a component?
- What level of abstraction do these components sit at?
- How well do these components align with the current draft sepcification for web components?

## Discussion

- Link to MVC
- MVC + templates + routing + components cover all the fundamental or "compulsory parts"/ "core competency" of a SPA framework
	- Thus these are the most important
- The remainder of the posts compare these frameworks or higher order differentiating factors
	- These are important too
	- But basics come first - hierarchy of needs
