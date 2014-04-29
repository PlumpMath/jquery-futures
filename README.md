jquery-futures
==============

A toolkit for managing jQuery Deferred and Promise objects. Inspired by Twitter Futures.

[![Build Status](https://travis-ci.org/karansag/jquery-futures.svg?branch=master)](https://travis-ci.org/karansag/jquery-futures)

Installation and Dependencies
=============================
Dependencies:
* jquery >= 1.8
* Natively supported Array.prototype.filter, forEach. [Compatibility](http://kangax.github.io/es5-compat-table/)

To install, simply copy and use src/futures.js. Note that, at the moment, this is only designed for client-side (browser) use.

API (at least some of it)
==========================
Use to chain computations onto deferred objects.
future.map (promise, fn) => promise
--------
    var d1 = $.Deferred();
    var d2 = future.map(d1, function(value){ return value * value });
    d1.resolve(5);
    d2.done(function(result){
        console.log(result)    // => 25
    });

Useful for transforming the value contained in a deferred object.

future.flatMap (promise, fn) => promise
------
    var d1 = $.Deferred();
    var d2 = $.Deferred();
    var someFunction = function(d1Result){ return d2.resolve(d1Result + 10) }
    var newPromise = future.flatMap(d1, someFunction);
    d1.resolve(7);
    newPromise.done(function(result){
      console.log(result) // => 17
    });

Useful for sequential, dependent calls that return deferred. For example, getting a uuid and
then account data based on that uuid through two AJAX calls.


future.join (promise1, promise2, ...) => promise
-----
    var query1 = $.Deferred();
    var query2 = $.Deferred();
    query1.resolve(6);
    query2.resolve(4);
    future.join(query1, query2).done(function(result1, result2){
      console.log(result1 + result2); // => 10
    });

Useful for synchronous calls. This directly proxies to jQuery.when. Note that the returned
promise succeeds if and only if all the passed promises succeed.

future.collect ([promise1, promise2,...]) => promise
----
(The array version of future.join)

Chaining style
----
Note that all results from the previous functions can be chained, OOP-style. Also, we expose a future function to kick off your promises' enhancements

    var d = $.Deferred()
    var f = future.map(d, ..).flatMap(..).handle(...)
    var g = future(d).map(...).flatMap(...).handle(...) // f and g are equivalent


Testing
==================
First, clone the repo.

*Via grunt and phantomjs:*

If you don't already have it, `npm install -g grunt-cli` for the grunt command line tool. Then,
`npm install` the dependencies from `package.json`.

You can run `grunt` for a single
task that compiles coffeescript and then runs jasmine tests or `grunt coffee`/`grunt test` for either one, respectively.
`grunt test` uses phantomjs to headlessly run the jasmine tests.

*Via the jasmine gem and your favorite browser:*

If you prefer a capitate browser, then`bundle install` the dependencies from the `Gemfile` and
run `rake jasmine`. The tests will by default run on port 8888.
