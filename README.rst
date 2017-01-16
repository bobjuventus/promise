Promise
=======

This is a implementation of Promises in Python. It is a super set of
Promises/A+ designed to have readable, performant code and to provide
just the extensions that are absolutely necessary for using promises in
Python.

This was forked to make it fully compatible with the `Promises/A+
spec <http://promises-aplus.github.io/promises-spec/>`__

On completion of `promises PR #20 <https://github.com/syrusakbary/promises/pull/20>`__,
maintenence for this fork will be dropped.

|travis| |pypi| |coveralls|

Installation
------------

::

    $ pip install promises

Usage
-----

The example below shows how you can load the promises library. It then
demonstrates creating a promises from scratch. You simply call
``Promise(fn)``. There is a complete specification for what is returned
by this method in
`Promises/A+ <http://promises-aplus.github.com/promises-spec/>`__.

.. code:: python

    from promises import Promise

    promises = Promise(
        lambda resolve, reject: resolve('RESOLVED!')
    )

API
---

Before all examples, you will need:

.. code:: python

    from promises import Promise

Promise(resolver)
~~~~~~~~~~~~~~~~~

This creates and returns a new promises. ``resolver`` must be a function.
The ``resolver`` function is passed two arguments:

1. ``resolve`` should be called with a single argument. If it is called
   with a non-promises value then the promises is fulfilled with that
   value. If it is called with a promises (A) then the returned promises
   takes on the state of that new promises (A).
2. ``reject`` should be called with a single argument. The returned
   promises will be rejected with that argument.

Class Methods
~~~~~~~~~~~~~

These methods are invoked by calling ``Promise.methodName``.

Promise.resolve(value)
^^^^^^^^^^^^^^^^^^^^^^

Converts values and foreign promises into Promises/A+ promises. If you
pass it a value then it returns a Promise for that value. If you pass it
something that is close to a promises (such as a jQuery attempt at a
promises) it returns a Promise that takes on the state of ``value``
(rejected or fulfilled).

Promise.rejected(value)
^^^^^^^^^^^^^^^^^^^^^^^

Returns a rejected promises with the given value.

Promise.all(list)
^^^^^^^^^^^^^^^^^

Returns a promises for a list. If it is called with a single argument
then this returns a promises for a copy of that list with any promises
replaced by their fulfilled values. e.g.

.. code:: python

    p = Promise.all([Promise.resolve('a'), 'b', Promise.resolve('c')]) \
           .then(lambda res: res == ['a', 'b', 'c'])

    assert p.get() is True

Promise.promisify(obj)
^^^^^^^^^^^^^^^^^^^^^^

This function wraps the ``obj`` act as a ``Promise`` if possible. Python
``Future``\ s are supported, with a callback to ``promises.done`` when
resolved.

Promise.for\_dict(d)
^^^^^^^^^^^^^^^^^^^^

A special function that takes a dictionary of promises and turns them
into a promises for a dictionary of values. In other words, this turns an
dictionary of promises for values into a promises for a dictionary of
values.

Instance Methods
~~~~~~~~~~~~~~~~

These methods are invoked on a promises instance by calling
``myPromise.methodName``

promises.then(on\_fulfilled, on\_rejected)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This method follows the `Promises/A+
spec <http://promises-aplus.github.io/promises-spec/>`__. It explains
things very clearly so I recommend you read it.

Either ``on_fulfilled`` or ``on_rejected`` will be called and they will
not be called more than once. They will be passed a single argument and
will always be called asynchronously (in the next turn of the event
loop).

If the promises is fulfilled then ``on_fulfilled`` is called. If the
promises is rejected then ``on_rejected`` is called.

The call to ``.then`` also returns a promises. If the handler that is
called returns a promises, the promises returned by ``.then`` takes on the
state of that returned promises. If the handler that is called returns a
value that is not a promises, the promises returned by ``.then`` will be
fulfilled with that value. If the handler that is called throws an
exception then the promises returned by ``.then`` is rejected with that
exception.

promises.catch(on\_rejected)
^^^^^^^^^^^^^^^^^^^^^^^^^^^

Sugar for ``promises.then(None, on_rejected)``, to mirror ``catch`` in
synchronous code.

promises.done(on\_fulfilled, on\_rejected)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The same semantics as ``.then`` except that it does not return a promises
and any exceptions are re-thrown so that they can be logged (crashing
the application in non-browser environments)

Other package functions
-----------------------

is\_thenable(obj)
~~~~~~~~~~~~~~~~~

This function checks if the ``obj`` is a ``Promise``, or could be
``promisify``\ ed.

Notes
=====

This package is heavily insipired in
`aplus <https://github.com/xogeny/aplus>`__.

License
-------

`MIT
License <https://github.com/syrusakbary/promises/blob/master/LICENSE>`__

.. |travis| image:: https://img.shields.io/travis/syrusakbary/promises.svg?style=flat
   :target: https://travis-ci.org/syrusakbary/promises
.. |pypi| image:: https://img.shields.io/pypi/v/promises.svg?style=flat
   :target: https://pypi.python.org/pypi/promises
.. |coveralls| image:: https://coveralls.io/repos/syrusakbary/promises/badge.svg?branch=master&service=github
   :target: https://coveralls.io/github/syrusakbary/promises?branch=master
