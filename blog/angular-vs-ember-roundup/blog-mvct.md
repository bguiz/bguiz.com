---
title: Models, Views, Controllers, and Templates in AngularJs and EmberJs
slug: angularjs-vs-emberjs-mvc-template
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
However the client-side javascript quite often ended up becoming a tangled mess of unstructured Javascript and jQuery when the site tried to do something complex.

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

- Models in AngularJs
	- What is the syntax used to express models?
	- What additional libraries do you need to use models?
	- How easy is it to get/ sync models between client and server?
- Models in EmberJs
	- What is the syntax used to express models?
	- What additional libraries do you need to use models?
	- How easy is it to get/ sync models between client and server?

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

### How this *might* change in ES6

There is currently a draft proposal in the ECMAScript 6,
the standards specification for the next version of Javascript,
for `Object.obseravable`.
It describes a means for all POJSOs to register listeners on property changes.
This will obviate the need for wrapper objects around models.

This has implications on each framework.
AngularJs will be able to continue using POJSOs for models,
without the complications mentioned above.
EmberJs will no longer need to use wrapper objects for models,
and thus it authors may choose to switch to using POJSOs instead.

That being said, this is a proposal, and we do not know if or when this will happen,
and more importantly, what the actual implementation and variation in adoption amongst the major browsers will be.

## Views

- Not much to say here, both frameworks defer most work to the templates
- What is the syntax used to express views?
- How do they interact with the controllers, models, and router?

## Controllers

- Controllers in AngularJs	
	- What is the syntax used to express controllers?
	- Does the framework have constructors? If not, what is in their place?
	- Support for two-way data binding, between the models and views?
	- Declartative or imperative?
- Controllers in EmberJs	
	- What is the syntax used to express controllers?
	- Does the framework have constructors? If not, what is in their place?
	- Support for two-way data binding, between the models and views?
	- Declartative or imperative?

## Templates

- Templates in AngularJs	
	- What is the syntax of the templates?
	- What additional libraries are used
	- Are the templates string based or DOM based?
	- Are the templates easy to work with for designers who are not developers?
- Templates in EmberJs	
	- What is the syntax of the templates?
	- What additional libraries are used
	- Are the templates string based or DOM based?
	- Are the templates easy to work with for designers who are not developers?

## Discussion

- Diagrams
	- MVC interaction in EmberJs
	- MVW interaction in AngularJs
- How does everything fit together in AngularJs
- How does everything fit together in EmberJs
- Pros and Cons
