---
title: Controllers in AngularJs and EmberJs
slug: angularjs-vs-emberjs-mvc-controllers
author: bguiz
date: 2014-03-09
template: article.jade
tags:
- angularjs
- emberjs
- single-page-apps
- comparison
- MVC
- controllers
---

## MVC + Templates

Models, views, and controllers are one of the key components of a single-page application framework.

![Model-View-Controller interactions](http://upload.wikimedia.org/wikipedia/commons/thumb/a/a0/MVC-Process.svg/200px-MVC-Process.svg.png)

We discussed the MVC pattern, the history of single-page apps,
and also compared Angular models and Ember models in detail, in the previous post.
This post continues from that with a comparison of views, controllers and templates in Angular and Ember.

## Controllers

Controllers are the glue that binds models to the views/ templates.
They are responsible for manipulating the models,
and also for responding to user interactions on the views.

Two-way binding, present in both AngularJs and EmberJs,
take care of most of the grunt work in these interactions,
by specifying that a particular attribute on a model
is "bound" to this particular part of the DOM within a view.
However, only direct mappings can work like this;
and controllers are necessary to define more complex interactions between the models and the views.

### AngularJs controllers

Controllers in Angular are not exactly controllers.
In fact Angular does not call itself an MVC framework,
[it calls itself an MVW (model-view-whatever) framework](http://plus.google.com/+AngularJS/posts/aZNVhj355G2) instead.
(I would argue that the `$scope` object in any angular controller module is the actual controller)
That being said, while they are not controllers from an academic point of view,
from a practical point of view, they most certainly are.

The syntax of defining a controller in an AngularJs app is like this:

        angular.module('app', []).controller('FooCtrl', function($scope) {
            $scope.someProperty = 'some initial value';
            $scope.someAction = function() {
                $scope.someProperty += '!';
            };
        });

The `$scope` object is prototypically inherited from the `$scope` object of the parent object,
which in this case is the main app.
It is made available to this controller through AngularJs' dependency injection framework.
This in itself is a fascinating topic that warrants a discussion of its own,
as it is a *beautiful* piece of software engineering architecture,
and understanding it lies at the core of understanding AngularJs.
Unfortunately, a discussion on this is not one of the criteria which we are comparing these frameworks upon.

## AngularJs two-way binding

As mentioned earlier, two-way binding comes out of the box with AngularJs.

        <div ng-controller="FooCtrl">
            <button ng-click="someAction()">Exclaim harder!</button>
            <p>Some property is {{someProperty}}</p>
            <input type="text" value="{{someProperty}}">
        </div>

In the above template, when AngularJs parses the DOM,
the `ng-controller` attribute is detected to have a name matching with the `ngController`directive,
which then knows to find the controller we have defined above.

The `ng-click` directive, as well as the `{{someProperty}}` bound directive,
are both within this "controlled" `<div>` and thus AngularJs know to look for `someAction` and `someAction` on the `$scope` belonging to the `FooCtrl` directive.

The `ng-click` is fairly self-explanatory -
this directive evaluates the expression within the attribute value when the element is clicked.
In this case, `$scope.someAction` gets invoked.

The `{{someProperty}}` is slightly more complex,
because there is a bit of magic going on behind the scenes -
two-way binding magic!

#### AngularJs two-way binding

When the user clicks on the `<button>`, the value of `someProperty` gets changed by the controller.
Angular, having parsed the template prior,
knows that there are two parts of the DOM controlled by `FooCtrl` that are bound to this property;
so it goes and updates them, and the view gets updated due to a change detected in the model.
Now the user does something else - type into the text `<input>`.
Angular knows that this property is within the `FooCtrl` controller, and updates the value of of the property on its `$scope`.
This is an update of the model due to a change detected in the view.
(subsequently, that model change triggers another update in the view).

This is AngularJs' two-way binding in action.
As apps get more complex,
it becomes more important to know how two-way binding works, so as not to break it.
The canonical use case is when you have a property on `$scope`,
but that property is too complex to be included in the dirty checking performed in each digest cycle.
You need to manually notify AngularJs of when your property has been updated,
so that the two-way binding between the model of that property,
and its representation within its view still is bound in both directions.
This is accomplished through `$watch` and `$apply`,
and using them correctly is contingent upon understanding how two-way binding works.

#### AngularJs controllers: Imperative vs declarative

When looking at the syntax, one thing worth analysing is where it sits
on the imperative to declarative spectrum.

When you declare an attribute on the `$scope` of the of the controller,
and then use directives or markup to bind to them,
two-way binding occurs.
In this situation, you merely declare that this property is linked to that part of a template,
and this two-way binding is declarative.

When you want to do something a little more complicated,
where two-way binding is not sufficient, however,
declarative syntax is no longer possible.
For example, if we want to express a relationship between one property and another on the same controller,
or on multiple others.
To accomplish this, we need to create a function that updates the relevant properties.
We then need to set a call `$scope.$watch` to observe for changes on the input property,
and provide it with a callback function that would then update the output property.
In this situation, you do not declare the relationship between one property and another,
instead you are imperatively describing the computation.

As a footnote, it is worth pointing out that instead of having a property on a scope,
we could just have a function on the scope that gets called and simply returns the computed value.
This, however, is not ideal for anything more than the simplest of computed properties,
because it will be called upon every digest cycle,
not just each time it needs tobe re-computed.

### EmberJs controllers

EmberJs supports controllers that conform to the MVC pattern.

The syntax of defining a controller in an EmberJs app is like this:

        App.FooController = Ember.Controller.extend({
            someProperty: 'some initial value',

            actions: {
                someAction: function() {
                    this.set('someProperty', this.get('someProperty') + '!');
                }
            }
        });

As we can see in the code above,
there is a clean separation between properties and actions, with all actions grouped within the `actions` object in the controller.

It is worth noting here that EmberJs controllers distinguish between properties on the controllers,
and models used by the same controllers.
Models are created and passed into a controllers using `Ember.Route` objects,
which we will cover when we cover routing in EmberJs.
This is because there is only one instance of any controller instantiated,
and thus its state does *not* get reset each time.
This is an important point to note,
and it can be a source of confusion at first.
To compound this confusion,
`Ember.Controller` is typically not used,
instead we use `Ember.ObjectController` and `Ember.ArayController`,
depending on whether the model is a single object or a collection of objects,
and these types of controllers proxy the properties of their models.

Another thing worth noting is that we had to name this controller `"FooController"`.
In AngularJs, we were free to name our controller anything we wanted -
`"FooBarCtrl"` would have worked just as well as `"FooCtrl"`.
In EmberJs, however, we must name our controllers (and most other objects actually), according to their naming convention.
This is a common gotcha, and sometimes you will be left scratching your head
as to why your code does not appear to have any effect.
To be fair, you can override this behaviour and specify your own naming strategy,
but most EmberJs app develpers will simply just conform to the prescribed naming conventions.

## EmberJs two-way binding

        <div>
            <button {{action 'someAction'}}>Exclaim harder!</button>
            <p>Some property is {{someProperty}}</p>
            {{input type="text" value=someProperty}}
        </div>

Two-way binding in EmberJs works in a very similar fashion to the way it works in EmberJs,
so we will only cover the differences.

Two-way bindings in markup work as expected, e.g. `{{someProperty}}`.
However, they do not work very well with element attributes.
In this case, we see that instead of just binding to the `value` attribute of input element,
we need to invoke a special Handlebars helper linked to a built in EmberJs view called input.
This is actually the recommended way to do this.
It gets more complicated when there is no bilt in helper or view.
In this case you will need to use `{{bind-attr}}`.
We have covered some of this in the section about EmberJs templates.

The implications of this is that binding is much more complex in EmberJs than it is in AngularJs,
simply because there is more syntax to learn.

#### EmberJs controllers: Imperative vs declarative

Two-way bound properties in EmberJs are declarative, just as they are in AngularJs.

Properties in EmberJs are declarative too:

        App.FoosController = Ember.ArrayController.extend({
            filterText: '',

            filteredModel: function() {
                var foos = this.get('model'), filterText = this.get('filterText');
                //compute a filtered version of the foos array, and return it
            }.property('filterText'),

            actions: {
                someAction: function() {
                    this.set('someProperty', this.get('someProperty') + '!');
                }
            }
        });

Here we define a property that is dependant on another property,
with a fairly complex computation (filtering an array's contents).
This is possible to do because we know that we know that EmberJs will
only perform the recomputation only when a property changes.
This is possible because EmberJs supports the concept of computed properties,
that is what the syntax of calling `.property()` on the function does -
the function is now marked as a property,
and thus can be referred to as a property int he template,
which is a great example of [the uniform access principle](http://en.wikipedia.org/wiki/Uniform_access_principle).
If this call to property `.property()` contains any arguments,
EmberJs knows that that property should be recomputed whenever those other properties are changed,
and thus is a computed property.

This way of defining computed properties is a lot more succinct,
than using `$watch` on AngularJs controller scope;
and is much more elegant, and easier to understand.

## Discussion

AngularJs provides an extremely elegant solution of defining modules and linking them to each other,
through its dependency injection framework.
The closest that EmberJs comes to this is its naming strategy.
Models, views, controllers, and templates find each other using this naming strategy,
in a manner that is not easy to change by developers.

Both AngularJs and EmberJs support two-way binding out of the box between models and views.
No special logic has to be defined within controller in order to make this happen.
The limitations of EmberJs's string based templating flow on to two-way binding,
and impact it too.

AngularJs controllers do not provide a nice means to define dependencies
between their properties.
This is perfectly fine for simple properties as we can use a function in its place.
However, when complex properties are involved,
this is not ideal, as it will get recomputed in every digest cycle.
EmberJs provides a much more elegant solution in this area,
by providing a means to define computed properties.

## MVC

We have now covered the topic of models, views (including templates), and controllers,
and have described how they work in both AngularJs and EmberJs,
along with templates, where both frameworks defer most of their view logic to.

In summary, both AngularJs and EmberJs are single-page application frameworks that have great support for the MVC pattern,
and provide an excellent infrastructure to build these apps with.

In various aspects, there are some pros and cons,
however, these are not deal breakers.
Many of them boil down to a matter of personal taste
in deciding which characteristics are a must,
and which ones are forgo-able.
