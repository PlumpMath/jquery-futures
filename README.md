jquery-futures
==============

A toolkit for combining and manpulating asynchronous tasks based on jQuery's Promise implementation. Inspired by Twitter Futures.

[![Build Status](https://travis-ci.org/karansag/jquery-futures.svg?branch=master)](https://travis-ci.org/karansag/jquery-futures)

Installation and Dependencies
=============================
Dependencies:
* jquery >= 1.8
* Natively supported Array.prototype.filter, forEach. [Compatibility](http://kangax.github.io/es5-compat-table/)

To install, simply copy and use src/futures.js. At the moment, this library's only designed for client-side (browser) use.

API
==========================

Future.map (promise, function) => promise
--------
    var d1 = $.Deferred();
    var d2 = Future.map(d1, function(value){ return value * value });
    d1.resolve(5);
    d2.done(function(result){
        console.log(result)    // => 25
    });

Useful for transforming the value contained in a deferred object.

Future.flatMap (promise, function) => promise
------
    var d1 = $.Deferred();
    var d2 = $.Deferred();
    var d2Getter = function(d1Result){ return d2.resolve(d1Result + 10) }
    var combinedPromise = Future.flatMap(d1, d2Getter);
    d1.resolve(7);
    combinedPromise.done(function(result){
      console.log(result) // => 17
    });

Useful for sequential, dependent calls that each return promises. For example, getting a uuid and
then account data based on that uuid through two AJAX calls.


Future.join (promise1, promise2, ...) => promise
-----
    var query1 = $.Deferred();
    var query2 = $.Deferred();
    query1.resolve(6);
    query2.resolve(4);
    Future.join(query1, query2).done(function(result1, result2){
      console.log(result1 + result2); // => 10
    });

Combine promises. This directly proxies to jQuery.when. Note that the returned
promise succeeds if and only if all the passed promises succeed.

Future.collect ([promise1, promise2,...]) => promise
----
(The array version of Future.join)

Future.rescue (promise, function) => promise
----

Future.handle (promise, function) => promise
----

Chaining style
----
You can chain results from the previous functions, OOP-style. Also, you can run
your promise through a Future function that adds the chaining functions to
the future you pass it.

    var d = $.Deferred()
    var f = Future.map(d, ..).flatMap(..).handle(...)
    var g = Future(d).map(...).flatMap(...).handle(...) // f and g are equivalent


Future.retry (futureGenerator, backoffGenerator) => promise
----
Given a function that returns a promise, returns another promise that will retry the given `futureGenerator` upon failure with a timeout specified by the value returned by the `backoffGenerator` function. Stops retrying when `backoffGenerator` returns null.

Note: calls `futureGenerator` immediately.


Future.retryWithConstantBackoff (futureGenerator, interval, maxAttempts) => promise
----
A special case of the above, retries the given `futureGenerator` a maximum of maxAttempts times with a constant interval of `interval`.


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
