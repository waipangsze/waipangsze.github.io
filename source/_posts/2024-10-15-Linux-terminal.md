---
layout: post
title: Linux | Terminal Command
categories: [Terminal]
tags: [HPC]
author: wpsze
date: 2024-10-15 16:00:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/WZjshPy.png
banner_img: 
---

Linux

```sh

# -m displays percentages
# -N displays line numbers
# -i ignores case when searching
# h Display the help interface
# Q quits less command
less -miN file # alias less='less -miN'

sh run.sh | tee output.txt
# If you want to print the error output to the screen and the file at the same time
sh run.sh 2>&1 | tee lsls.log

sh run.sh >/dev/null 2>&1 &

# -C : Print Around Lines
grep -irn -C 3 "xxx" file
grep -irn -C 3 "xxx" ./*

find ./ -name "xxx"
find ./ -type f | wc -l # count how many files
find ./ -size +1M       # large 1MB size

ldd xxx

rename IMG img IMG* # IMG001.jpg to img001.jpg
```

# Find & Delete

```sh
# Find files smaller than 100M: -print or -delete
find . -type f -name "*.nc" -size -100M -print
find . -type f -name "*.nc" -size -100M -delete

# Find files larger than 100M: -print or -delete
find . -type f -name "*.nc" -size +100M -print
find . -type f -name "*.nc" -size +100M -delete

# if your "find" does not support delete:
find . -type f ! -name '*.mp3' ! -name '*.mp4' -size +1M -exec rm {} \;
```

# For client node,

## How can I display the absolute path in bash prompt?

- <https://superuser.com/questions/202212/how-can-i-display-the-absolute-path-in-bash-prompt>

```
export PS1="\[\`if [[ \$? = "0" ]]; then echo '\e[32m\h\e[0m'; else echo '\e[31m\h\e[0m' ; fi\`:\`pwd\`\n\$ "
```

## ** Is there a directory history for bash?

- <https://superuser.com/questions/299694/is-there-a-directory-history-for-bash>

You can build your own `cd` command with `pushd`, `popd`, `dirs` , `dirs -v` builtin commands.

Usage

- `cd --` ( list current history )

- `cd -num` ( go to num directory )

- `cd -` ( go to previous directory )

```
function cd () 
{ 
    local hnum=16;
    local new_dir index dir cnt;
    if ! [ $# -eq 0 ]; then
        if [[ $# -eq 2 && $1 = "--" ]]; then
            shift;
        else
            if ! { 
                [ $# -eq 1 ] && [[ $1 =~ ^(-[0-9]{,2}|-|--|[^-].*)$ ]]
            }; then
                builtin cd "$@";
                return;
            fi;
        fi;
    fi;
    [ "$1" = "--" ] && { 
        dirs -v;
        return
    };
    new_dir=${1:-$HOME};
    if [[ "$new_dir" =~ ^-[0-9]{,2}$ ]]; then
        index=${new_dir:1};
        if [ -z "$index" ]; then
            new_dir=$OLDPWD;
        else
            new_dir=$(dirs -l +$index) || return;
        fi;
    fi;
    pushd -- "$new_dir" > /dev/null || return;
    popd -n +$hnum &> /dev/null || true;
    new_dir=$PWD cnt=1;
    while dir=$(dirs -l +$cnt 2> /dev/null); do
        if [ "$dir" = "$new_dir" ]; then
            popd -n +$cnt > /dev/null;
            continue;
        fi;
        let cnt++;
    done
}
```