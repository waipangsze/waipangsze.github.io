---
layout: post
title: "Bash & Oh-My-Bash"
categories: [Linux]
tags: [shell script, HPC, bashrc]
author: wpsze
index_img: https://i.imgur.com/WZjshPy.png
banner_img: https://i.imgur.com/WZjshPy.png
---

# Bash & Oh-My-Bash

Ref:

1. [配置安装oh-my-bash](https://www.cnblogs.com/nbtech/p/oh-my-bash.html)
2. <https://github.com/ohmybash/oh-my-bash>
3. [plugins](https://github.com/ohmybash/oh-my-bash/tree/master/plugins)
4. [Themes](https://github.com/ohmybash/oh-my-bash/wiki/Themes)

*Oh My Bash* works best on macOS and Linux.

> Unix-like operating system (macOS or Linux)\
> curl or wget should be installed\
> git should be installed

## Check $SHELL used

```sh
cat /etc/shells
## /etc/shells: valid login shells
# /bin/sh
# /bin/bash
# /bin/rbash
# /bin/dash
# /bin/csh
# /bin/zsh
# /usr/bin/zsh

echo $SHELL
# /bin/bash
```

To change your shell.

```sh
chsh -s /bin/bash ## change to bash ##
# or
exec bash # zsh
```

## Installation

On the HOME dir, like /home/wpsze/

```sh
git clone https://github.com/ohmybash/oh-my-bash.git
mv oh-my-bash-master .oh-my-bash
cd .oh-my-bash/
chmod +x oh-my-bash.sh
```

Backup our .bashrc first,
```sh
cp ~/.bashrc ~/.bashrc.bak
```

copy the oh-my-bash to .bashrc
```sh
cp ~/.oh-my-bash/templates/bashrc.osh-template ~/.bashrc
```

source it,
```sh
source ~/.bashrc
```

The installation and use of oh-my-bash is very simple, and the use of oh-my-bash is similar to that of oh-my-zsh. There are functions such as auto-completion (ignoring case), history, and quick cd.

## Theme
```sh
ls .oh-my-bash/themes/
vim ~/.bashrc
#
# OSH_THEME="font"
# or
OSH_THEME="simple"
```

and change theme like /home/wpsze/.oh-my-bash/themes/font/font.theme.sh
```sh
CLOCK_THEME_PROMPT_PREFIX='['
CLOCK_THEME_PROMPT_SUFFIX=']'

local hostname="${_omb_prompt_bold_green}\u@\h"
```

## absolute path
How can I display the absolute path in bash prompt?
<https://superuser.com/questions/202212/how-can-i-display-the-absolute-path-in-bash-prompt>

```sh
# PS1='\u@\h:\w\$ '
# Replace the "\w" with "\$PWD"
# PS1=[\u@\h ]\$PWD$
# PS1="$(clock_prompt)$python_venv${hostname}:${_omb_prompt_bold_teal}\${PWD} $(scm_prompt_char_info)${ret_status}$ ${_omb_prompt_normal}"
# or
PS1="$(clock_prompt)$python_venv${hostname}:${_omb_prompt_bold_teal}\${PWD} $(scm_prompt_char_info)${ret_status} \n $ ${_omb_prompt_normal}"
```

The key here is **\w** gives the full path while **\W** gives only the directory.

## conda initialize 
Add the following into .bashrc

```sh
# >>> conda initialize >>>
# !! Contents within this block are managed by 'conda init' !!
__conda_setup="$('/home/wpsze/anaconda3/bin/conda' 'shell.bash' 'hook' 2> /dev/null)"
if [ $? -eq 0 ]; then
    eval "$__conda_setup"
else
    if [ -f "/home/wpsze/anaconda3/etc/profile.d/conda.sh" ]; then
        . "/home/wpsze/anaconda3/etc/profile.d/conda.sh"
    else
        export PATH="/home/wpsze/anaconda3/bin:$PATH"
    fi
fi
unset __conda_setup
# <<< conda initialize <<<
```

## History of most recent directories
Looks like a history of most recent directories you've been in. **which d** identifies it as an alias to **dirs** shell builtin, which prints the contents of the directory stack. Just tried it and number keys allow to move to respective directory.

**dirs** command shell builtin is used to display the list of currently remembered directories. By default, it includes the directory you are currently in. A directory can get into the list via pushd command followed by the dir name and can be removed via popd command.

```sh
$ dirs -v | head -n 10
# 0 ~
# 1 ~/.oh-my-bash/themes/font
# 2 ~/.oh-my-bash/themes
# ...

## Select number above will jump to that direcotry
$ 1
.oh-my-bash/themes/font $ 
```

### add it to .bashrc
refer to [zsh directories.zsh](https://github.com/ohmyzsh/ohmyzsh/blob/master/lib/directories.zsh)
here dl() function name can be defined by yourself. 
```sh
function dl() {
    dirs -v | head -n 10
}
```
Remark:
In .oh-my-bash/lib/directories.sh
New package may be false on this function as
```sh
OMB_DIRECTORIES_CD_USE_PUSHD=true #false
```
Set as **ture**

#### If it is client's node and don't want to modify the terminal config, then just add a function on .bashrc,

- <https://waipangsze.github.io/2024/10/15/Linux-terminal/>

## autojump - a faster way to navigate your filesystem
[Git autojump](https://github.com/wting/autojump)


## ble.sh ― Bash Line Editor
### autosuggestions

[Git ble.sh](https://github.com/akinomyoga/ble.sh)

```sh
# TRIAL without installation

wget -O - https://github.com/akinomyoga/ble.sh/releases/download/nightly/ble-nightly.tar.xz | tar xJf -
source ble-nightly/ble.sh

# Quick INSTALL to BASHRC (If this doesn't work, please follow Sec 1.3)

wget -O - https://github.com/akinomyoga/ble.sh/releases/download/nightly/ble-nightly.tar.xz | tar xJf -
mkdir -p ~/.local/share/blesh
cp -Rf ble-nightly/* ~/.local/share/blesh/
rm -rf ble-nightly
echo 'source ~/.local/share/blesh/ble.sh' >> ~/.bashrc
```

## Whole .bashrc
```sh
# Enable the subsequent settings only in interactive sessions
case $- in
  *i*) ;;
    *) return;;
esac

# Path to your oh-my-bash installation.
export OSH=~/.oh-my-bash

# Set name of the theme to load. Optionally, if you set this to "random"
# it'll load a random theme each time that oh-my-bash is loaded.
OSH_THEME="font"

# Uncomment the following line to use case-sensitive completion.
# OMB_CASE_SENSITIVE="true"

# Uncomment the following line to use hyphen-insensitive completion. Case
# sensitive completion must be off. _ and - will be interchangeable.
# OMB_HYPHEN_SENSITIVE="false"

# Uncomment the following line to disable bi-weekly auto-update checks.
# DISABLE_AUTO_UPDATE="true"

# Uncomment the following line to change how often to auto-update (in days).
# export UPDATE_OSH_DAYS=13

# Uncomment the following line to disable colors in ls.
# DISABLE_LS_COLORS="true"

# Uncomment the following line to disable auto-setting terminal title.
# DISABLE_AUTO_TITLE="true"

# Uncomment the following line to enable command auto-correction.
# ENABLE_CORRECTION="true"

# Uncomment the following line to display red dots whilst waiting for completion.
# COMPLETION_WAITING_DOTS="true"

# Uncomment the following line if you want to disable marking untracked files
# under VCS as dirty. This makes repository status check for large repositories
# much, much faster.
# DISABLE_UNTRACKED_FILES_DIRTY="true"

# Uncomment the following line if you want to change the command execution time
# stamp shown in the history command output.  One of the following values can
# be used to specify the timestamp format.
# * 'mm/dd/yyyy'     # mm/dd/yyyy + time
# * 'dd.mm.yyyy'     # dd.mm.yyyy + time
# * 'yyyy-mm-dd'     # yyyy-mm-dd + time
# * '[mm/dd/yyyy]'   # [mm/dd/yyyy] + [time] with colors
# * '[dd.mm.yyyy]'   # [dd.mm.yyyy] + [time] with colors
# * '[yyyy-mm-dd]'   # [yyyy-mm-dd] + [time] with colors
# If not set, the default value is 'yyyy-mm-dd'.
# HIST_STAMPS='yyyy-mm-dd'

# Uncomment the following line if you do not want OMB to overwrite the existing
# aliases by the default OMB aliases defined in lib/*.sh
# OMB_DEFAULT_ALIASES="check"

# Would you like to use another custom folder than $OSH/custom?
# OSH_CUSTOM=/path/to/new-custom-folder

# To disable the uses of "sudo" by oh-my-bash, please set "false" to
# this variable.  The default behavior for the empty value is "true".
OMB_USE_SUDO=true

# To enable/disable display of Python virtualenv and condaenv
# OMB_PROMPT_SHOW_PYTHON_VENV=true  # enable
# OMB_PROMPT_SHOW_PYTHON_VENV=false # disable

# Which completions would you like to load? (completions can be found in ~/.oh-my-bash/completions/*)
# Custom completions may be added to ~/.oh-my-bash/custom/completions/
# Example format: completions=(ssh git bundler gem pip pip3)
# Add wisely, as too many completions slow down shell startup.
completions=(
  git
  ssh
)

# Which aliases would you like to load? (aliases can be found in ~/.oh-my-bash/aliases/*)
# Custom aliases may be added to ~/.oh-my-bash/custom/aliases/
# Example format: aliases=(vagrant composer git-avh)
# Add wisely, as too many aliases slow down shell startup.
#aliases=(
#  general
#)

# Which plugins would you like to load? (plugins can be found in ~/.oh-my-bash/plugins/*)
# Custom plugins may be added to ~/.oh-my-bash/custom/plugins/
# Example format: plugins=(rails git textmate ruby lighthouse)
# Add wisely, as too many plugins slow down shell startup.
plugins=(
  git
  bashmarks
)

# Which plugins would you like to conditionally load? (plugins can be found in ~/.oh-my-bash/plugins/*)
# Custom plugins may be added to ~/.oh-my-bash/custom/plugins/
# Example format:
#  if [ "$DISPLAY" ] || [ "$SSH" ]; then
#      plugins+=(tmux-autoattach)
#  fi

source "$OSH"/oh-my-bash.sh

# User configuration
# export MANPATH="/usr/local/man:$MANPATH"

# You may need to manually set your language environment
# export LANG=en_US.UTF-8

# Preferred editor for local and remote sessions
# if [[ -n $SSH_CONNECTION ]]; then
#   export EDITOR='vim'
# else
#   export EDITOR='mvim'
# fi

# Compilation flags
# export ARCHFLAGS="-arch x86_64"

# ssh
# export SSH_KEY_PATH="~/.ssh/rsa_id"

# Set personal aliases, overriding those provided by oh-my-bash libs,
# plugins, and themes. Aliases can be placed here, though oh-my-bash
# users are encouraged to define aliases within the OSH_CUSTOM folder.
# For a full list of active aliases, run `alias`.
#
# Example aliases
# alias bashconfig="mate ~/.bashrc"
# alias ohmybash="mate ~/.oh-my-bash"

## >>> conda initialize >>>
# !! Contents within this block are managed by 'conda init' !!
__conda_setup="$('/home/wpsze/anaconda3/bin/conda' 'shell.bash' 'hook' 2> /dev/null)"
if [ $? -eq 0 ]; then
    eval "$__conda_setup"
else
    if [ -f "/home/wpsze/anaconda3/etc/profile.d/conda.sh" ]; then
        . "/home/wpsze/anaconda3/etc/profile.d/conda.sh"
    else
        export PATH="/home/wpsze/anaconda3/bin:$PATH"
    fi
fi
unset __conda_setup
# <<< conda initialize <<<

## History of most recent directories and jump 
function dl(){
    dirs -v | head -n 10
}

##
# some more ls aliases
alias ll='ls -alF'
alias la='ls -A'
alias l='ls -CF'

# statistic bash
function bash_stats(){
  fc -l 1 \
    | awk '{ CMD[$2]++; count++; } END { for (a in CMD) print CMD[a] " " CMD[a]*100/count "% " a }' \
    | grep -v "./" | sort -nr | head -n 20 | column -c3 -s " " -t | nl
}


```










