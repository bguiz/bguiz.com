---
title: Modernizr Command Line Interface
slug: modernizr-command-line-interface
author: bguiz
date: 2014-04-09
template: article.jade
tags:
- modernizr
- nodejs
- cli
---

Modernizr is an an excellent library used for client-side feature detection.

![Modernizr](http://modernizr.com/i/img/logo-x12.png)

The full set of features detected can be obtained using its [development version](http://modernizr.com/downloads/modernizr-latest.js),
however, including this entire file is not ideal in production -
why make users download all the extra code for feature detection tests that will not ever be used?

Thankfully, Modernizr has a nifty [download feature](http://modernizr.com/download/),
which lets you fill in a form to select which tests you want,
and then it generates a customised file for you to download.

## How about a CLI?

![Modernizr](http://modernizr.com/i/img/logo-x12.png "Modernizr") +
![Command Line](http://upload.wikimedia.org/wikipedia/commons/thumb/8/86/Blank-extended-keyboard.svg/300px-Blank-extended-keyboard.svg.png "Command Line") = ?

That is great, however, what if you want to use Modernizr to generate
custom files on the command line?

I came across [this closed ticket](https://github.com/Modernizr/Modernizr/issues/287) raised against Modernizr,
and that helped somewhat, as I found a way to generate custom files on the command line,
but the command I had to issue was extremely convoluted.

Seeking an easier way, [I came up with this](https://gist.github.com/bguiz/9926259),
which was a huge improvement in usability.

Now it is even easier:

		npm install -g modernizr-cli
		modernizr custom.json

(Where `custom.json` should conform to [this format](https://github.com/Modernizr/Modernizr/blob/master/lib/config-all.json))

Check it out:
[modernizr-cli on github](https://github.com/bguiz/modernizr-cli) |
[modernizr-cli on npm](https://www.npmjs.org/package/modernizr-cli)
