## Criteria

- Learning curve
- Models
- Views
- Controllers
- Router
- Templating
- Components
- Dependency management
- Build tooling
- Opinionation
- Development tooling
- Dependencies
- Community
- Track record
- Contributions
- Maturity
- Level of abstraction/ structure
- Testability

## AngularJs

### Learning curve

Easy at the beginning, harder at the end.

Googling a little tends to yield the answers to most beginner questions. However, as apps get large, you will have a very [tricky time optimising the digest cycle][1], and this needs to be done manually.

### Models

built-in:
Uses plain old Javascript objects (POJSO) that are set on $scope - no models. Very easy to use.

Provides a directive called $http, which makes it very straight forward to update/ sync these models with the server.

Higher-level model interaction with server is made possible through the $resource directive.

### Views

Almost N/A. You can define them to do trivial things. Templates do the heavy lifting.

### Controllers

Controllers aren’t really controllers. In fact angular [calls itself a MVW framework][6] (Model-View-Whatever)

Limited in that behaviour is undefined when setting a primitive value on the $scope.

### Router

built-in:
Angular’s router runs a finite state machine.

As such, it is only able to run a single controller at a time. Rendering one view within another requires the ability to tinker with the internals of angular.

[angular-ui/ui-router][7]:
Uses directives plus tinkering with angular internals to achieve something similar to what the ember router does. Have not tried this. Is not developed by a member of the core angular team.

### Templating

built-in:
DOM-aware, supports expressions and piping. Rendered DOM is not cluttered with extra elements, but is cluttered with many extra attributes.

Very easy to bind controller properties to template, and to invoke functions on the controller.

Declarative style syntax of linking templates with directives.

### Components

Achieved via directives. Directives are too general purpose, and in order to make components from them, a deep understanding of scope isolation and transclusion is necessary. Can be made to work without such a deep understanding, however, can feel like a you are dealing with something at too low a level of abstraction.

### Dependency management

Angular uses dependency injection to figure out which modules depend on which others and include them into each other, AMD style.

### Build tooling

[generator-angular][11]:
Using yeoman we can quickly scaffold a project for angular that manages 3rd party libraries using bower, and scaffolds the angular code base. It also includes all the using Grunt tasks like watch, livereload, sass, build, and serve.

### Opinionation

Only opinionated about two things: Low level architectural decisions (which don’t matter to us as the developer), and that DOM manipulation should not be done within directives/ controllers (but does not prevent you from doing so)

### Development tooling

[batarang][16] (chrome extension):
A great debugging tool that allows one to inspect internals of angular scopes and modules

### Framework Dependencies

[angular-ui/ui-router][7] (optional)

This means that an angular app tends to be snappier to download

### Community

Large community

Developed by: Google (misko hevery)

### Track record

Being used in a large number of digital agency type, or simple web app type projects. Only enterprise app listed was Google’s DoubleClick.

See [built with AngularJs][20]

### Open Source Contributions

Open source, very popular, with a high number of contributions.
Contributions are more consistent over time.
Much lower likelihood of accepting contributions from non-core team.

See [tenxer dashboard for AngularJs][22]

### Maturity

Older and more stable.
Everything works the way it should right away.
No breaking changes to worry about, except testing transition from karam to protractor.

### Level of abstraction/ structure

Lower level of abstraction around various aspects of a SPA. Somewhere halfway in between EmberJs and BackboneJS.
As a result, there can be several different ways to do the same thing, and you do not get “forced” into making certain decisions earlier on. This can lead to greater short term productivity, because it allows more freedom of expression

### Testability

[Unit testing][24]:
Built into angular using dependency injection in combination with native mock objects.

[End-to-end testing][25]:
Currently using testacular/ karma as the test runner, but Google has directed the community away from this, to protractor, a new project. Existing tooling, e.g. generator-angular do not work out-of-the-box due to this.

Overall, testing in Angular has been a consideration since the inception of the framework, and it shows.

## EmberJs

### Learning curve

Extremely hard at the beginning, easier towards the end.

Absolutely cannot get by using Google and Stackoverflow at the beginning. Had to follow many tutorials, and get a couple of paid courses ([peepcode][2] and [codeschool][3]) before able to become productive. Once over this initial hurdle, progress was much quicker

### Models

built-in ([ember-data][4]):
Wrapper objects around POJSOs. Need to use model.get and model.set, so syntax is slightly more complex than angular’s.

Ember data is is still in beta, and thus not production ready, must use a drop in replacement.

[ember-model][5]:
Maintained by ebryn, a core Ember contributor. Thus syntax is very tight with that of ember-data

Allows for a way to define bring your own AJAX (BYO$A), on a per model class basis, thus equivalent to angular.

### Views

Almost N/A, as with AngularJs. You can define them to do trivial things. Templates do the heavy lifting.

### Controllers

Supports traditional MVC controllers.

Clean segregation of properties from actions.

Declarative syntax for defining dependencies between one property and another, using Function.prototype.property and Ember.computed.*

Routes are responsible for passing models into the controllers, and only one of each controller needs to be instantiated. Provides a clean separation of concerns.

### Router

built-in:
Ember’s router runs a hierarchical state machine under the hood.

As such, it know how to keep multiple controllers (that belong to the same vertical section of a hierarchical state) running at the same time. This makes rendering one view within the context of another view extremely easy to do, using {{outlet}} in the templates.


### Templating

built-in (handlebars):
String-based templating, not DOM-aware, no expressions or piping, rendered DOM cluttered with <script> tags

Easy to bind controller properties to template, and to invoke functions on the controller. Syntax is more restrictive than angular’s.

HTMLbars:
Compatible with Handlebars, looks like migration should not be tricky.
Solves all the limitations of Handlebars: DOM-aware, has expressions and piping, and rendered DOM is clutter-free
[example by wycats][8]
[slides by ebryn][9]

### Components

["An Ember.Component is an element-restricted, isolate-scoped, transcluded directive"][10]

This is one part where the learning curve for Ember is actually much easier than that of Angular.

### Dependency management

Ember requires you to create a global ‘App’ object, and put everything in that, using it as a namespace.

### Build tooling

[generator-ember][12]:
Using yeoman we can quickly scaffold a project for angular that manages 3rd party libraries using bower, and scaffolds the ember code base. It also includes all the using Grunt tasks like watch, livereload, sass, build, and serve.

[ember-app-kit][13]:
The main benefit of this is that the code is structured by modules rather than type; and that it supports dependency management using ES6 modules.
Maintained by stefanpenner, a core member of the Ember team.

[ember-cli][14]:
A replacement for both the yeoman generator and ember-app-kit combines, that aims to be as powerful as the rails gem at scaffolding. Is likely to swap from using Grunt to [Broccoli][15] as the underlying build tool, and may require learning this new system.
Maintained by stefanpenner as well.

### Opinionation

Extremely opinionated on almost every aspect of SPAs:
- The route must drive the current controller(s)
- The naming conventions of all of the following: models, views, controllers, partials, templates, routes, router urls, components. This can be frustrating, when you cannot figure out why some code is not not kicking in. Ember Inspector is invaluable in discovering these. 

### Development tooling

[ember inspector][17] (chrome extension):
A great debugging tool that allows one to inspect internals of ember models and controllers

### Framework Dependencies

[jQuery][18]
[handlebars][19]
[ember-data][4] (optional)

This means that an ember app will take a longer time to download

### Community

Small community

Developed by: Tilde (wycats)

### Track record

Being used in a small number of  complex enterprise web apps. Notably: Codeschool, ThoughtBot, UrbanSpoon, Crowdflower, Discourse, Zendesk, Square, Yahoo, LivingSocial, Groupon

See [who’s using EmberJs][21]

### Open Source Contributions

Open source, very popular, with a high number of contributions.
Contributions are more variable over time.
Much higher likelihood of accepting contributions from non-core team.

See [tenxer dashboard for EmberJs][23]

### Maturity

Newer and less stable. Only come out of beta recently (in December 2013).
Not everything works right away, and need to spend some time figuring it out.
Need to keep up with breaking changes.
Much less things not working or breaking changes to be expected since coming out of beta. Development team has promised no more breaking changes to ember-core. Currently, there is a possibility of ember-data, htmlbars, and ember-cli introducing breaking changes

### Level of abstraction/ structure

Higher level of abstraction around various aspects of a SPA.
As a result, there is often only one way to do a certain thing, and you have to make certain decisions earlier on. This can lead to lower short term productivity, because of the lack of freedom of expression, and greater long term productivity by reducing the need to refactor down the road.

### Testability

Unit testing:
Karma & QUnit

[End-to-end testing][26]:
Karma & QUnit

Overall, support for testing has not been there from the beginning (ember-testing only released in with RC3, which was in April 2013), and it appears to be tacked on as an afterthought. Nonetheless, now that testing is supported by the core framework, it is possible to do a full set of tests following along guides by [toranb][27] or [pixelhandler][28].

[1]: http://briantford.com/blog/huuuuuge-angular-apps.html
[2]: http://peepcode.com/products/emberjs
[3]: http://www.codeschool.com/courses/warming-up-with-emberjs
[4]: http://github.com/emberjs/data
[5]: http://github.com/ebryn/ember-model
[6]: http://plus.google.com/+AngularJS/posts/aZNVhj355G2
[7]: http://github.com/angular-ui/ui-router
[8]: http://gist.github.com/wycats/8116673 (wycats on how HTMLbars improves on handlebars)
[9]: http://talks.erikbryn.com/htmlbars/#/6 (ebryn - HTMLbars is a DOM-aware handlebars)
[10]: http://docs.google.com/presentation/d/1e0z1pT9JuEh8G5DOtib6XFDHK0GUFtrZrU3IfxJynaA/present?slide=id.g177e4bd2b_0400 (machty on Ember.Component compared to Angular directive)
[11]: http://github.com/yeoman/generator-angular
[12]: http://github.com/yeoman/generator-ember
[13]: http://github.com/stefanpenner/ember-app-kit‎
[14]: http://github.com/stefanpenner/ember-cli‎
[15]: http://www.solitr.com/blog/2014/02/broccoli-first-release/‎
[16]: http://github.com/angular/angularjs-batarang
[17]: http://github.com/tildeio/ember-extension
[18]: http://jquery.com/
[19]: http://handlebarsjs.com/
[20]: http://builtwith.angularjs.org/ (who uses AngularJs)
[21]: http://emberjs.com/ember-users/ (who uses EmberJs)
[22]: http://www.tenxer.com/opensource/angularjs/ (open source contribution dashboard for angular)
[23]: http://www.tenxer.com/opensource/emberjs/ (open source contribution dashboard for angular)
[24]: http://docs.angularjs.org/guide/dev_guide.unit-testing
[25]: http://docs.angularjs.org/guide/dev_guide.e2e-testing
[26]: http://emberjs.com/guides/testing/integration/
[27]: http://www.toranbillups.com/blog/archive/2013/07/21/Integration-testing-your-emberjs-app-with-QUnit-and-Karma/
[28]: http://pixelhandler.com/blog/2013/08/15/testing-an-ember-application-integration-and-unit-tests/
[29]: http://blog.bguiz.com/post/60397801810/digest-cycles-in-single-page-apps (on tresholds for dirty checking in digest cycles)
[30]: http://stackoverflow.com/a/9693933/194982 (misko hevery explainign the performance of digest cycles)
[31]: http://stackoverflow.com/a/18381836/194982 (reply to misko’s answer, stating how easy it is to hit these limits)
[32]: http://people.groupon.com/blog/2012/groupon-engineer-spotlight-peter-bergstrom/ (groupon scheduling engineer using ember)
[33]: http://speakerdeck.com/schmonference/ember-dot-js-in-the-wild (groupon scheduling engineer giving a talk on ember)
[34]: http://github.com/TryGhost/Ghost/issues/2144  http://dev.ghost.org/hello-ember/ (ghost discussion thread & decision on which framework to choose)
[35]: http://errplane.com/blog/why-angularjs-beat-out-emberjs-in-our-stack (errplane post on why they chose angular)
[36]: http://emberjs.com/guides/models/the-rest-adapter/ (shows how opinionated the ember models are)

