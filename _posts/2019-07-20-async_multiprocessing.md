---
layout: post
comments: true
title: (Part 1) Speed up your code (Async. Multiprocessing Python3) 
---

You know the scenario: you have a list of datapoints and you need to apply certain function on each one. They don't depend on eachother. The order doesn't matter, you just need to process them all eventually.

As your dataset (your list of elements) increases and the function itself starts doing more complex (CPU intensive) processing, you start to lose your patience.


It's time to speed things up.

Chances are you're not utilizing all your CPU processors. If you want to verify this, run the command `htop` in your terminal; the CPU usage will be given at the top. If you don't have `htop` installed, now is the time to do it: `sudo apt-get install htop`.

So, how do we actually do it in python?
Before we dig in, ensure the following applies in your case:

- you have an iterable of datapoints and you need to apply your function to each one of them independently. 
- Your bottleneck is CPU-bound (not I/O bound). This means the reason your program is slow is because you're doing computationally intensive operations that increase CPU usage. If you need to read/write to a lot of files, this post is not for you.
- You have a function that can be independently executed at any time; it does not depend on some other function finishing earlier and does not modify global variable where the order matters (and the order is not recoverable)


**Example problem**

We have a list of sentences. We want to count the number of vowels in each one and sort them in ascending order based on the count. (I'm not sure why this would be useful, except maybe illustrating a point)

Here's a small random dataset we'll use:
```
data = ["I currently have 4 windows open up… and I don’t know why.",
        "Lets all be unique together until we realise we are all the same.",
        "They got there early, and they got really good seats."
        "The quick brown fox jumps over the lazy dog.",
        "I often see the time 11:11 or 12:34 on clocks.",
        "Cats are good pets, for they are clean and are not noisy.",
        ]
```
Here's the code without any optimizations:

#### Code without multiprocessing:
```python

def count_vowels(sentence, idx):
    """Get the number of vowels in a sentence"""
    counter = 0
    for ch in sentence:
        if ch in {'a', 'e', 'i', 'o', 'u'}:
            counter += 1
    return counter, idx

if __name__ == '__main__':
    result = [count_vowels(sent, idx) for idx, sent in enumerate(data)]
    sorted_result = sorted(result, key=lambda x: x[0])  # sort by first value
    sorted_sentences = [data[idx] for _, idx in sorted_result]
    print(sorted_sentences)
```

#### Code with multiprocessing:
```python
import multiprocessing as mp

def count_vowels(sentence, idx):
    """Get the number of vowels in a sentence"""
    counter = 0
    for ch in sentence:
        if ch in {'a', 'e', 'i', 'o', 'u'}:
            counter += 1
    return counter, idx

if __name__ == '__main__':

    pool = mp.Pool(mp.cpu_count())  # use all your CPUs
    result = []
    def update(*args):
        args = args[0]
        result.append(args)

    for idx, sent in enumerate(data):
        # the main line you should understand
        pool.apply_async(count_vowels, (sent, idx), callback=update)
    pool.close()
    pool.join()  # wait for all processes to end before executing next line

    sorted_result = sorted(result, key=lambda x: x[0])  # sort by first value
    sorted_sentences = [data[idx] for _, idx in sorted_result]
    print(sorted_sentences)
```

Note that we only needed to change a small portion of the code; the function itself is untouched. Also, because we want to know which vowel count refers 
to which sentence, we also save the index of the datapoint in the result. If you do synchronous multiprocessing you would not have to do this because you would get the outputs in the same order as the inputs.
However, this comes at a cost as it is a bit slower than asynchronous multiprocessing presented here.

The [`multiprocessing`](https://docs.python.org/3/library/multiprocessing.html) library in python offers many options to parallelize your code and you should definitely check it out if your project demands other type of parallelism. 

I strived to make this example generic enough so that people can apply it in their projects with small amount of tweaks.
 Some things that might vary for you which are easy to change are the number of arguments in your function, the number of output values, the stuff you're doing in the `update` callback function (called when processing for a datapoint is done) etc. 

 Finally, note that multiprocessing is worth it only if your data is actually large enough to see the difference; this toy example just demonstrates how to write the code for multiprocessing, assuming your own large dataset.




