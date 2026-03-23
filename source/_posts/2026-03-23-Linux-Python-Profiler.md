---
layout: post
title: Linux | Python Profiler
categories: [Linux]
tags: [python, Profiler, cProfile, snakeviz, memory_profiler]
author: wpsze
date: 2026-03-23 06:00:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/Krfenm2.png
banner_img: https://i.imgur.com/Krfenm2.png
---

Whether you're trying to figure out why your script is crawling or you just want to optimize a specific function, Python has some built-in heavy hitters to help you find those bottlenecks.

Here is the breakdown of how to use the standard tools effectively.

---

## 1. The "Quick & Dirty" Way: `cProfile`

`cProfile` is the recommended built-in profiler for most users. It provides deterministic profiling of programs, meaning it tracks every function call, how many times it was called, and how long it took.

### From the Command Line
This is the easiest way to profile a script without changing a single line of code:

```bash
python -m cProfile -s cumulative my_script.py
```
* `-s cumulative`: Sorts the output by the cumulative time spent in each function (the most useful metric for finding bottlenecks).

### Inside Your Code
If you only want to profile a specific block of code:

```python
import cProfile

def my_slow_function():
    # Your logic here
    pass

cProfile.run('my_slow_function()')
```

---

## 2. Understanding the Output
When you run a profile, you’ll see a table with several columns. Here is what they mean:

| Column | Meaning |
| :--- | :--- |
| **ncalls** | Number of times the function was called. |
| **tottime** | Total time spent in the function (excluding time in sub-functions). |
| **percall** | `tottime` divided by `ncalls`. |
| **cumtime** | Total time spent in this and all sub-functions (from invocation to exit). |
| **filename:lineno** | The location of the function in your code. |

---

## 3. Visualizing the Data (Highly Recommended)
Reading text tables can get exhausting. You can export the profile data and view it as a "Flame Graph" or a call tree.

1.  **Export to a file:**
    ```bash
    python -m cProfile -o output.prof my_script.py
    ```
2.  **Use a visualizer:**
    * **SnakeViz:** A web-based viewer. Install it via `pip install snakeviz`, then run `snakeviz output.prof`.
    * **PyCharm:** If you use the Professional edition, it has a built-in "Profile" button that generates a beautiful call graph.



---

## 4. Deep Dive: Line-by-Line Profiling
Sometimes `cProfile` tells you a function is slow, but that function is 100 lines long. To see exactly which line is the culprit, use `line_profiler`.

1.  **Install it:** `pip install line_profiler`
2.  **Decorate your function:** Add `@profile` above the function you want to inspect (you don't need to import anything).
3.  **Run it:**
    ```bash
    kernprof -l -v my_script.py
    ```

---

## 5. Checking Memory Usage
If your script isn't slow but is crashing because it eats all your RAM, you need `memory_profiler`.

1.  **Install it:** `pip install memory_profiler`
2.  **Decorate:** Use `@profile` just like the line profiler.
3.  **Run it:**
    ```bash
    python -m memory_profiler my_script.py
    ```

> **Pro Tip:** Profiling adds overhead. Your code will run slower while being profiled, so don't use these tools in a production environment unless you're using a "sampling" profiler like **Py-Spy**.

## Example

```python
#!/bin/python
# -*- coding: utf-8 -*-

def find_primes_up_to(limit):
    if limit < 2:
        return []
    
    # Create boolean array - True means "maybe prime"
    is_prime = [True] * (limit + 1)
    is_prime[0] = is_prime[1] = False
    
    for i in range(2, int(limit ** 0.5) + 1):
        if is_prime[i]:
            # Mark all multiples of i as not prime
            for j in range(i*i, limit+1, i):
                is_prime[j] = False
    
    # Collect all prime numbers
    primes = [i for i in range(2, limit+1) if is_prime[i]]
    return primes


# Examples
print(find_primes_up_to(30))
# [2, 3, 5, 7, 11, 13, 17, 19, 23, 29]

print(len(find_primes_up_to(1000000)), "primes below 1 million")
```

### cProfile

-  `$ python -m cProfile -s cumulative primes.py`

```log
[2, 3, 5, 7, 11, 13, 17, 19, 23, 29]
78498 primes below 1 million
         10 function calls in 0.148 seconds

   Ordered by: cumulative time

   ncalls  tottime  percall  cumtime  percall filename:lineno(function)
        1    0.000    0.000    0.148    0.148 {built-in method builtins.exec}
        1    0.003    0.003    0.148    0.148 primes.py:13(<module>)
        2    0.098    0.049    0.145    0.073 primes.py:13(find_primes_up_to)
        2    0.047    0.023    0.047    0.023 primes.py:28(<listcomp>)
        2    0.000    0.000    0.000    0.000 {built-in method builtins.print}
        1    0.000    0.000    0.000    0.000 {built-in method builtins.len}
        1    0.000    0.000    0.000    0.000 {method 'disable' of '_lsprof.Profiler' objects}
```

- `$ python -m cProfile -o output.prof primes.py`
  - ` $ snakeviz output.prof`

{% gi 3 1-2 %}
![](https://i.imgur.com/V9Iz8qB.png)
![](https://i.imgur.com/Krfenm2.png)
![](https://i.imgur.com/xo3PdST.png)
{% endgi %}

### memory_profiler

```python
@profile
def find_primes_up_to(limit):
    ......
```

- `Memory usage`: Memory usage status
- `Increment`: Memory added after executing this line of code

{% note primary %}
Your code will run **slower** while being profiled.
{% endnote %}


```log
[2, 3, 5, 7, 11, 13, 17, 19, 23, 29]
78498 primes below 1 million
Filename: primes.py

Line #    Mem usage    Increment  Occurrences   Line Contents
=============================================================
    13   37.852 MiB   37.852 MiB           2   @profile
    14                                         def find_primes_up_to(limit):
    15   37.852 MiB    0.000 MiB           2       if limit < 2:
    16                                                 return []
    17                                             
    18                                             # Create boolean array - True means "maybe prime"
    19   45.375 MiB    7.523 MiB           2       is_prime = [True] * (limit + 1)
    20   45.375 MiB    0.000 MiB           2       is_prime[0] = is_prime[1] = False
    21                                             
    22   51.125 MiB    0.000 MiB        1005       for i in range(2, int(limit ** 0.5) + 1):
    23   51.125 MiB    0.000 MiB        1003           if is_prime[i]:
    24                                                     # Mark all multiples of i as not prime
    25   51.125 MiB    2.145 MiB     2122243               for j in range(i*i, limit+1, i):
    26   51.125 MiB    3.605 MiB     2122072                   is_prime[j] = False
    27                                             
    28                                             # Collect all prime numbers
    29   53.375 MiB    2.250 MiB     1000034       primes = [i for i in range(2, limit+1) if is_prime[i]]
    30   53.375 MiB    0.000 MiB           2       return primes
```