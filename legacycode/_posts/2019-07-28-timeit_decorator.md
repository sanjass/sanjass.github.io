---
layout: post
comments: true
title: Python profiling (Timing decorator)
---

[Profiling](https://en.wikipedia.org/wiki/Profiling_(computer_programming)), to put it simply, means analyzing your code for memory usage or duration
 as a means to find places where it can be optimized.
 
In this post I'll explain how to use a Python timing decorator to see how long a particular function took to execute.


We've seen the Python memoization decorator in [another post](https://sanjass.github.io/notes/2019/07/28/speedup_memoization). Again, a decorator serves as a wrapper for a function, 
altering the way the function behaves. Today, our decorator will simply measure the duration of a function
execution using the [time](https://docs.python.org/3/library/time.html#module-time) built-in module.

Let's see the code for the decorator:

```python
from functools import wraps
import time

def timeit(my_func):
    @wraps(my_func)
    def timed(*args, **kw):
    
        tstart = time.time()
        output = my_func(*args, **kw)
        tend = time.time()
        
        print('"{}" took {:.3f} ms to execute\n'.format(my_func.__name__, (tend - tstart) * 1000))
        return output
    return timed
```

In the code above `my_func` is a reference to the function we want to measure the duration of. Note that in the wrapper function we call the original function with the original arguments and eventually we print the difference between the time just before execution and the time just after the execution.

To use this decorator, simply put `@timeit` right above the function you want to time. Let's see a simple example:

```python
@timeit
def sum_numbers(n):
    """Sum the numbers from 1 to n inclusive"""
    return sum([number for number in range(1, n+1)])


if __name__ == '__main__':
    n = 100
    print("The sum of the first {} numbers is: {}".format(n, sum_numbers(n)))
```

This gives us:
```
"sum_numbers" took 0.008 ms to execute

The sum of the first 100 numbers is: 5050
```
If we run the same function with a larger input value, we see increase in the duration of the function execution, as expected.

```
"sum_numbers" took 48.398 ms to execute

The sum of the first 1000000 numbers is: 500000500000
``` 

Couple of notes:

- If you need to do serious profiling in Python consider the build-in profiler [cProfile](https://docs.python.org/3/library/profile.html#module-cProfile). It gives information about every function call as well as how many times it was called.
- If you don't want to use decorators at all, but want to just time your function once you can use the `time` module in isolation, like this:
```python
n = 1000000
tstart = time.time()
output = sum_numbers(n)
tend = time.time()
print("The sum of the first {} numbers is: {}".format(n, output))
print("execution took: {:.3f} ms".format((tend - tstart) * 1000))
```
However, this can get pretty messy if you have several functions that you want to time. 
On the other hand, the decorator is there to be used whenever and makes things much neater, much more pythonic. 
 
- Oh, also, if you ever actually need the sum of the first `n` numbers, just use the [Gauss's trick](https://nzmaths.co.nz/gauss-trick-staff-seminar) formula: `0.5*n*(n+1)`. It's clearly much faster, try it :) .
