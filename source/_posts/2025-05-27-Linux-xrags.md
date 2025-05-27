---
layout: post
title: Linux | xrags
categories: [Linux]
tags: [Linux, HPC, Terminal, shell script, xrags]
author: wpsze
date: 2025-05-27 15:57:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/WZjshPy.png
banner_img: https://i.imgur.com/WZjshPy.png
---

- **xargs（eXtended ARGuments）**是給指令傳遞參數的一個篩選器，也是組合多個指令的一個工具。
- xargs 可以將管道或標準輸入（stdin）資料轉換成命令列參數，也能夠從檔案的輸出中讀取資料。
- xargs 也可以將單行或多行文字輸入轉換為其他格式，例如多行變單行，單行變多行。
- xargs 預設的指令是 echo，這表示透過管道傳遞給 xargs 的輸入將會包含換行和空白，不過透過 xargs 的處理，換行和空白將會被空格取代。
- xargs 是一個強而有力的指令，它能夠捕捉一個指令的輸出，然後傳遞給另一個指令。

# xargs

How to use `xargs` with the options `-t`, `-P`, and `-I`:

## Options Explained

- **`-t`**: Print each command before executing it.
- **`-P <num>`**: Run up to `<num>` processes in parallel.
- **`-I{}`**: Replace occurrences of `{}` in the command with the input items.

## Example Usage

1. **Create a File**: Make a file named `items.txt` with the following contents:
   ```
   file1.txt
   file2.txt
   file3.txt
   ```

2. **Run xargs**:
   ```bash
   cat files.txt | xargs -t -P 2 -I{} echo Processing {}
   cat files.txt | xargs -t -P 3 -I{} sh -c 'cd /path/to/dir && echo Processing {}'
   ```

## Breakdown of the Command

- **`cat items.txt`**: Reads the list of items from the file.
- **`xargs -t -P 2 -I{}`**:
  - `-t`: Prints the command before execution.
  - `-P 2`: Executes up to 2 commands in parallel.
  - `-I {}`: Replaces `{}` with each line from the input.
- **`echo Processing {}`**: The command executed, which will print "Processing itemX" for each item.

## Notes

- Adjust the number in `-P` based on your system's capabilities.
- You can replace `echo Processing {}` with any command you want to execute for each item.

# Example: wrf.exe

To use the command `xargs -t -P 2 -I{} mpirun -np 2 -wdir {} ./wrf.exe >& log.log`, follow these steps:

## Explanation of the Command

- **`xargs -t -P 2 -I{}`**: 
  - `-t`: Print each command before executing it.
  - `-P 2`: Run up to 2 processes in parallel.
  - `-I{}`: Replace occurrences of `{}` with input items.

- **`mpirun -np 2 -wdir {}`**: 
  - `-np 2`: Run 2 MPI processes.
  - `-wdir {}`: Set the working directory to the value provided by `{}`.

- **`./wrf.exe`**: The executable you want to run.

- **`>& log.log`**: Redirect both standard output and standard error to `log.log`.

## Example Usage

1. **Create a File**: Prepare a file named `dirs.txt` containing directory paths:
   ```
   /path/to/dir1
   /path/to/dir2
   ```

2. **Run the Command**:
   ```bash
   cat dirs.txt | xargs -t -P 2 -I{} mpirun -np 2 -wdir {} ./wrf.exe >& log.log
   ```

## Breakdown of the Command Execution

- **`cat dirs.txt`**: Reads the list of directories.
- **`xargs -t -P 2 -I{}`**: For each directory in `dirs.txt`, it will:
  - Print the command being executed.
  - Run up to 2 instances of the `mpirun` command in parallel.
- **`mpirun -np 2 -wdir {}`**: Executes the WRF model in the specified directory with 2 MPI processes.
- **`./wrf.exe`**: The executable to run.
- **`>& log.log`**: Captures all output and errors into `log.log`.

## Notes

- Ensure that each directory in `dirs.txt` contains the necessary files for `wrf.exe`.
- Adjust the parameters as needed based on your system and requirements.

# References

1. [Linux xargs 命令](https://www.runoob.com/linux/linux-comm-xargs.html)
2. [xargs 命令教程](https://www.ruanyifeng.com/blog/2019/08/xargs-tutorial.html)