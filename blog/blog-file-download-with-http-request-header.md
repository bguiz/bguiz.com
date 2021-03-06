---
title: File Download with HTTP Request Header
slug: file-download-with-http-request-header
author: bguiz
date: 2014-07-02
template: article.jade
tags:
- javascript
- html5
- http
---

In a website which uses session-based authentication,
when a file needs to be downloaded, 
and that file should only accessible by the currently logged in user,
making that work client side in a web page is extremely easy.
That is because the session credentials are typically stored inside cookies,
and the browser automatically adds the cookies to every HTTP request's headers
for that domain.

When you create an anchor tag, and set its URL to point to the route
that responds with the file to be downloaded;
and this anchor tag is clicked, that file will get downloaded,
as the authentication requirement for that route is satisfied by the cookie
that gets automatically added to the HTTP request header by the the browser.

However, it is not quite so simple if the website uses token-based authentication.
This is because browsers do not have any mechanism where it can be told to
add the token to each HTTP request's headers across the board.

Let us say that you do the same thing as before:
create an anchor tag, and set its the URL to point to the route
that responds with the file to be downloaded.
The only difference is that this time, that route requires token in the header,
and there are no cookies involved.
Now when you click on this anchor tag,
the authentication requirement *is not met*,
and the file does not get downloaded.

My instinctive reaction to this was to find out a way to add
the token to the header of the HTTP `GET` request that gets
sent upon clicking the anchor link.
It turns out, however, that there is *no way* to do this;
there is simply no way to intercept that request and modify it before it gets sent.

So I asked [this question on Stackoverflow](http://stackoverflow.com/a/24523253/194982 "How to set a header for a HTTP GET request, and trigger file download?").

The only way to add a header to any HTTP request is using AJAX -
by creating a `XMLHttpRequest`.
However, the catch is that you simply get the data in a JavaScript variable
in the callback function when the AJAX response arrives.
It does not trigger a file download, like clicking an anchor tag would.

<blockquote class="twitter-tweet" data-cards="hidden" lang="en"><p>How to set a header for a HTTP GET request, and trigger file download? <a href="http://t.co/u7PnurXDjM">http://t.co/u7PnurXDjM</a></p>&mdash; Brendan Graetz (@bguiz) <a href="https://twitter.com/bguiz/statuses/483789449482862593">July 1, 2014</a></blockquote>

How do we get around this? 
Turns out that there are a couple of rather creative solutions to the problem.

<blockquote class="twitter-tweet" data-cards="hidden" lang="en"><p>Set a header on the HTTP request and have that trigger a file download with the response <a href="http://t.co/UIXr9CXXcO">http://t.co/UIXr9CXXcO</a> <a href="https://twitter.com/hashtag/javascript?src=hash">#javascript</a> <a href="https://twitter.com/hashtag/html5?src=hash">#html5</a> <a href="https://twitter.com/hashtag/solved?src=hash">#solved</a></p>&mdash; Brendan Graetz (@bguiz) <a href="https://twitter.com/bguiz/statuses/484204570071953408">July 2, 2014</a></blockquote>

When the anchor tag is clicked, intercept the event, and initiate an AJAX request,
being sure to add the appropriate token in the request header:

        var id = 123;
        var req = ic.ajax.raw({
            type: 'GET',
            url: '/api/dowloads/'+id,
            beforeSend: function (request) {
                request.setRequestHeader('token', 'token for '+id);
            },
            processData: false
        });

When the response is returned, we use a temporary anchor tag when handling it:

        req.then(
            function resolve(result) {
                var str = result.response;

                var anchor = $('.vcard-hyperlink');

                /* transform the response into a file */

            }.bind(this),
            function reject(err) {
                console.log(err);
            }
        );

Depending on the size of the response,
and whether the browser is modern enough to support HTML5 File APIs,
we either use base64 encoding or temporary files.

Using HTML5 temporary files:

                var maxSizeForBase64 = 1048576; //1024 * 1024
                var windowUrl = window.URL || window.webkitURL;
                if (str.length > maxSizeForBase64 && typeof windowUrl.createObjectURL === 'function') {
                    var blob = new Blob([result.response], { type: 'text/bin' });
                    var url = windowUrl.createObjectURL(blob);
                    anchor.prop('href', url);
                    anchor.prop('download', id+'.bin');
                    anchor.get(0).click();
                    windowUrl.revokeObjectURL(url);
                }

Using base64 encoding:

                else {
                    //use base64 encoding when less than set limit or file API is not available
                    anchor.attr({
                        href: 'data:text/plain;base64,'+FormatUtils.utf8toBase64(result.response),
                        download: id+'.bin',
                    });
                    anchor.get(0).click();
                }

In both cases we set the anchor tag to a data URI or file URI,
and then trigger a click event on it.

The caveat for this however, is that both of these approaches are going to be
rather inefficient when downloading and processing large files.
More so for the base64 encoding method than the HTML5 File API method.

One way of solving this problem is to modify the server such that the route that
requires the token in the HTTP header does not respond with file contents,
but instead with the URL of a another route,
which *does not* require anything in the header at all, 
but expires very quickly.

In my case however, I only needed to download rather small files
(mostly under 1KB), so this worked very well,
as I wanted to find if there was a way to solve this problem client-side.
With large files however, I would recommend considering using a server-side solution.

<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>
