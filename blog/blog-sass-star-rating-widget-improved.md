---
title: New & Improved: Star rating widget with SASS
slug: sass-star-rating-widget-improved
author: bguiz
date: 2014-03-02
template: article.jade
tags:
- sass
- css
- html5
---

I came across this interesting write up on a [star rating system with SASS](http://hugogiraudel.com/2014/02/24/star-rating-system-with-sass/).

I happened to be creating something with a star rating system myself, so I decided to incorporate this. Unfortunately, I found it to be a bit clunky, requiring classes to be set in two places - once on the `<ul>` and once on the `<li>` - in the case of a half star.

While this was possible to do in an AngularJs `directive` or an EmberJs `Component`, the code for it proved to be a little unwieldy for my liking. I wanted to simply set one class, on the `<ul>`, and not have to do anything else.

So I came up with some [improvements of my own](http://codepen.io/bguiz/pen/dtbFv). [And in gist form](https://gist.github.com/bguiz/9245614).

[![SASS star rating widget improved](https://31.media.tumblr.com/4ce4e3ac9eff340e03168f0845409546/tumblr_inline_n1sgilq6yZ1rer3hy.png)](http://codepen.io/bguiz/pen/dtbFv)
