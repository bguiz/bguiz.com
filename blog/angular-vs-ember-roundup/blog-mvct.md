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

Prior to single-page applications, 
this logic was coded almost entirely on the server - thin-client, thick-server.
However, with single-page applications, web apps are thick-clients, 
and a lot of this logic has been shifted to the client, 
and the front end developer now needs to be well versed in this pattern.

Most single-page application frameworks are designed around the MVC pattern, or variations of it. 

While models, views, and controllers are at the core parts of most single-page application framework, they also include a few more core parts, templates, routing, and components. 
This post will discuss models, views, and controllers, as well as templates, because they are so closely connected to views.
Routing and components will be dscussed in a subsequent post.

## Models

- Models in AngularJs
	- What is the syntax used to express models?
	- What additional libraries do you need to use models?
	- How easy is it to get/ sync models between client and server?
- Models in EmberJs
	- What is the syntax used to express models?
	- What additional libraries do you need to use models?
	- How easy is it to get/ sync models between client and server?

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
