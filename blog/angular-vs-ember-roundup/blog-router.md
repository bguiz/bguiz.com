---
title: Routers and Components in AngularJs and EmberJs
slug: angularjs-vs-emberjs-router
author: bguiz
date: 2014-03-11
template: article.jade
tags:
- angularjs
- emberjs
- single-page-apps
- comparison
- router
---

## Routing

Routing is a mechanism which keeps the state of a web application in sync with its URL.
That is, when its URL changes, the app is notified to transition to a new state.
Conversely, when the app transitions to a new state, it updates its URL.

If this sounds like familiar territory, that is because it is!
Whenever we use a traditional web site -
where the page refreshes upon each navigation -
this was the only paradigm that was available.
When you navigate to a new page, you expected the URL to change.

Why is routing important, and why does it continue to be needed in single page apps?

- It is what we are used to - when the app goes to a different "page" we expect the URL to change
- Browser history - we expect to be able to use back and forward buttons in order to be able to navigate within the app
- URL sharing - we have an expectation that copy-pasting the URL and sharing it with someone else will bring them to a similar state within the app. This is known as deep linking.
- Deep link within packaged apps - single page web apps can be packaged using tools like PhoneGap into native apps. Deep linking within these apps is also possible, if they use routing too.

Let us take a look at how routing is implemented in AngularJs and EmberJs.

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
and merely *pretend* navigating between different "pages"
by using Javascript to swap the content of large parts of the page's DOM.

However, this would mean that we would lose all the benefits of our app being URL-driven,
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
Many of these are hyperlinks that will jump you down to the bottom of the page.
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
unlike hash fragments, which understood by all browsers.

It is not very much different from the hash fragment means of storing the application state in the URL.
All it really does, is provide a way to tell the server that this particular URL path is served by an app that is history API enabled,
and therefore, the rest of the string in the path that comes after it should be ignored by the server,
and merely passed on the page serving the app.
The app then obtains the remainder of the path that was passed by the server,
via the history API, and uses that just as it would the hash fragment URLs described earlier.

How is the end result different?
Well, the obvious difference is that the end user does not see the hash fragment -
the URLs that change when navigating between different states within a single-page app.
This single-page app may as well be a traditional web site,
the end user will not perceive the difference.
(Do note that there are a few other minor differences as well,
which we will not go into).

*The 'finite' in finite state machine*

Earlier we mentioned that the routing, at an abstract or conceptual level,
is implemented using state machines.
We also mentioned that AngularJs' `$routeProvider` is implemented a finite state machine.

We have, however, glossed over one very important detail,
and that is that there are dfferent types of state machine.
A finite state machine being just one of them.

The other common type of state machine is the hierarchical state machine.
This is also the type of state machine that EmberJs uses,
and so we will look into them in more detail then.

A hierarchical state machine allows for some states to be nested within others,
in a hierarchy of substates, that together form a composite state.

What impact does the choice of a finite state machine over a hierarchical state machine have,
on apps built with AngularJs though?
As it turns out, quite a significant one.
In traditional web applications, most pages would share common sections, such as the header, footer, navigation menu, or side bars.
Amongst the more complex of these, the content area would contain a certain level of overlap.
Single-page application frameworks also support this concept.

In AngularJs you may apply the `ngView` directive to a DOM element.
When the URL changes, the new state's template and controller are determined,
and the contents of that elements are replaced with what this template an controller say should be there.

Since the router defines a series of finite states,
that `ngView` can only ever be driven my one active controller and template,
and it is not possible to have multiple ones active at the same time,
at least without an in-depth knowledge of AngularJs' inner workings.

This can be quite limiting, and this is a serious limitation of using AngularJs.
Thankfully there is the [ui-router](https://github.com/angular-ui/ui-router) library,
which provides a hierarchical state machine driven router for AngularJs apps.
Some from the AngularJs team have also drafted this proposal on [router design in AngularJs 2.0](https://groups.google.com/forum/#!topic/angular-dev/mO6jmYhvsMk), which draws inspiration from both `ui-router` and EmberJs' router (and a few others);
thus AngularJs may support this in the future as it appears that the community is behind it.

### EmberJs Routing

In EmberJs, routing keeps the state of the web app in sync with the current URL.

Routing in EmberJs, compared to routing in AngularJs, is significantly more complex.
The two primary differences are that:

- The responsibility for routing is split between a `Router` object and multiple `Route` objects, and they have more responsibilities.
- Routing uses a heirarchical state machine, and composite states need to be considered.

		App.Router.map(function() {
			this.resource('foos', function() {
				this.route('foo', {path: '/:foo_id'});
			});
			this.route('bar');
		});

The syntax is a little more complex.
To understand how routing works in EmberJs,
it is necessary to first understand EmberJs' philosophy on convention over configuration -
in particular, its naming conventions.

When you instantiate a new app using
`var App = Ember.Application.create({})`,
the framework will create a `Router` object, `App.Router`.
This is the first of many naming conventions.

What remains is for us to tell the router which URLs correspond to which states.
This is done using the `map` function on the `Router` object.
The map function takes in a function as its first parameter.
Within this function, call `this.resource` and `this.route` in a hierarchy,
with `this.route` at the leaf nodes, and `this.resource` when there are child nodes.
Both functions take in a string as the first parameter
That string is,by default, a section of the URL.

That string is also what EmberJs will use as the input for other objects that represent the state.
The naming conventions are described in detail in the
[EmberJs guides](http://emberjs.com/guides/concepts/naming-conventions/).

- URL: `/`
	- Route: `IndexRoute`
	- Controller: `IndexController`
	- Template: `index`
- URL: `/foos`
	- Route: `FoosRoute`
	- Controller: `FoosController`
	- Template: `foos`
- URL: `/foos/123`
	- Route: `FooRoute`
	- Controller: `FooIndexController`
	- Template: `foo/index`

This is actually a little hard to grok, and this
[EmberJs routes guide](http://emberjs.com/guides/routing/defining-your-routes/)
is a detailed, but excellent reference,
essential in determining what you should name your `Route`s, `Controller`s and templates.
If you name them any differently from what EmberJs expects them to be named,
they simply are not found, and defaults are used instead.
This is quite difficult at first, as it is quite easy to get these names wrong.

#### Routes

The second part of the picture in routing in EmberJs are the `Route` objects.

		App.FoosRoute = Ember.Route.extend({
			model: function() {
				return [{name: 'f1'}, {name: 'f2'}, {name: 'f3'}];
			}
		});

Most `Route` object simply will implement the `model` hook to return the model.
If the corresponding controller is an `ArrayController`,
the model is expected to be an `Array`.
If the corresponding controller is an `ObjectController`,
the model is expetced to be an `Object`.
However, it is most likely that your app does not want to use hard coded models like so.
Most likely, you will want to dynamically load data from a server,
using AJAX.
There are libraries which cover this quite well,
such as Ember Data and Ember Model.
However, it is possible to use AJAX directly without wrapping the objects in a model object.

		App.FoosRoute = Ember.Route.extend({
			model: function() {
				var req = Ember.RSVP.resolve(jQuery.getJSON('/api/foos'));
				return req.then(function resolve(response) {
					return response.foos;
				});
			}
		});

Here we use `jQuery` to perform the AJAX request.
AJAX requests, are, of course, asynchronous,
and thus they need to be handled using either callbacks or promises,
and in EmberJs, we will use promises,
as EmberJs uses promises for all async return types.
JQuery does itself return a promise-like object,
however, it is not fully compiant with the Promises/A+ specification,
which EmberJs requires.
Thus we need to wrap it with `Ember.RSVP.resolve`,
a utility function from EmberJs' own internal promises library.
This is a really annoying gotcha when just starting out with EmberJs,
and it would be a good idea if this was better documented,
or if EmberJs ships with its own AJAX constructs,
like what AngularJs does with its `$http` module.

#### Promises and async

When we get data over the network in a single-page app,
we use AJAX - asynchronous Javascript and XML.
This is accomplished using a native code wrapped in a global Javascript object, `XMLHttpRequest`.
Javascript has been single threaded from the get-go,
and only recently, in the ECMAScript 6 spec,
and this poses a fundamental problem for AJAX.
We cannot halt the execution of the rest of the app,
while waiting for the response to come back from the server,
as it certainly takes a noticeable amount of time.

Thus we need a way to execute code asynchronously.
This is solved using callback functions,
where we pass in a reference to a function to the `XMLHttpRequest`,
and then simply put code to handle the response (or error message in the case of rejection) in that function.
Combined with Javascript's concept of closures,
this allows us to *emulate* multi-threaded programming concepts quite well.

That being said, callbacks do have their limitations.
Their limitations led to the development of a concept called promises,
which are clever wrappers around callbacks which provide a very nice interface to work with asynchronous code -
much nicer than callbacks.

If you would like to know more about the frustrations of using callbacks -
and in using promises too - check out my [presentation on qryq](http://bguiz.github.io/qryq),
a NodeJs library I wrote to help with [managing promises with dependencies between them](http://github.com/bguiz/qryq).

For these reasons, and others, the more modern Javascript frameworks
have embraced promises as their go to means of managing asynchronous code.
AngularJs and EmberJs both have their own promise libraries embeddedwithin them.
AngularJs uses `q` (which [`qryq`](http://github.com/bguiz/qryq) also uses),
and EmberJs uses `Ember.RSVP`.

The difference between AngularJs and EmberJs is not really much,
because both of their promise library implementations follow the same specification,
the Promises/A+ spec.
However, the difference between these two frameworks lies in their flexibility.
In AngularJs, you can avoid using promises if you do not wish to, the choice is yours.
With EmberJs on the other hand, for anything asynchronous, it would be extremely hard to avoid using promises.
Thus if you have existing Javascript code that uses callbacks,
in EmberJs, be prepared to rewrite them using promises.

#### Hierarchical Routes

AngularJs routing uses a finite state machine under the hood.
EmberJs routing, on the other hand, uses ahierarchical state machine under the hood.
The difference between these two is evident when inspecting the routers.
In AngularJs' router syntax, using `$routeProvider`,
we see that all states are define at the same level.
In EmberJs' router syntax, using `Ember.Router.map`,
we see that states might define other states "nested" within them.
This is where the hierarchy comes into play.

This `map` function tells Ember how to define the hierarchy of URL fragments.
The composite of these URL fragments is obtained by concatenating these strings together.
That is one side of the equation - the URLs.

The second half of the equation is comprised of the state.
This state consists of a `Route`, a model, a `View` plus its template, and a `Controller`.
But where do we provide these mappings?
That is where the EmberJs' naming conventions come in!

When we define a route mapping like this:

		this.route('bar');

EmberJs knows to look for `FooRoute`, use the model returned by its `model` hook, `FooView` and a template named `foo`, and finally `FooController`.

Alternatively, when we define a route mapping like this:

		this.route('foo', {path: '/:foo_id'});

The same naming conventions as above apply,
except that we have overridden the default URL fragment -
instead of `/foo`, it will be `/:foo_id`, a dynamic fragment.
It is also possible to similarly override the defaults for the objects that comprise the state too.

Assembling these states together into a composite state
is not quite as assembling the composite URL for that state,
but it is quite a powerful tool to have when building a web app.

EmberJs simply keeps all the current `Route`s, models, `View`s, and `Controller`s
defined by each of the active states active at the same time to accomplish this.
Templates are slightly more complex.
In summary, for multiple templates to be active in a hierarchy,
each one must be defined within the others.
This is accomplished using the Handlebars helper, `{{outlet}}`.
That simply tells the current template that,
when its `Route`'s child `Route` is active,
the template belonging to that child `Route` should be rendered where `{{outlet}}` is.

One common pattern that this enables is the master-detail pattern.
This is where the user interface displays a list of items,
perhaps just the name and one other piece of information about each item,
in one section of the page,
and when each of these is clicked on,
the full detail of that item is displayed in another section of the page.

This article by UX Magazine on
[application screen design](http://uxmag.com/articles/rich-internet-application-screen-design)
lists quite a number of the most common user interface screen layouts.
Several of these are only possible using when the user interface supports the concept of hierarchy, as EmberJs does;
with several others not requiring, but made much easier.

Describing everything put together here is a little complex,
however, this JsBin [demonstrates hierarchical routing in EmberJs](http://jsbin.com/wiyin/4/edit?html,js,output) rather succinctly.
EmberJs' router is extremely powerful,
allowing us to describe hierarchical states within the app quite easily.
The hardest part of it really is just getting used to the naming conventions.

## Discussion

Unlike AngularJs, no external libraries are necessary to meet routing needs,
the one that EmberJs comes with is powerful enough to handle most use cases.

AngularJs' routing syntax is a lot less complex and involved,
and is best for simple apps.
EmberJs' routing syntax is a lot more complex,
but with the added complexity comes a lot of additional features.

AngularJs' routing does not do very much different from very basic routing implementations,
such as BackboneJs' implementation, and comes across as an afterthought.
EmberJs' routing is a huge improvement over simple routing implementations;
and the developers of EmberJs consider it to be the most important part of any EmberJs app.

When it comes to routing, EmberJs' approach shines, and AngularJs' approach pales in comparison.
