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

As a side note, you might be tempted to simply do `<foo-component />`,
since it does not contain anything.
Do not do this - AngularJs' templating will not parse this as expected,
as self-closing or void tags do no get read.

Next, we have `transclude: true`.
This is what we should do if we want to be able to support
components which do contain DOM within their tag, for example:

		<foo-component>
			<p>Some transcluded text</p>
		</foo-component>

Inside the template *for the component*, we have

		<div>
			<div ng-transclude></div>
			<p>Some non-transcluded text</p>
		</div>

This will render DOM that looks like this:

		<foo-component>
			<p>Some transcluded text</p>
			<p>Some non-transcluded text</p>
		</foo-component>

If our component does not need to support this type of functionality,
then we can leave out `transclude` from the directive definition,
and avoid its overhead.

The last concept that we have is scope isolation.
The default behaviour in an AngularJs template,
is for the scope present on an element to be available on any of its child elements.
This is because, normally, a child scope prototypically inherits from its parent scope.
When you do not want this to happen,
you apply a directive on a child element,
and in the definition for the directive,
you tell it to use a new scope,
optionally telling it which attributes from the parent scope to copy.
In AngularJs vocabulary, this is known as scope isolation.

The most basic use of scope isolation looks like this

		App.directive('fooComponent', function() {
			return {
				scope: true,
				templateUrl: 'components/foo.html'
			};
		});

The `scope: true` tells AngularJs that this directive uses a brand new scope,
and does not have access to the `$scope` of its parent.
Since we have simply used a boolean flag,
we are simply saying, "this directive wants its scope to not inherits from its parent scope"
and since it is empty, there will not be anything for the templates to bind to.

		App.directive('fooComponent', function() {
			return {
				scope: true,
				templateUrl: 'components/foo.html'
			};
		});

Now we step things up a notch, and create a more useful isolate scope.

		App.directive('fooComponent', function() {
			return {
				scope: {
					foo: '=', //two-way binding
					bar: '@', //one-way binding
					baz: '&'  //expression binding
				},
				templateUrl: 'components/foo.html'
			};
		});

Here we have made three things available on the this directive's `$scope`.

- The `&` binds to an expression named `baz`
- The `@` binds to a property `bar`
- The `&` binds to a property `foo`

The parent scope might have many other properties,
however, if this directive tries to access them,
it will not be able to find them.
This allows us to set up a boundary of sorts,
which is great for making components,
because its inputs and outputs, and thus its interface, are clearly defined.

This is a rather simple explanation of the concepts of `restrict`, 
`transclude`, and `scope` isolation,
which are necessary to make components in AngularJs.
The follwing resources cover these in greater depth:

- [Creating components with AngularJs](http://blog.ijasoneverett.com/2013/03/creating-components-with-angular-js/)
- [Understanding Scopes - Directives](https://github.com/angular/angular.js/wiki/Understanding-Scopes#-directives)

As mentioned earlier, AngularJs does not provide a means to create web components directly.
What is does provide you with, however, are some lower level concepts,
with which web components may be created.
Combine them together using certain design patterns,
such as these ones described above, 
and you have got your own web components.

That being said, all that flexibility means that you can put these concepts together in novel ways,
and create other things than web components.
One great example of this is creating your own Domain Specific Language (DSL).
Take this creative example, where some created a [DSL to describe a choose-your-own adventure story](https://github.com/danielstern/cyo).

#### The web components spec

The [web components specification](http://www.w3.org/TR/components-intro/)
is curently [in draft status](https://www.w3.org/standards/techs/components#drafts).

Miško Hevery has written about plans to [make web components work seamlessly with AngularJs directives](https://groups.google.com/forum/#!msg/polymer-dev/4RSYaKmbtEk/uYnY3900wpIJ).
Knowing that AngularJs plans to keep aligned with standards specifications is important,
because it means that apps developed with the framework will themselves be standards compliant,
and gain all the benefits inherent in being so.

### EmberJs components

In comparing the various aspects of the two single page application frameworks so far,
in every point of comparison,
EmberJs' offering has presented a more complex option than AngularJs' the equivalent.
With components, however, the opposite is true -
Ember components are much more straight forward than what we have looked at above in AngularJs.

This is because in Ember components,
all those three decisions have been made for you.
An Ember components can be thought of as an
["element-restricted, isolate-scoped, transcluded directive"](http://docs.google.com/presentation/d/1e0z1pT9JuEh8G5DOtib6XFDHK0GUFtrZrU3IfxJynaA/present?slide=id.g177e4bd2b_0400).

#### Syntax for Ember Components

The minimum required to create a component is to create a template,
which is named according to the naming convention:
Prefixed with `components/` ,
and the name of the component must contain at least one hyphen (`-`).

		{{!-- components/the-foo --}}
		<div>
			This is a foo
		</div>

In order to display the component in a template,
use a handlebars helper with the same name as the component:

		{{the-foo}}

This is the minimum requirement, however, not very useful.
We want to be able to do three things, just like we did with the AngularJs directive.

- Use elements - strictly speaking, we do not get this, but we get something similar, with the handlebars helper above (element restricted)
- Display content from the template within the component (transclusion)
- Pass in select models or attributes into the component (scope &amp; scope isolation)

This is quite easily achieved:

		{{!-- components/the-foo --}}
		<div>
			Foo bar is: {{foo.bar}}
			{{yield}}
		</div>

&hellip; and we use the component in the template like this:

		{{#the-foo foo=model}}
			<p>This paragraph will be displayed where {{yield}} is<p>
		{{/the-foo}}
		
Extremely straightforward.

One last thing, is that in AngularJs components, 
you already have a directive,
and if you wish to add additional properties and actions,
you simply stick them in the scope..

In EmberJs components, we can get transclusion and scope isolation,
without writing any Javascript.
However, when we wish to add properties and actions,
we will need to to this define a component.

Create an object that extends `Ember.View` as a propety on the `App` object,
and it must be named the same as the template,
except capitalised, un-dasherised, and suffixed with `Component`.

		App.TheFooComponent = Ember.View.extend({
			exclaimBar: function() {
				return this.get('foo.bar')+'!!!';
			}.property('foo.bar')
		});

&hellip; and modify the component to use the custom property:

		{{!-- components/the-foo --}}
		<div>
			Foo bar is: {{exclaimBar}}
			{{yield}}
		</div>

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
