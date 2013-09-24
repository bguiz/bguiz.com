<!-- Digest Cycles in Single Page Apps -->
<!-- javascript, game loop, digest cycle, emberjs, angularjs, backbonejs, single page apps, DOM manipulation -->

We will be taking a very concise look into digest cycles, and their use in Javascript single page applications.

*[Accompanying Slides](http://bguiz.github.io/preso-digest-cycles/ "Digest Cycles in Single Page Apps")*

![AngularJS digest cycle diagram](http://docs.angularjs.org/img/guide/concepts-runtime.png)

This is a diagram from the official AngularJS documentation that explains what a digest cycle is. If this goes over the top of your head, don't worry, it did for me too. It is more complicated than it needs to be.

A digest cycle is, simply put, code that runs at an interval. This interval may be sometimes simply as fast as possible after the previous one, and sometimes the interval is set.

They are typically used to aggregate expensive tasks such that they do not have to run multiple times when there is no need to do so. For example, DOM rendering.

While we are here to talk about digest cycles in the context of Javascript SPAs, a discussion of this will not be complete without talking about where the idea for this originated from.

The Game Loop is a term familiar to most games developers. When writing a game, one would typically set the interval of the game loop, such that each cycle runs as soon as possible after the previous one completes. As such a variable frame rate is inevitable, and techniques such as linear interpolation of time elapsed is used to calculate the rendered position of a moving object, for example.

*An [introduction](http://www.koonsolo.com/news/dewitters-gameloop/), and an [in-depth explanation](http://www.gameprogblog.com/generic-game-loop/).*

In single page apps, digest cycles are also used. Their intent here is quite different, because maxing out frames per second is not usually a goal for web apps. When using dirty checking to observe for model state changes, SPAs have taken two different approaches - [dirty checking and accessors](http://bguiz.com/post/57373805814/accessors-vs-dirty-checking-in-javascript-frameworks "Accessors vs. Dirty-checking"), which you can read about on my blog. In fact this presentation is a derivative work of that post.

*[AngularJS' $digest](https://github.com/angular/angular.js/blob/v1.0.x/src/ng/rootScope.js#L364), [EmberJS' Ember.run+runLoop](https://github.com/emberjs/ember.js/blob/1-0-beta/packages/ember-metal/lib/run_loop.js#L216)*

When using dirty checking, as AngularJS does, a digest cycle is necessary. When using the alternative to dirty checking - accessors - as BackboneJS and EmberJS do, a digest cycle is *not necessary*. In fact, BackboneJS does not have one, but EmberJS does.

So why are digest cycles important in SPA frameworks? As mentioned earlier, it is where you would put your code that dirty checks your models for differences to previous state. Secondly - and this is why EmberJS uses it despite using accessors - is that it improves efficiency.

Each change on each model sets off a DOM update, and thus a re-render, and a reflow. The latter two are expensive operations. Instead of doing this once per model change, we instead aggregate the changes until the point where the digest cycle executes - updating a purely in-memory buffer with the new DOM with all the changes. When the it is time for the digest cycle to execute, this buffer replaces the relevant parts of the DOM; which then re-renders and reflows just once per digest cycle.

Changes listeners that are triggered are aggregated in a similar manner, and all of them are fired at once during the digest cycle. 

<code><pre>
App.Person = Ember.Object.extend({
    bmi: function() {
        var height = this.get('height');
        return this.get('weight') / height / height;
    }.property('weight', 'height')
});
</pre></code>

Here is an example of a [computed property](https://github.com/emberjs/ember.js/blob/1-0-beta/packages/ember-runtime/lib/ext/function.js#L64 "Computed properties allow you to treat a function like a property") in an EmberJS app. What this says is "whenever the `weight` or `height` properties of a `Person` model change, its `bmi` property could change too."

Computed properties rely on the framework maintaining a directed acyclic graph (per model class) between properties, and needs to traverse them (per model instance). These traversals can be expensive, so we defer them to - you guessed it - the digest cycle.

Now that we know more about digest cycles - what is the main takeaway from it? The number one thing to avoid, is doing too much in each digest cycle. Once the time taken to perform the work needed in each digest cycle approaches or exceeds the duration of the interval between each one, the app will become noticeably laggy.

Here I have linked [Misko Hevery's Stackoverflow post on dirty checking](http://stackoverflow.com/a/9693933/194982), where he works out some numbers for what these thresholds are. If you are developing a SPA, especially one that is intended to run on a mobile device, you should check out his numbers. I have discussed [performance metrics](http://bguiz.com/post/57373805814/accessors-vs-dirty-checking-in-javascript-frameworks#performance) in a previous post.

<code><pre>
var socket = io.connect();
return {
  on: function (eventName, callback) {
    socket.on(eventName, _.throttle(function () {
      var args = arguments;
      $rootScope.$apply(function () {
        callback.apply(socket, args);
      });
    }, 500));  // limit to once every 500ms
  } // ...
};
</pre></code>

This snippet by Brian Ford is from his post on building huge AngularJS apps. To avoid executing this expensive function every digest cycle, he wraps it using [underscore's throttle function](http://underscorejs.org/#throttle), thereby ensuring that it only runs twice per second instead.

To round up:

- Digest cycles are quite similar to the Game Loop programming pattern
- They are used in SPAs to execute code at a set interval
- They are where dirty checking is done
- They are where DOM updates, listeners, and other expensive tasks are aggregated for performance gains
- Measure & curb the execution time of the work done in each digest cycle

----

These are my speaker notes rehashed as a blog post. The original talk was at [Web Directions WDYK](http://www.webdirections.org/events/wdyk-melbourne). Here are the [accompanying slides](http://bguiz.github.io/preso-digest-cycles/ "Digest Cycles in Single Page Apps") for it.
