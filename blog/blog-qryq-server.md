<!-- Introducing qryq server -->
<!-- javascript, nodejs, qryq, expressjs, scaffold -->

I have given [talks on qryq a few times now](http://bguiz.github.io/qryq/), and,
quite often I have been asked, about how to actually begin using `qryq`.

`qryq` is a general purpose library, and it can be used in a number of different ways.
The most common, or default, use for it is to expose a single API endpoint on a server,
where any number of APIs can be invoked in a query queue specified by the client.
Thus I have created a template server that does just this,
and have (creatively) decided to name it `qryq-server`.

This project is most useful as a scaffold or template,
where you simply make a copy of it,
and in most cases merely need to add more methods to the `api` object.

- Check out [`qryq-server` on github](https://github.com/bguiz/qryq-server)
- Check out [`qryq-server` on npm](https://npmjs.org/package/qryq-server)
