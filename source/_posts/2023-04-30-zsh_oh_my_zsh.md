---
layout: post
title: "zsh & oh-my-zsh"
categories: [Linux]
tags: [shell script,HPC]
author: wpsze
---

# zsh & oh-my-zsh

The pre-installed shell of most Linux distributions is bash. But, zsh is more powerfull tool on the terminal.

## zsh
```sh
sudo apt install zsh

# change the default shell
sudo chsh -s $(which zsh) $(whoami)
```
Note: Because the Shell can set different default values ​​according to each account, so referring to the syntax of this article, a whoami technique is used to dynamically obtain the name of the currently logged-in account through the command to set it.

### Install oh-my-zsh
After installing zsh in the previous step, we can start to adjust the command line appearance settings we want, but the original zsh was too difficult to set up, so it didn’t receive much attention when it first appeared many years ago, until someone wrote a set The framework called oh-my-zsh helps everyone use zsh, and zsh became popular. Now almost all zsh useful tools support oh-my-zsh, so of course it is necessary to install this thing

Installation
```sh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```
After execution, if there is no error message, it means success, and you will find more folders of oh-my-zsh ~/.oh-my-zsh

However, oh-my-zsh has a lot of built-in themes, and there are many screenshots on its github wiki for reference
> https://github.com/ohmyzsh/ohmyzsh/wiki/Themes 

It is very simple to switch the built-in theme, directly modify your ~/.zshrc, and change the original ZSH_THEME="robbyrussell" to what you want

### Edit .zshrc
```sh
# some more ls aliases
alias ll='ls -alF'
alias la='ls -A'
alias l='ls -CF'

# export TERM='xterm-256color'
# # To determine the prompt format, use the $PS1 variable.
# PS1="%{$fg[red]%}%n%{$reset_color%}@%{$fg[green]%}%m %{$fg[yellow]%}%(5~|%-1~/.../%3~|%4~) %{$reset_color%}%% "
```

> ZSH_THEME=”agnoster” # Try changing robbyrussell to agnoster
> After any zsh settings are modified, the following commands must be executed to take effect

```sh
exec $SHELL
```

### Conda problem 
```sh
CommandNotFoundError: Your shell has not been properly configured to use 'conda activate'.
To initialize your shell, run

    $ conda init <SHELL_NAME>

Currently supported shells are:
  - bash
  - fish
  - tcsh
  - xonsh
  - zsh
  - powershell

See 'conda init --help' for more information and options.
IMPORTANT: You may need to close and restart your shell after running 'conda init'.
```
do
```sh
conda init zsh
```

### zsh-autosuggestions
```sh
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```
In .zshrc, 
> plugins=(zsh-autosuggestions)
and then,
source ~/.zshrc

Usage
> If you think it is correct, you can press CTRL+F to accept it


### quick jump: cd -

As mentioned earlier, switching between different paths is a headache in command line work. In addition to the abbreviation completion mentioned above, is there a faster way for me to immediately switch to a certain path I recently jumped to? path? Of course there is the **"cd -"** command

After inputting cd followed by a minus sign, press tab once to list the recent paths visited after this login, and then enter the number according to the prompt below and press Enter to pass, for example, input:** $ cd -5 < Press Enter>** to jump to the ~/software/libclang-python3 path. Of course, instead of entering numbers, you can press tab again to enter the selection mode, use the up and down keys or ctrl+n/p to select, press Enter to confirm, and ctrl+g to return.

### jump: d

Zsh's directory jump is more intelligent, you don't need to enter cd, just enter the path directly. .. means back one directory, ../../ means back two levels, and so on. (The function of ... is the same as that of ../../) Enter d to list all directories visited by the current session, and then press the number prompted to enter the corresponding directory.
```sh
~ > d
0 ~ 
1 ~/Applications 
2 ~/.proxychains 
3 /opt/homebrew-cask/Caskroom
~ > 1
~/Applications
```

## zsh mkdir/source/...: not found
Using bash for all script,
> #!/bin/bash 

## Building zsh without root

> No terminal handling library found

Have to install **ncurses**
```sh
wget https://invisible-island.net/ncurses/#download_ncurses
ex
```
output is 
```sh
=== output ===
** Configuration summary for NCURSES 6.3 20211021:

       extended funcs: yes
       xterm terminfo: xterm-new

        bin directory: /home/wpsze/zsh/ncurses/bin
        lib directory: /home/wpsze/zsh/ncurses/lib
    include directory: /home/wpsze/zsh/ncurses/include/ncurses
        man directory: /home/wpsze/zsh/ncurses/share/man
   terminfo directory: /home/wpsze/zsh/ncurses/share/terminfo

** Include-directory is not in a standard location
=====
```

```sh
wget -O zsh.tar.xz https://sourceforge.net/projects/zsh/files/latest/download --no-check-certificate
tar -xvf zsh.tar
cd zsh

INSTALLATION_PATH='/home/wpsze/zsh/ncurses/'
export PATH=$INSTALLATION_PATH/bin/:$PATH
export LD_LIBRARY_PATH=$INSTALLATION_PATH/lib:$LD_LIBRARY_PATH
export CFLAGS=-I$INSTALLATION_PATH/include

export CPPFLAGS="-I$INSTALLATION_PATH/include" 
export LDFLAGS="-L$INSTALLATION_PATH/lib"

./configure --prefix=$HOME/zsh
make 
make install
```

Error
```sh
/usr/bin/ld: /home/wpsze/zsh/ncurses//lib/libncurses.a(comp_captab.o): relocation R_X86_64_32 against `.rodata' can not be used when making a shared object; recompile with -fPIC
/usr/bin/ld: final link failed: Nonrepresentable section on output
collect2: error: ld returned 1 exit status
```
and 
```sh
zsh configuration
-----------------
zsh version               : 5.9
host operating system     : x86_64-pc-linux-gnu
source code location      : .
compiler                  : gcc -std=gnu11
preprocessor flags        : -I/home/wpsze/zsh/ncurses//include
executable compiler flags : -I/home/wpsze/zsh/ncurses//include -fPIC
module compiler flags     : -I/home/wpsze/zsh/ncurses//include -fPIC -fPIC
executable linker flags   : -L/home/wpsze/zsh/ncurses//lib  -rdynamic
module linker flags       : -L/home/wpsze/zsh/ncurses//lib  -shared
library flags             : -ldl -lncurses -lrt -lm  -lc
installation basename     : zsh
binary install path       : /home/wpsze/zsh/bin
man page install path     : /home/wpsze/zsh/share/man
info install path         : /home/wpsze/zsh/share/info
functions install path    : /home/wpsze/zsh/share/zsh/5.9/functions
See config.modules for installed modules and functions.
```
> gcc -std=gnu11 -L/home/wpsze/zsh/ncurses//lib  -rdynamic -o zsh main.o  `cat stamp-modobjs`   -ldl -lncurses -lrt -lm  -lc

> recompile with -fPIC

Not solved

```sh
 6903 LIBS="$ac_func_search_save_LIBS -fPIC"
```






