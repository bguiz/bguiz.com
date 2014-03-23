---
title: Routers and Components in AngularJs and EmberJs
slug: angularjs-vs-emberjs-component
author: bguiz
date: 2014-03-20
template: article.jade
tags:
- angularjs
- emberjs
- single-page-apps
- comparison
- component
---

## Components

- define componnents
- web components spec

### AngularJs components

AngularJs does not provide a direct construct for custom components.
However, through the use of directives,
it is posible to create your own custom components.

Directives in AngularJs can be used to define the way in which the Javascript application code
interacts with the DOM.
We have already seen some of these in action,
with the `ngModel`, `ngController` and `ngClick` directives.
The templates know where to apply the Javascript code for these directives,
where DOM elements contain the dasherised versions of these directives' names
as attributes `ng-model`, `ng-controller`, and `ng-click`.

However, this is just the very basic way in which we can use directives.
All the the directives that AngularJs ships with -
the `ng*` directives -
are applied in this manner.
To build components, however,
we need to use more advanced features of directives.

#### `restrict`, `transclude`, and isolate `scope`

Let us take the most basic use case:

		App.directive('fooComponent', function() {
			return {
				templateUrl: 'components/foo.html'
			};
		});

In order to use this "component" in a template we would simply use:

		<div foo-component>
		</div>

All this does is take the template at `components/foo.html`,
and render it within this `div`.
This does not look very much like a component,
and lacks a few features that we would expect of one.

Now let us make it a little more useful by adding into the mix
`restrict`, `transclude`, and an isolate `scope`:

		App.directive('fooComponent', function() {
			return {
				restrict: 'E',
				transclude: true,
				scope: {
					bar: '='
				},
				templateUrl: 'components/foo.html'
			};
		});

In order to use this component within a temaplate we use:

		<foo-component>
		</foo-component>

Now that is looking a little more like it should -
we are no longer "tagging" a `div` with the name of a directive,
but instead have an element which is the name of the directive.
This was accomplished using `restrict: 'E'`,
which tells angular that this directive should be applied to elements,
instead of attributes, which we saw previously.


[creating components with AngularJs](http://blog.ijasoneverett.com/2013/03/creating-components-with-angular-js/)

[choose your own adventure](https://github.com/danielstern/cyo)

[Miško Hevery on plans tomake web compnents work seamlessly with AngularJs directives](https://groups.google.com/forum/#!msg/polymer-dev/4RSYaKmbtEk/uYnY3900wpIJ)

[Web components draft spec](http://www.w3.org/TR/components-intro/)

- building a DSL using directives
- no special syntax for components
- just use directives and isolate their scope
- syntax of a scope isolated, element restricted directive
- relation to web components spec

### EmberJs components

[Using EmberJs components](http://emberjs.com/guides/components/passing-properties-to-a-component/)

[Yehuda Katz on plans for Web Components in EmberJs](https://gist.github.com/wycats/9144666b0c606d1838be)

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
