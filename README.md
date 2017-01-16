# Promise

This is a implementation of Promises in Python.
It is a super set of Promises/A+ designed to have readable, performant code and to provide just the extensions that are absolutely necessary for using promises in Python.

Its fully compatible with the [Promises/A+ spec](http://promises-aplus.github.io/promises-spec/)

[![travis][travis-image]][travis-url]
[![pypi][pypi-image]][pypi-url]
[![coveralls][coveralls-image]][coveralls-url]

[travis-image]: https://img.shields.io/travis/syrusakbary/promises.svg?style=flat
[travis-url]: https://travis-ci.org/syrusakbary/promises
[pypi-image]: https://img.shields.io/pypi/v/promises.svg?style=flat
[pypi-url]: https://pypi.python.org/pypi/promises
[coveralls-image]: https://coveralls.io/repos/syrusakbary/promises/badge.svg?branch=master&service=github
[coveralls-url]: https://coveralls.io/github/syrusakbary/promises?branch=master

## Installation

    $ pip install promises


## Usage

The example below shows how you can load the promises library.  It then demonstrates creating a promises from scratch.  You simply call `Promise(fn)`.  There is a complete specification for what is returned by this method in [Promises/A+](http://promises-aplus.github.com/promises-spec/).

```python
from promises import Promise

promises = Promise(
    lambda resolve, reject: resolve('RESOLVED!')
)
```

## API

Before all examples, you will need:

```python
from promises import Promise
```

### Promise(resolver)

This creates and returns a new promises.  `resolver` must be a function.  The `resolver` function is passed two arguments:

 1. `resolve` should be called with a single argument.  If it is called with a non-promises value then the promises is fulfilled with that value.  If it is called with a promises (A) then the returned promises takes on the state of that new promises (A).
 2. `reject` should be called with a single argument.  The returned promises will be rejected with that argument.

### Class Methods

  These methods are invoked by calling `Promise.methodName`.

#### Promise.resolve(value)

Converts values and foreign promises into Promises/A+ promises.  If you pass it a value then it returns a Promise for that value.  If you pass it something that is close to a promises (such as a jQuery attempt at a promises) it returns a Promise that takes on the state of `value` (rejected or fulfilled).

#### Promise.rejected(value)

Returns a rejected promises with the given value.

#### Promise.all(list)

Returns a promises for a list.  If it is called with a single argument then this returns a promises for a copy of that list with any promises replaced by their fulfilled values.  e.g.

```python
p = Promise.all([Promise.resolve('a'), 'b', Promise.resolve('c')]) \
       .then(lambda res: res == ['a', 'b', 'c'])

assert p.get() is True
```

#### Promise.promisify(obj)

This function wraps the `obj` act as a `Promise` if possible.
Python `Future`s are supported, with a callback to `promises.done` when resolved.


#### Promise.for_dict(d)

A special function that takes a dictionary of promises and turns them
into a promises for a dictionary of values.  In other words, this turns
an dictionary of promises for values into a promises for a dictionary
of values.


### Instance Methods

These methods are invoked on a promises instance by calling `myPromise.methodName`

### promises.then(on_fulfilled, on_rejected)

This method follows the [Promises/A+ spec](http://promises-aplus.github.io/promises-spec/).  It explains things very clearly so I recommend you read it.

Either `on_fulfilled` or `on_rejected` will be called and they will not be called more than once.  They will be passed a single argument and will always be called asynchronously (in the next turn of the event loop).

If the promises is fulfilled then `on_fulfilled` is called.  If the promises is rejected then `on_rejected` is called.

The call to `.then` also returns a promises.  If the handler that is called returns a promises, the promises returned by `.then` takes on the state of that returned promises.  If the handler that is called returns a value that is not a promises, the promises returned by `.then` will be fulfilled with that value. If the handler that is called throws an exception then the promises returned by `.then` is rejected with that exception.

#### promises.catch(on_rejected)

Sugar for `promises.then(None, on_rejected)`, to mirror `catch` in synchronous code.

#### promises.done(on_fulfilled, on_rejected)

The same semantics as `.then` except that it does not return a promises and any exceptions are re-thrown so that they can be logged (crashing the application in non-browser environments)

## Other package functions

### is_thenable(obj)

This function checks if the `obj` is a `Promise`, or could be `promisify`ed.


# Notes

This package is heavily insipired in [aplus](https://github.com/xogeny/aplus).

## License

[MIT License](https://github.com/syrusakbary/promises/blob/master/LICENSE)
