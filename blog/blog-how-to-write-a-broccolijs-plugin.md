---
title: How to Write a BroccoliJs Plugin
slug: broccolijs-plugin-how-to-write
author: bguiz
date: 2014-06-14
template: article.jade
tags:
- broccolijs
- nodejs
- javascript
- tutorial
---

Recently, I released [broccoli-sprite](https://github.com/bguiz/broccoli-sprite).
I was just a week into using BroccoliJs for the first time,
and writing a plugin for a build system that I had barely used
was understandably tricky.

While writing it, I googled quite a bit for how to write a BroccoliJs plugin,
but there really has not been much written about it.
I would like to make it easier for others doing the same thing,
so here is a quick overview of the process of creating a BroccoliJs plugin.

## Basics

What is [BroccoliJs](https://github.com/broccolijs/broccoli)?
Think [GruntJs], but different.
Different how?
Well in a number of ways. Its creator, Jo Liss,
[explains its philosophy in her post on its release].

tl;dr= Plugins can chain their output to one another,
and the built-in watch only rebuilds what has changed rather than the whole lot.

There is one more thing to it:
If you are building an app using [ember-cli](https://github.com/stefanpenner/ember-cli),
you will *need* to use BroccoliJs.

## Sold! Now Time to Write a BroccoliJs Plugin

The first thing to take a look at is the
[plugin API specification](https://github.com/broccolijs/broccoli#plugin-api-specification).
Looks very straight forward:
There are just two things that you need to implement:
`tree.read()`and `tree.cleanup()`.
The former, however, does not really do much that is useful, on its own at least.

## Getting started

All BroccoliJs plugins are NodeJs modules that should be installed
inside a project

    cd my-project/
    npm install --save-dev broccoli-my-plugin

&hellip; and thus the first step is to create an `npm` package:

    mkdir broccoli-my-plugin #replace `my-plugin` with the name you would like
    cd broccoli-my-plugin/
    #if you plan to use version control (which is a good idea), do it now, e.g.
    #git init && git flow init
    npm init #this creates `package.json`
    #be sure to specifiy one of the keywords as `broccoli-plugin`

Now you will need to edit `index.js`, which exports your plugin:

    var BroccoliMyPlugin = function BroccoliMyPlugin() {};
    modules.exports = BroccoliMyPlugin;

### Extending an Existing BroccoliJs Plugin

BroccoliJs has several plugins, that are designed to be extended.
The one that we will look at here is
[broccoli-writer](https://github.com/broccolijs/broccoli-writer).

Install it:

    npm install broccoli-writer

Edit `index.js`:

    var brocWriter = require('broccoli-writer');
    
    var BroccoliMyPlugin = function BroccoliMyPlugin() {
      if (!(this instanceof BroccoliMyPlugin)) {
        return new BroccoliMyPlugin();
      }
    };
    BroccoliMyPlugin.prototype = Object.create(brocWriter.prototype);
    BroccoliMyPlugin.prototype.constructor = BroccoliMyPlugin;
    BroccoliMyPlugin.prototype.description = 'my-plugin';
    modules.exports = BroccoliMyPlugin;

Here we have simply extended the function exported by broccoli-writer
using prototypical inheritance.
At the moment it does not do anything at all,
and we will add that next.

### Adding functionality

Firstly, we should make the plugin able to accept some input parameters.
All BroccoliJs plugins *must* accept an input tree as its first argument.
Any subsequent parameters are completely up to you as the plugin developer.
A common pattern, however, seems to be to accept just one parameter,
and `options` hash, which is what we will do here.

    var brocWriter = require('broccoli-writer');
    
    var BroccoliMyPlugin = function BroccoliMyPlugin(inTree, options) {
      if (!(this instanceof BroccoliMyPlugin)) {
        return new BroccoliMyPlugin(inTree, options);
      }
      this.inTree = inTree;
      this.options = options || {};
    };
    BroccoliMyPlugin.prototype = Object.create(brocWriter.prototype);
    BroccoliMyPlugin.prototype.constructor = BroccoliMyPlugin;
    BroccoliMyPlugin.prototype.description = 'my-plugin';
    modules.exports = BroccoliMyPlugin;

We add the `inTree` and `options` parameters to the constructor function,
and then save them in the instance.
If you wish to specify default options,
or other instance variables,
this is where you would parse and set them.

Next we can implement the main functionality,
the part where we specify the thing that this plugin does.
Since this plugins extends the `broccoli-writer` plugin,
we do this by specifying a `write`function:

    BroccoliMyPlugin.prototype.write = function(readTree, destDir) {
      var self = this;
      return readTree(this.inTree).then(function (srcDir) {
        /* use srcDir and information from self.options to figure out which files to read from */
        /* use destDir and information from self.options to figure outwhich files to write to */
        /* synchronously read input files, do some processing, and write output files */
      });
    };

`readTree` is passed in as the first variable to the `write function,
and this is a function that returns a promise that you should return.
Call `then()`on the promise, and do the processing in the callback function.
Here you do whatever it is the plugin needs to do;
but you have to do it synchronously - no callbacks allowed.

### Asynchronous Plugins

Most of the time however, we want to do things asynchronously -
after all, that is the NodeJs way!
See Mixu's article on [control flow in NodeJs](http://book.mixu.net/node/ch7.html)
for an excellent introduction to asynchronous code in NodeJs.
We need to get a little more advance than this however,
and use promises instead of callbacks.
Not to worry though, promises are actually much more straight forward to use
than callbacks!
In fact, we have already used the one returned by the `readTree` function previously.

We shall use promises implemented in the RSVP library,
as that appears to be the most popular choice amongst Broccoli plugins;
although you are free to use any other promise library.

Install RSVP:

    npm install --save rsvp

Include RSVP:

    var rsvp= require('rsvp');

Modify the `readTree` callback to create a promise an return it.

    return readTree(this.inTree).then(function (srcDir) {
      /* use srcDir and information from self.options to figure out which files to read from */
      /* use destDir and information from self.options to figure outwhich files to write to */
      var promise = new rsvp.Promise(function(resolvePromise, rejectPromise) {
        /* asynchronously read input files, do some processing, and write output files,
           for example, here we have `someAsyncFunc` that does this` */
        someAsyncFunc(function(err, asyncData) {
          if (err) {
            rejectPromise(err);
          }
          else {
            resolvePromise(asyncData);
          }
        });
      });
      return promise;
    });

Here, since we return a promise, BroccoliJs knows to wait until it is
either resolved or rejected.
For the more astute, you will notice that here we actually have
a promise within a promise, as `readTree` itself returns a promise.
We could possibly refactor this to chain the promises instead of nesting them,
but I shall leave that as an exercise for the reader!

## Fin

Now we have a functional BroccoliJs plugin, and it is ready to be published:

    npm publish

&hellip; and now anyone can `npm install` it!

## Going further

Besides [broccoli-writer](https://github.com/broccolijs/broccoli-writer),
there is also [broccoli-filter](https://github.com/broccolijs/broccoli-filter),
and [broccoli-caching-writer](https://github.com/rjackson/broccoli-caching-writer), 
which I have not covered here.

Depending on what your plugin does, you might want to extend these instead.
One great way to learn more about writing BroccoliJs plugins is to
[search for existing ones](https://www.npmjs.org/search?q=broccoli-plugin),
and examine the source code for each one.
Most of them are fairly simple, only containing a single `index.js` file,
which means that you will likely find what you are looking for rather quickly.
In fact, that is precisely what I did to get up to speed,
when writing [brocoli-sprite](https://github.com/bguiz/broccoli-sprite).

[![broccoli-sprite NPM](https://nodei.co/npm/broccoli-sprite.png?compact=true)](https://github.com/bguiz/broccoli-sprite)

Good luck with yours!
