---
title: Creating images quickly using montage from imagemagick
slug: image-processing-cli-using-imagemagick-montage
author: bguiz
date: 2014-05-26
template: article.jade
tags:
- imagemagick
- gimpfu
- gimp
- montage
- cli
- image
- image processing
---

I was looking for a way to combine images together programmatically, and spent some time dabbling around in [`gimpfu` python land](http://www.gimp.org/docs/python/) without getting very far at all.

Who would have thought just placing one image next to another could get so hairy?

![This image was created using a single command line](https://31.media.tumblr.com/2c27307056c3243b3f4ba3ce20cc547b/tumblr_inline_n66mmnEQas1rer3hy.png)

What if I said that it was possible to create the above image from three images using a single command line? Read on!

I had one of those "Aha!" moments of discovery when I chanced upon this articles: [Imagemagick montage usage](http://www.imagemagick.org/Usage/montage/).

`montage foo.png bar.png -geometry 200X200+2+1  foobar.png`

No script involved, just a single command line; and that resizes both `foo.png` and `bar.png` to 200 by 200 pixels, arranges them into a two by one grid, adds some default spacing between the two, and saves the output to `foobar.png`.

If you want the background to be transparent, and to add a dropshadow, like I did, it is as simple as:

`montage foo.png bar.png -shadow -background None -geometry 200X200+2+1  foobar.png`

That is all that I needed for what I was doing, but the article explores quite a few other nifty things that the `montage` tool does - check out the polaroid filter:

![Imagemagick montage with polaroid filter](http://www.imagemagick.org/Usage/montage/polaroid_overlap.jpg)
