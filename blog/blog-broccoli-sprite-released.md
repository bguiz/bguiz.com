---
title: The ember-cli voyage begins
slug: broccoli-sprite-released
author: bguiz
date: 2014-06-14
template: article.jade
tags:
- broccolijs
- nodejs
- ember-cli
- javascript
- release
- css
- sprite
---

[![broccoli-sprite NPM](https://nodei.co/npm/broccoli-sprite.png?compact=true)](https://github.com/bguiz/broccoli-sprite)

After much frustration attempting to get [`broccoli-compass`](https://github.com/g13013/broccoli-compass) to work,
including asking a [question on Stackoverflow](http://stackoverflow.com/q/24074351/194982),
and getting no responses despite offering a bounty on it,
I decided to bite the bullet and write my own
[BroccoliJs](https://github.com/broccolijs/broccoli) plugin.

It was a little tough, because I am completely new to BroccoliJs,
having used GruntJs up until now.
In order to build an app using
[ember-cli](https://github.com/stefanpenner/ember-cli),
however, switching to BroccoliJs was a necessity.

So I present [broccoli-sprite](https://github.com/bguiz/broccoli-sprite),
hot off the presses.

To install it:

    npm install broccoli-sprite

To use it:

    var broccoliSprite = require('broccoli-sprite');
    var spritesTree = broccoliSprite('public', {
      src: [
        'public/images/sprites/*.png'
      ],
      spritePath: 'assets/sprites.png',
      stylesheetPath: 'assets/sprites.css',
      stylesheet: 'css',
      stylesheetOptions: {
        prefix: 'sprite-',
      },
    });

More detailed instructions in the
[README](https://github.com/bguiz/broccoli-sprite#a-broccoli-plugin-for-sprite-generation).
