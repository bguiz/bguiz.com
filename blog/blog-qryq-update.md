<!-- An update on qryq -->
<!-- qryq, nodejs, javascript, update, publish -->

Some important changes to [qryq](https://github.com/bguiz/qryq) since its [first announcement](http://bguiz.com/post/54620002947/qryq):

- Picked [GPL v3](http://opensource.org/licenses/GPL-3.0) as the licence
- Fully extracted as its own project
- Inline referencing + property drilldown of the results of other `qry`s
- Specifying `depends` for each `qry` is now optional, and will be inferred when not present
- Unit tested using [`nodeunit`](https://github.com/caolan/nodeunit)

Qryq has also just been [published as a node module](https://npmjs.org/package/qryq "qryq on node package manager"). You can now get it using:

`npm install qryq`

[Pull requests and suggestions](https://github.com/bguiz/qryq/issues) are most welcome.

Happy coding!
