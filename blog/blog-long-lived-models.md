<!-- Long-lived Models in Single Page Apps -->
<!-- javascript, memory management, memory leak, MVC, single page app, backbonejs, backbone relational, identity map -->

In single page applications (SPAs), when the users navigates from one "page" to another, what the browser really does is stay to on the same page, and use Javascript to re-render most of the DOM. No further HTTP requests are made by the browser to obtain a new HTML page, instead Javascript (usually  the SPA framework) loads more data in the form of JSON, and more Javascript (usually the templating engine or the SPA framework) re-renders this as new DOM, replacing the old DOM. More Javascript - the SPA framework's routing component - will also update the URL, using hash fragments, or the history API.

All this used to be done purely using the browser, and it would load a new HTML page, with a new HTTP request and response per navigation. Why use Javascript to do all this instead; after all, is it not more work?

- No "flash" in between page navigations
- Reduce bandwidth of HTTP protocol overhead
- Most importantly: Not all of the DOM changes between each "page", so we only need to update the parts that need to change

Yes, doing so is indeed more work - but most of it is cookie cutter stuff, and is taken care of by your SPA framework of choice. However, one area - managing model state - is not as simple to do. Here we shall take a look at the decisions involved in managing long-lived model state; that is, ways to maintain model state when navigating between pages, and keeping them in sync with changes on the server.

## Impact on Models

Most SPA frameworks are MV&lowast; frameworks. I have previously mentioned them in my post on [Accessors vs Dirty Checking in Javascript Frameworks](http://bguiz.com/post/57373805814/accessors-vs-dirty-checking-in-javascript-frameworks) (check out that section if you have yet to).

One thing that all MV&lowast; SPA frameworks have in common is that they all need use Models to represent data. Managing the state of these models is one of the problems which MV&lowast; SPA frameworks have to solve. Implementations of how this is done vary significantly, and they all generally do a good job of it. One area, however, that I have found lacking amongst them, in managing model state, is that they generally do not provide a mechanism to manage long-lived model state baked into the framework - and one needs to roll their own mechanism to accomplish this.

This post will explore this in greater depth; including some strategies used to keep model state on the client in sync with the data backing that model on the server; and also discuss the additional considerations that arise from maintaining this state across page navigation.

## Two Decisions

When are models fetched?

- fetch model once per navigation
- fetch model on first navigation only

When are subscription to changes on models reset?

- subscriptions reset per navigation
- subscriptions on first navigation only

Ultimately these decisions should be made upon expectations that users have towards the user interface, and user expectations of how it should behave. These expectations have changed somewhat in the domain of web applications.

### The lead up to present day SPAs

When the models views and controllers exist purely server side, as they did in traditional web sites, all the rendering was done server side, and both of these decisions were made for you. Essentially you didn't have subscriptions, so the models displayed on the page were only current at the point of time when the page was loaded. Since all the rendering was done server side, the client did not have to care about when the models were fetched either - one could assume that it was done upon each page navigation.

Enter Web 2.0, when the phrase "AJAX" was as hot a buzzword as "cloud" is presently. Using client side Javascript, one could poll the server to see if any data should be updated, and if so, the relevant section of the DOM would be re-rendered.  

One would now be using techniques such as long polling - no web sockets yet - to ask the server to notify the client when there had been changes to a model on the server. Whenever the server "pushes" such an incremental change to the client, the client updates its view of that model accordingly. These were the precursors to todays SPA frameworks.

At this stage one still could put off making either of these decisions. Since each navigation completely refreshes the page, model are still re-fetched, and subscriptions would have to be reset upon each navigation anyway.

Presently, we have single page applications, and as the name suggests, the entire app lives in a single page. Each time we navigate from one page to another, one has the freedom to decide whether to re-fetch the models from the server. One also has the freedom to decide whether to reset the subscriptions to changes on these models.

Let us represent the permutations of these decisions using a four-quadrant diagram:

<pre><code>
fetch model once per navigation        |      fetch model once per navigation
subscriptions reset per navigation     |      subscriptions on first navigation only
                                     Q1|Q2
-----------------------------------------------------------------------------------
                                     Q3|Q4
fetch model on first navigation only   |      fetch model on first navigation only
subscriptions reset per navigation     |      subscriptions on first navigation only
</code></pre>

From this it is evident that once we start using a SPA framework, we need to start thinking hard about the implications of long lived model state.

Inherent in this is an overarching theme of considering the memory footprint that each approach would require, and ways in which this can be managed. However in light of keeping this post focused, these will be briefly outlined in the footnotes<sup>1</sup>, and perhaps will be discussed in greater detail in a follow up post.

## Techniques

### The Ol' Fashioned

With reference to the diagram above, `Quadrant 1` is the easiest to implement. In fact doing this would be effectively no additional effort required above what a traditional web site or a *Web 2.0* site would do. Upon each page navigation, always request the model displayed by the page again, even if that model is currently in memory. Also, upon each page navigation, always reset the subscriptions to changes in the models. This will ensure that the models displayed on each page are always up to date.

When should this be done? The first, or stop-gap fix, when considering adding long-lived model state to a SPA for the first time. Chances are, that if this is the case, your app will display model state correctly most of the time, but there will be a few edge cases where they are not. Thus removing these edge cases will be the first port of call.

Indeed, discovering that this was occurring in an app I was working on was what prompted me to write this post.

This often happens unintentionally - as was the case for me - when one uses frameworks or libraries that do things behind the scenes that one would not explicitly consider. In my case, I was using BackboneJS with [Backbone Relational](http://backbonerelational.org). When these are used together, BackboneJS makes a REST-ful HTTP request (a `GET`) to the URL as defined by the `url` property of the `Backbone.Model`. Backbone Relational sits on top of this, and intercepts this request, if the model is a `Backbone.RelationalModel`, as was the case in all of our models. One of the things that Backbone relational does is provide an [identity map](http://martinfowler.com/eaaCatalog/identityMap.html). In a nutshell, it uses an ID-to-object hash to cache instances of subclasses of `Backbone.RelationalModel`. If a Model with a particular ID had been previously fetched, the cached instance would simply be re-used, rather than doing another `HTTP GET` for it.

While this is exactly what you want to happen in the default case, when managing long-lived model state, it is imperative to consider the implications when combined with the subscription strategy. Subscriptions here refer to the client, the single page application, listening to (subscribing to), changes in the state of models on the server. 

This is best illustrated with a timeline:

- Fresh page load on home page
- No instances of `Food` loaded
- User navigates to `#/food/123`
- The page subscribes to updates on `Food` with an ID of `123`
- This page has a view which needs to display a model of 
- The Backbone Relational identity map (cache) is queried for `Food` with ID of `123`. No results are returned, so a request is is made: `HTTP GET /api/v1/food/123`
- A JSON object is returned: `{"id":123,"name":"Sandwich"}`. Backbone relational stores this is its identity map (cache)
- The page renders `Sandwich` somewhere in the view
- The user navigates to `#/clothes/456`
- The page resets its subscriptions, including clearing its subscriptions to updates on `Food` with an ID of `123`
- While the user is looking at this clothes page, an external events causes the data backing the model `Food` with an ID of `123` to be updated. Its `name` is now `Chicken Sandwich`
- Because the client is no longer subscribed to updates for this model, this delta does not get pushed to it
- The user presses the back button, and navigating back to `#/food/123`
- The page subscribes to updates on `Food` with an ID of `123` (but it is too late now)
- The Backbone Relational identity map (cache) is queried for `Food` with ID of `123`. The cached result `{"id":123,"name":"Sandwich"}` is returned.
- The page incorrectly renders `Sandwich` (instead of `Chicken Sandwich`) somewhere in the view

This serves to point out two things:

- Deciding when models are fetched or re-fetched must be thought of **in conjunction with** deciding when subscriptions to updates on these models are subscribed and unsubscribed.
- User expectations have changed. In a traditional web site, when the user presses the back button, it is completely OK to display the old and out-of-date data. It is OK for a refresh of the page to display something different compared to before the refresh. Now however, web sites are no more - web apps are where it is at. One of the expectations of a web app is for the data displayed to be "live".

### The In-Betweens

In the diagram above, `Quadrant 2` and `Quandrant 3` are in-betweens, as they are merely half of the way to solving the problem. As just pointed out, it is important for the strategy in managing model fetches, and the strategy in managing model state subscriptions, to be compatible.

While these quadrants represent a combination of strategies that are possible to adopt, in my opinion, they would be strategies in which two architectural decisions that have orthogonal desires being made to work together. As such, I shall leave an exploration of these as an exercise for the reader.

### The Always-On

In the diagram above, `Quadrant 4` would be ideal in managing long-lived model state in a SPA.

When the page is first loaded, models are fetched, and subscriptions to those models are initiated. When the user navigates to a different page, the models are maintained, as are the subscriptions to them. If new models exist on this page that have not been loaded prior, then these models are fetched, and subscriptions to those models are initiated. The main point to note is that no matter how many page navigations the user performs, each model will only be fetched once, and subscribed to once.

Ideal? Yes.

Works in the real world? Not quite.

The description above would have set off alarm bells right away. Why yes, given unlimited memory and bandwidth, why not do so? Unfortunately, both are constrained. One cannot indefinitely load more models without clearing out older ones at some stage. Keeping all those subscriptions open also eats into memory, even consume extra bandwidth at the same time.

Thus, the key to managing long-lived model state in SPAs becomes heavily dependent on memory management techniques.

## Wrap Up

We have looked at the fundamental differences between traditional web sites and present day single page applications; as well as the history of the decisions that we had to make along the way. With single page applications, two key decisions need to be made with regards to keeping model state synchronised between client and server, and we have taken a look at why those decisions need to be congruent.

We then explored the pros and cons of the strategy resultant from a combination of each of these decisions. The final, and possibly ideal strategy for managing long-lived model state, we find to be practically not feasible, because it would lead to memory consumption with a pattern typical of a memory leak - new memory allocated without any being freed.

A subsequent post will explore techniques to reign in memory consumption while maintaining model state.

----

<sup>1</sup> Considerations for keeping memory footprint in line:

- How can we keep track track of all models presently being displayed in views?
- Are least recently used queuing or garbage collection feasible?
