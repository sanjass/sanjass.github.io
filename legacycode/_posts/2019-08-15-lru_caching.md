---
layout: post
comments: true
title: Memory aware memoization (LRU cache)
---

In a [previous post](https://sanjass.github.io/notes/2019/07/28/speedup_memoization) we looked at how we can speed up our code by using memoization decorator. 

### Space-time tradeoff 

There is a catch with our memoization decorator! We not only care about speed, but also about memory. Actually, this is quite an important tradeoff in the world of CS, called [Space–time tradeoff](https://en.wikipedia.org/wiki/Space%E2%80%93time_tradeoff). Our storage (cache) that we used in our memorization decorator was **unbounded**, meaning it will keep growing until you run out of memory. Today we'll see how can we ensure this doesn't happen.

### Check how much RAM you have
On a side note, to check your total memory for Linux machine you can run `htop` (`sudo apt-get install htop` if you don't have it) in your terminal and check the total memory in the `Mem` bar. Alternatevely you can check `Memory` via the GUI, by going to `Settings` in the top right corner and `About this computer`.

### The solution

The solution is actually pretty intuitive and nothing too fancy. We will limit the size of our cache, while still trying to maximally leverage the performance benefits of caching. 

We will use LRU cache decorator. [LRU](https://en.wikipedia.org/wiki/Cache_replacement_policies#Least_recently_used_(LRU)) stands for `Least Recently Used` and it is a cache replacement technique where the least recently used items are discarded when a new item appears. 

While this technique is pretty widespread and works well in most cases, there are definitely situations where it might not be that helpful (imagine the size of your cache is 10 and you are continiously invoking your function with 11 different inputs in a cyclic manner).  

## How to use LRU cache in python

Incorporating LRU caching in python is as simple as importing `lru_cache` from `functools` and adding the decorator on top of your function definiton:

```python
from functools import lru_cache

@lru_cache(maxsize=512)
def my_function(arg):
	 ...
``` 
Easy peasy lemon squeezy, right?


Note than `maxsize` is something you can specify depending on your needs and memory capacity. If you don't set it, the cache will be able to grow unbounded (which is what we're trying to avoid!). It is recommended that `maxsize` is a power of 2.

### More about LRU in python

You can read more about `lru_cache` in python in the [offical functools package documentation](https://docs.python.org/3/library/functools.html).

Something useful from the documentation:
> An LRU (least recently used) cache works best when the most recent calls are the best predictors of upcoming calls (for example, the most popular articles on a news server tend to change each day). The cache’s size limit assures that the cache does not grow without bound on long-running processes such as web servers.

> In general, the LRU cache should only be used when you want to reuse previously computed values. Accordingly, it doesn’t make sense to cache functions with side-effects, functions that need to create distinct mutable objects on each call, or impure functions such as time() or random().

