---
title: Models, Views, Controllers, and Templates in AngularJs and EmberJs
slug: angularjs-vs-emberjs-mvc-template-part-2
author: bguiz
date: 2014-03-09
template: article.jade
tags:
- angularjs
- emberjs
- single-page-apps
- comparison
- MVC
---

## MVC + Templates

Models, views, and controllers are one of the key components of a single-page application framework.

![Model-View-Controller interactions](http://upload.wikimedia.org/wikipedia/commons/thumb/a/a0/MVC-Process.svg/200px-MVC-Process.svg.png)

We discussed the MVC pattern, the history of single-page apps,
and also compared Angular models and Ember models in detail, in the previous post.
This post continues from that with a comparison of views, controllers and templates in Angular and Ember.

## Views

Views are classes which control the behaviour of the presentation aspects of one or more models.
Both Angular templates, and Handlebars templates (Ember),
are so powerful that views do not do much.

In fact most developers can develop an entire app without declaring or modifying any views,
and only use templates instead.
They are only *really* needed when there is a need to handle complex user events.

However, for the sake of completeness,
we shall just take a brief look at the syntax for views in each framework.

### AngularJs views

You never see the Javascript for the views.
Instead, you invoke the `ngView ` directive in the template.
That is all there is to it.

### EmberJs views

		var myFooView = Ember.View.create({
			templateName: 'foo-view'
		});
		//do things with the view

Usually, however, you can define user interaction in controllers,
and that is more common than using views.

A pretty brief section here;
we will be taking a look at where the majority of the presentation logic occurs -
in templates - later.

## Templates


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
	- Declarative or imperative?

## Discussion

- Diagrams
	- MVC interaction in EmberJs
	- MVW interaction in AngularJs
- How does everything fit together in AngularJs
- How does everything fit together in EmberJs
- Pros and Cons

