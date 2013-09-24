# qryq

## Pronunciation

`/ˈkwərik/`

## What it is

`qry` is a NodeJs library that allows one to express a series of API queries and define the dependencies between them. These queries may be executed in parallel, in sequence, or in a directed acyclic graph.

Despite being non-RESTful, this makes code more readable and composable, and affords a number of benefits.

## Benefits

- Allows the programmer to state client-server interactions **declaratively**, and thus avoid both callback spaghetti code and promise spaghetti code.
- A reduction in total **bandwidth** and **latency**.
- A reduction in **duplication of business logic** on the client and the server.
- A treatment of **several queries as an atomic unit**, makes it easier to write asynchronous user interfaces.

## Example

```javascript
    [
      {"id":"qGeocodeOrigin","depends":[],"api":"gmapsGeoLookup","qry":{"address":"36 Meadow Wood Walk, Narre Warren VIC 3805"}},
      {"id":"qGeocodeDestination","depends":[],"api":"gmapsGeoLookup","qry":{"address":"19 Bourke Street, Melbourne, VIC 3000"}},
      {"id":"qScore","depends":["qGeocodeOrigin","qGeocodeDestination"],"api":"score","qry":{
          "origin":{"address":"36 Meadow Wood Walk, Narre Warren VIC 3805","lat":"#{qGeocodeOrigin}.lat","lon":"#{qGeocodeOrigin}.lon"},
          "journeyPlanner":"melbtrans",
          "destinations":[
            {
              "fixed":true,"class":"work","weight":0.8,
              "location":{"address":"19 Bourke Street, Melbourne, VIC 3000","lat":"#{qGeocodeDestination}.lat","lon":"#{qGeocodeDestination}.lon"},
              "modes":[{"form":"transit","max":{"time":2400}}]
            }
          ]
        }
      }
    ]
```

- Here we can see that we have defined three queries, named `qGeocodeOrigin`, `qGeocodeDestination`, `qScore`.
- The first two do not define any dependencies, but the thirds does.
  - `qryq` will figure out that it needs to execute `qScore`, only when both `qGeocodeOrigin` and `qGeocodeDestination` have completed, so that it may use its results.
- The `qScore` invokes the function that handles the `score` API, and passes in a set of presently known values through `qry`
- The `qry` for `qScore` also passes in variables, whose values are not known at the time of making the query.
  - An example: `"lon":"#{qGeocodeOrigin}.lon"`.
  - `qryq` will substitute the computed values for these variables when it is this query's turn to execute.

## More Details

[A full presentation on `qryq`](https://github.com/bguiz/qryq/blob/master/doco/present/markdown/present.md "qryq presentation")

`qryq` is a project that has spun off from another project of mine, [`walkre`](https://github.com/bguiz/walkre "walkre"). Most of the development of `qryq` has thus far occurred in `walkre`, and currently the `qryq` repository is merely a *snapshot* of the `qryq` code used in `walkre`. A proper extraction is on the plans.

## Open Source

`qryq` is open source, and you can [find it on github](https://github.com/bguiz/qryq "qryq source code on github")
