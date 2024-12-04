---
layout: post
title: Bash | Measure the execution time
categories: [Bash]
tags: [Linux, Bash]
author: wpsze
date: 2024-12-04 14:50:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/HkcQn5a.jpeg
banner_img: https://i.imgur.com/HkcQn5a.jpeg
---

# Measure the execution time

## `time`

The `time` that you are using is the bash shell built in `time` which the only option is `-p` 

The command "time" report a execution time information

```console
time sleep 1

Output:
real 0m1.001s
user 0m0.000s
sys 0m0.001s
```

Description:

- `Real` - time from start to finish.
- `User` - the amount of CPU time spent in use-mode. It is actual CPU time used in executing the process.
- `Sys` - the amount of CPU time spent in kernel.

## `date +%s`

You can measure execution time by subtraction start date and end date:

```sh
# time elapsed
start_time="$(date -u +%s)"

commands...

echo "====================="
end_time="$(date -u +%s)"
elapsed="$(($end_time-$start_time))"
echo "Total of $elapsed seconds elapsed for process"
time_mins="$(($elapsed /60))"
echo "Total of $time_mins minutes elapsed for process"
time_hours="$(($elapsed /60/60))"
echo "Total of $time_hours hours elapsed for process" 
echo "====================="
```

# `> log 2>&1 &`

```console
$ time ./Allrun > output_file.txt 2>&1 & 
```

# `tee`

It basically breaks the output of a program so that it can be **both displayed and saved in a file**. It does both the tasks simultaneously, copies the result into the specified files or variables and also display the result.

```console
$ time ./Allrun | tee output_file.txt 
```

![](https://media.geeksforgeeks.org/wp-content/uploads/tee-command-linux.jpg){width=600}

# References
1. [tee command in Linux with examples](https://www.geeksforgeeks.org/tee-command-linux-example/)