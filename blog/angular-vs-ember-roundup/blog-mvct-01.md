---
title: Models, Views, Controllers, and Templates in AngularJs and EmberJs
slug: angularjs-vs-emberjs-mvc-template-part-1
author: bguiz
date: 2014-03-08
template: article.jade
tags:
- angularjs
- emberjs
- single-page-apps
- comparison
- MVC
---

## MVC + Templates

Model-View-Controller (MVC) is a common software architecture pattern that is popular in web applications. 

![Model-View-Controller interactions](http://upload.wikimedia.org/wikipedia/commons/thumb/a/a0/MVC-Process.svg/200px-MVC-Process.svg.png)

### A brief history of single-page applications

**Timeline**

- One page load per navigation
- Add AJAX
- Web 2.0
- Add MVC patterns
- Single-page applications

In the early days of the web, all web sites made one request to the server, 
and the server responded with the entire web page that shoudl be displayed. 
Query parameters and HTTP request headers were about as much variation as one could achieve.

Then Microsoft came up with XMLHttpRequest, 
a means to send a request to the server and receive the request, 
entirely using Javascript run in a web page.
Quite often, this was used to swap the contents of one `<div>` on the page verbatim with the returned AJAX response.
Responses were not restrited to XML, and JSON gained rapid following, as it was the easiest to parse natively with Javascript.
This disrupted the status quo of one page load per request, 
and created an explosion in the amount of interactivity possible on a page.

Web developers did exactly that, making their sites very interactive, 
and thus was born the era of "Web 2.0" web sites. 
However the client-side Javascript quite often ended up becoming a tangled mess of unstructured Javascript and jQuery when the site tried to do something complex.

However, server side developers had been using the MVC pattern for some time now, 
and there was no reason to not do so client side too. 
After all, since web sites were no longer thin-clients, 
it became necessary to ad some code organisation and structure to them.

There were a several frameworks to come out before, but BackboneJs really nailed it. 
It combined the use of AJAX with MVC patterns in a Javascript library that allowed front end developers to create a single-page application;
an app where every page was simply Javascript used to re-render (most of) the visible page in the client, and none of the requests navigated you away from `index.html`.

This combination of AJAX and the MVC pattern is the cornerstone of single-page application frameworks. 
BackboneJs is certainly not the only single-page application framework, 
it merely was the first to gain widespread popularity.
There a currently a [plethora of different frameworks](http://todomvc.com) looking to take its place,
two of which are AngularJs and EmberJs.

### MVC in single-page application frameworks

Prior to single-page applications, 
this logic was coded almost entirely on the server - thin-client, thick-server.
However, with single-page applications, web apps are thick-clients, 
and a lot of this logic has been shifted to the client, 
and the front end developer now needs to be well versed in this pattern.

Most single-page application frameworks are designed around the MVC pattern, or variations of it. 

Models, views, and controllers are at the core parts of most single-page application frameworks, and they also include a few more: 
Namely templates, routing, and components. 
This post will discuss models, views, and controllers, as well as templates, because they are so closely connected to views.
Routing and components will be dscussed in a subsequent post.

## Models

Models are data objects that are presented and manipulated by the app.
Single-page application frameworks come with methods to manage models. 
They sometimes also come with methods to sync their models with their counterparts on the server/ database
(analogous to Object-Relational Mapping on the server), 
often through a RESTful interface.

### AngularJs Models

Models in AngularJs are extremely simple - they are plain old Javascript objects (POJSOs):

`var myModel = { id: 1, name: 'foo1' };`

Bam! There you go, you have got yourself an Angular model!

If we want to change the state of the model, the syntax is not different from manipulating POJSOs:

`myModel.name = 'myNewName';`

If we want to read the current state of the model, again, the syntax is no different from accessing POJSOs:

`myModel.name` (evaluates to "myNewName")

Hang on, can it really be so simple&hellip; Where is the catch?

In order to get much use out of these models,
you set them as a property on the `$scope` of a controller.

&hellip; Simple!

Furthermore there are implications of using a POJSO for models on data binding, between the model and the view, to work correctly. 
It means that we need to do dirty checking on them at a particular interval to check if they have changed.
A complete explanation of this would require a discussion on `$scope`, `$watch`, dirty checking, digest, cycles, and run loops -
which is pretty heavy-going stuff (we will only get to this towards the end of this series).

&hellip; Tricky!

Essentially, Angular models are extremely simple to get up and running with the basic use cases. Once you get to the more complex use cases however, it gets hairy very quickly, and requires some advanced understanding of the inner workings of the framework.

That being said, this is a prety good deal most times, because you get 80% of the way for 20% of the effort. 

#### Sync&apos;ing Angular Models

Angular models are just POJSOs,
thus when you need to sync them with the server,
you are left to implement the means yourself.

The following methods are the most popular ways to accomplish this, arrange in order of increasing levels of abstraction:

- `$http`
	- An angular directive at the same level as `jQuery.ajax` - specify the AJAX requests on a per-interaction basis
- `$resource`
	- An angular directive that provides a means to define a wrapper around `$http` to group common patterns of AJAX requestsfor models together
- restangular
	- This does not come with AngularJs, and is a 3rd-party library. It provides allthe bells and whistles that make it easy to map your models to a RESTful API.

The specifics of the syntax required to use each of these methods falls beyond the scope of this article.
However, it would suffice to say that all of them are fairly straight forward to use,
the hardest part of this lies in deciding which level of abstraction,
and thus which method is the most appropriate for your app.

This is great for the developer, because there are three options available, and all of them are pretty good.

### EmberJs Models

The syntax used to express a Model is slightly more complex than Angular,
as they have to be "wrapped" in an Ember wrapper object.

`var Foo = Ember.Object.extend({});`

This defines a class (more specifically, a constructor function) for an object named `Foo`. You do not have an instance of `Foo` yet &hellip;

`var myModel = new Foo({ id: 1, name: 'foo1' })`

&hellip; and now we have one. So why did we have to create a class and instantiate it? 

Ember models need to conform to a certain interface in order to work with the rest of the framework. Most important amongst these is that attributes on the models are accessed and mutated using `.get()` and `.set()`.

If we want to change the state of the model, we have to to use `.get()`:

`myModel.set('name', 'myNewName');`

If we want to read the current state of the model, we have to use `.set()`:

`myModel.get('name')` (evaluates to "myNewName")

#### Sync&apos;ing Ember Models

The syntax above was for `Ember.Object`, 
which provide a versatile wrapper object around almost every class in the framework. 
This general purpose wrapper is sufficient for use as models, 
provided that you are happy to write your own AJAX for all operations.
This [article shows one great way to do this][eviltrout-ember-without-ember-data].

However, the Ember framework does get in the way of this somewhat,
because it is designed to work better at a higher level of abstraction.
The canonical way to do this is with ember-data,
a library that is developed by the same core team as EmberJs itself.

		var Foo = DS.Model.extend({
			name: DS.attr('string')
		});
		var myModel = new Foo({
			name: 'foo1'
		});

The primary difference is that you are here extending a `DS.Model` instead of `Ember.Object`,
and when doing so, you define each of the attributes for any instance of that model,
as well as the type of that attribute.
These definitions provide the means for object relational mapping to occur,
and for ways to customise this mapping.

It also provides the means to define relationships between models of one class and models of another class.

		var Foo = DS.Model.extend({
			name: DS.attr('string'),
			bars: DS.hasMany('bar') 	
		});
		var Bar = DS.Model.extend({
			name: DS.attr('string'),
			foo: DS.belongsTo('foo') 
		});
		//... create a few Bar models
		var myModel = new Foo({
			name: 'foo1',
			bars: [1001, 1002, 1003] //IDs of the bars
		});

Ember data then allows you to sync the state of these models via a RESTful interface that conforms to a format specified by them.
Hint: It is the same as the Ruby on Rails API format.

All batteries included, so it is all peaches, right?
&hellip; not quite!

The problem is that ember data is not yet complete. 
It is still in rapid development, 
and the team developing it puts a big warning label on it,
saying it is not ready for production.

Even if you are willing to take that risk with the app you are building,
it poses another problem,
and that is that it is not very flexible.
While it does provide a means to customise the models and the way it syncs with the server,
it is within bounds, and doesn&apos;t provide full flexibility to define your own AJAX requests.

To solve this problem, there are a [slew of Ember Data alternatives][alts-ember-data]. 
Amongst these, the stand out is [Ember Model][ember-model].

- Developed by Erik Bryn, a core member of the Ember team. As such, its syntax is very tight with that of Ember Data, and seems like a drop in relacement for it
- It allows for a way to define adapters where you can define your own AJAX calls, on a per model class basis.

When you need to sync models with the server in Ember, 
the developer is put in a rather hard spot, 
because of the limitations of the options available.
Chances are, quite a lot of time will be wasted in developing solutions in each of these approaches,
before you get one of them working in your app;
due to a combination of not being ready, and a lack of flexibility.

### How this *might* change in ES6

There is currently a draft proposal in the ECMAScript 6,
the standards specification for the next version of Javascript,
for `Object.observable`.
It describes a means for all POJSOs to register listeners on property changes.
This will obviate the need for wrapper objects around models.
	
This has implications on each framework.
AngularJs will be able to continue using POJSOs for models,
without the complications mentioned above.
EmberJs will no longer need to use wrapper objects for models,
and thus it authors may choose to switch to using POJSOs instead.

That being said, it is pertinent to remember that this is presently a proposal, and we do not know if or when this will happen,
and more importantly, what the actual implementation and variation in adoption amongst the major browsers will be.

## Continued

This post has gottent a little too long, so I'll split it. In part two, we will continue  with views, controllers and templates.
