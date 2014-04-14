---
title: The MVC pattern in AngularJs and EmberJs
slug: angularjs-vs-emberjs-mvc-overview
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

## MVC

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

## Discussion

We have taken a very brief (and selective) look at the parts of Internet history,
which have contributed ideas toward, and eventually led to,
the rise of Javascript single-page applications;
and their relationship to the MVC pattern (or similar).

In the following posts, we will take a closer look at the MVC pattern,
and what working with it is like in AngularJs and EmberJs.
