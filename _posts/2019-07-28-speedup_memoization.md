---
layout: post
comments: true
title: (Part 2) Speed up your code (Memoization decorator)
---

[Memoization](https://en.wikipedia.org/wiki/Memoization) refers to storing results of function calls so that we don't have to 
recompute the result when the same input appears again. 

Functions are type of abstraction, so given the same input they will produce the same output (well, unless there 
is randomness component in the function). So, a natural way to be more efficient is to avoid recomputing stuff you have already computed in the past.
Memoization achieves this by storing input/output pairs in a look-up table (like a dictionary).

To *memoize* a function we'll use a Python memoization decorator. If you don't know what decorators are
there are plenty of detailed [tutorials](https://realpython.com/primer-on-python-decorators/) out there. But basically,
a decorator serves as a wrapper for a function, 
altering the way it behaves. 

Out memoization decorator will serve to check if an input to a function has been encountered before; if yes, it will return the cached result.
Otherwise, it will actually call the function, compute and store the result and finally return it. 

### When NOT to use memoization:

- If your function has a side effect on some object (example mutates external variable or has mutable input)
- If you rarely or never have the same input
- If the computation in the function is actually short and simple 


### Example - Fibonacci number
Before we code the decorator, let's see the example.

In our example we want to compute the `n-th` [Fibonacci number](https://en.wikipedia.org/wiki/Fibonacci_number). Why would memoization be useful here? Consider the fifth Fibonacci number, F<sub>5</sub>. 
To compute this we need F<sub>4</sub> and F<sub>3</sub>. But to compute F<sub>4</sub> we need F<sub>3</sub>! See how we got F<sub>3</sub> twice there? Instead of computing it twice, we can just store the result the first time we encounter it. This is the canonical example
of why memoization is useful and we'll prove it again here.

```python
import time

def fib(n):
    """Recursively compute the n-th Fibonacci number"""
    return n if n < 2 else fib(n-1) + fib(n-2)

if __name__ == '__main__':
    n = 40
    tstart = time.time()
    output = fib(n)
    tend = time.time()
    print("The {}-th Fibonacci number is: {}".format(n, output))
    print("execution took: {:.3f} ms".format((tend - tstart) * 1000))

```
The output of the above is:
```
The 40-th Fibonacci number is: 102334155
execution took: 30949.738 ms
```
As you can see, calculating the 40-th Fibonacci number took ~30 
seconds! Well, that's a while.
 If we set `n = 41` we get:
 ```
 The 41-th Fibonacci number is: 165580141
execution took: 55417.119 ms
 ```
 55 seconds! That escalated quickly!
 What of we wanted to compute the 1000th, or 10000th Fibonacci number? Probably not in this lifetime with this function.

***
**Note**: In all implementations below we use dictionary as a type, so
 you need the function inputs to be of immutable type (like tuples, integers, strings) not mutable (such as lists, sets or dictionaries).
***

### Simple memoization decorator

Let's jump into the code for the simple memoization decorator:

```python
from functools import wraps

def memoize_simple(my_func):
    """Simple memoization decorator"""
    cache = dict()
    @wraps(my_func)
    def memoized_func(*args):
        if args in cache:
            return cache[args]
        result = my_func(*args)
        cache[args] = result
        return result

    return memoized_func
```

To use it, just put `@memoize_simple` on top of the function you want to memoize, like this:

```python
@memoize_simple
def fib(n):
    return n if n < 2 else fib(n-1) + fib(n-2)
```

With our decorator, we obtained the result in a fraction of a millisecond!
```
The 40-th Fibonacci number is: 102334155
execution took: 0.040 ms
```

### Enhanced Dictionary-based memoization decorators

#### Single argument functions

A faster memoization decorator for functions taking a single argument is the one using a dictionary:
```python
def memodict(my_func):
    """Memoization decorator for a function taking a single argument """
    class memodict(dict):
        def __missing__(self, key):
            result = self[key] = my_func(key)
            return result
    return memodict().__getitem__
```
Using this decorator gives us:
```
The 40-th Fibonacci number is: 102334155
execution took: 0.036 ms
```
#### Multiple argument functions
If you have a function with multiple arguments you will need a workaround using the above decorators, or simply use the following:

```python
def memodict_multiple_args(my_func):
    """ Memoization decorator for functions taking one or more arguments. """
    class memodict(dict):
        def __getitem__(self, *args):
            return dict.__getitem__(self, args)

        def __missing__(self, args):
            # cache miss; need to compute and store result
            result = self[args] = my_func(*args)
            return result

    return memodict().__getitem__
```
The output with this decorator shows just slightly longer execution time,
due to overhead of additional function call.

```
The 40-th Fibonacci number is: 102334155
execution took: 0.171 ms
```

***

Well this post turned out longer than I planned, but hopefully it will be helpful to someone whose code is slow.
The optimization benefits could be huge if you actually have repeating inputs to your function, so ask yourself to which of your functions this applies and feel free to
sprinkle `@memoize` on top of them. Ensure that you've read when NOT to use memoization above.

***
Core code concepts courtesy to [Oren Tirosh](http://code.activestate.com/recipes/578231-probably-the-fastest-memoization-decorator-in-the-/) and related commenters. 


