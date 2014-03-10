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

As mentioned earlier, templates are where most of the presentation logic occurs.
AngularJs and EmberJs have taken very different approaches for their templates,
but they both accomplish a fairly similar task.

** AngularJs templates **

Syntax:

		<body ng-controller="FooController">
			<input ng-model="foo" value="bar">
			<button ng-click="changeFoo()">{{buttonText}}</button>
		</body>

Angular has no templating language per se - it simply inspects the rendered DOM,
which has been decorated with certain attributes that are bound to various angular modules.
This is done in one of two ways:

- Directives
	- Elements or attributes that link to modules known to the agnular app
	- Angular comes with several built in directives, and you can recognise them as `ng-*` attributes in the DOM
- Markup
	- Any part of the markup, where text appears between doubly curly braces `{{like this}}`
	- Angular evaluates the expression within the braces, and substitutes for the output value

Angular allows an extremely expressive syntax to be added to the templates,
including Javascript expressions, and filters using a UNIX-style pipe (`|`) syntax.

** EmberJs templates **
Syntax:

		<input {{bind-attr value='model.foo'}}>
		<button {{action 'changeText'}}>{{model.buttonText}}</button>

Ember uses a templating langvuage called Handlebars.
It's approach is fundamentally different form that of Angular's,
as it uses string-based templating.
This means that it parses the entire template before-hand,
and generates a function as an output.
That function is then subsequently used to generate the DOM.

This approach has pros and cons.

- Pros
	- Since templates are pre-compiled they render quickly
- Cons
	- Requires an extra compilation step
	- As quick as precompiled templates are, they cannot be faster than DOM that is already there
	- For two way bindings to work, extra nodes need to be inserted into the DOM (metamorph tags)

All of these, in my opinion, are pretty minor, except for the one about two-way bindings.
Littering the DOM with numerous `<script>` metamorph tags really makes the DOM ugly to inspect,
and makes one wonder, surely, there must be a better way to do this!
The other implication of this is that two way binding of attributes on DOM nodes
is simply not possible using the same syntax,
and a special alternate syntax (`{{bind-attr}}`) is required to accomplish this.
All this makes it feel more like something tacked together, rather than an elegant solution.

The syntax allowed within the templates is very restrictive as well.
We can only output properties verbatim.
If any processing or formatting is required,
the way to do this is to do it in the controller, and create a Handlebars helper, respectively.

### Front-end designers

Eventually, the front-end Javascript developer will need to let the front-end
design/ UI/ UX developer modify the templates.
Most times, these are not the same people,
and possess different skill sets.
Templates, however, is where some overlap occurs.

How easily understood are Angular and Ember templates,
by someone who is not familiar with the Javascript for that web app?

In both cases, it turns out, to be pretty easy,
as they are straight forward, and intuitive to understand.

However Angular developers may need to be cautious about how complex the expressions
that they put into the templates are.
In Handlebars, this is not a concern, because developers are forced to extract
all logic into Handlebars helpers.

### Additional libraries

AngularJs has got some solid templating going for it,
and EmberJs' offering has a few large drawbacks.
Thankfully there is a new library, [HTMLbars](https://github.com/tildeio/htmlbars), that tackles these problems.
It is DOM-aware, has expressions and piping, and renders a clutter-free DOM.
[Example by wycats][8]
[Slides by ebryn][9]

Unfortunately, [it is still in the works](http://discuss.emberjs.com/t/when-will-htmlbars-be-ready/3155/20).

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

