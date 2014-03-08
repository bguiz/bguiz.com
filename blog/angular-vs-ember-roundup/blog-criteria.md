---
title: Angular vs. Ember - Criteria
slug: criteria-angularjs-vs-emberjs
author: bguiz
date: 2014-03-04
template: article.jade
tags:
- angularjs
- emberjs
- single-page-apps
- comparison
---

I would like to kick off this series of posts where we compare AngularJs to EmberJs by stating what exactly we will be comparing.

The space that these two operate in - Javascript frameworks used to develop single-page applications - is a rather complex one.
Not only is it highly complex because of the number of different issues, concepts, and even philosophies, that these thigs touch;
it is also a moving target, because they are a fairly new new thing, and still going through the initial stages of rapid development.

Thus the only certainty here is that there is no definitive answer.
We shall try, nonetheless.

## Why criteria are necessary

Without facts, opinions will have their day in the park.
We want to make this as non-subjective as possible.

Also, it helps you the reader, know if this series of posts is going to be worth your time reading.
If - and I know this is a silly example - whether the chipmunk or the shield with the "A" is the better logo is very important to you, and you want to choose your framework based on that;
then these posts are not for you, as you do not see this being listed as one of the criteria below.

That being said, if I have missed something out below, and you think it would benefit the discussion, [let me know](https://twitter.com/bguiz).

## What the criteria are

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

There we have it, all the criteria that this series of posts will cover!

## A note of fairness and polarisation

Which one is better at what?

One framework is is going to better than the other on some of the criteria, and the other way around on the others.

It is worth pointing out here that this a fairly polarised topic, 
with the communities of developers using each of these frameworks, 
firmly believing that their framework of choice is the best thing since sliced bread 
(or at least **way better** than the other).

Thus I feel that it is necessary to do a little bit of a disclaimer here, 
and state that I belong to neither camp.
I have worked with jQuery mobile, BB10 webworks, and BackboneJs before;
and prior to this I have been keeping abreast with both AngularJs and EmberJs,
but have not developed anything serious - nothing more complex than [a Todo app](http://todomvc.com) - before.

tl;dr = I am going to be fair and unbiased.

## Let&apos;s begin!

... below are the questions that we will be exploring for each of the criteria.

*Learning curve*

- How fast can you get going and get productive with the framework?
- Can you get by with Google and Stackoverflow?
- Do you need to take a course to learn it?
- Does this change when the app gets more complex?

*Models*

- What is the syntax used to express models?
- What additional libraries do you need to use models?
- How easy is it to get/ sync models between client and server?

*Views*

- What is the syntax used to express views?
- How do they interact with the controllers, models, and router?

*Controllers*

- What is the syntax used to express controllers?
- Does the framework have constructors? If not, what is in their place?
- Support for two-way data binding, between the models and views?
- Declartative or imperative?

*Router*

- What is the syntax used to express a router?
- Does the router support a flat or hierarchical series of routes?
- What additional libraries do you need to use the router?

*Templating*

- What is the syntax of the templates?
- What additional libraries are used
- Are the templates string based or DOM based?
- Are the templates easy to work with for designers who are not developers?

*Components*

- What is the syntax used to express a component?
- What level of abstraction do these components sit at?
- How well do these components align with the current draft sepcification for web components?

*Dependency management*

- What is the syntax or convention used to express that one module depends upon another?
- Does the framework encourage modular development to begin with?
- How robust is this means to managing dependencies between modules?

*Build tooling*

- What build tools exist to build an app using each framework?
- What limitations do the current build tools have, and what are they good at?
- What future build tools are in the pipeline?

*Opinionation*

- How opinionated is the framework with regards to how to develop an app?
- How does this opinionation benefit and detract from the framework?

*Development tooling*

- What tools exist to allow easy debugging of the aplication?

*Dependencies*

- What external libraries does each framework depend upon?

*Community*

- Who develops the framework, and who backs it?
- What is the size of active users of the framework?

*Track record*

- Who currently has apps in production built using each framework?
- What is the profile of apps developed using each framework?

*Contributions*

- What is the volume and consistency of the open source contributions to each framework?

*Maturity*

- How long has each framework been around?
- How stable is each framework?

*Level of abstraction/ structure*

- What levels of abstraction around the concept of single-page application does each framework provide?
- What implications do these levels of abstraction have on the development process?

*Testability*

- How can one go about unit testing an app built using each framework?
- How can one go about end-to-end testing an app built using each framework?
- How testable are apps built using each framework?
- What is the level of importance accorded to testability by the authors of each framework?
