---
layout: post
title: Linux | conda | Make package/environment creation much faster
categories: [Linux]
tags: [conda, Micromamba]
author: wpsze
date: 2026-04-20 06:30:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/0SF9NEC.png
banner_img: https://i.imgur.com/0SF9NEC.png
---

- [Conda/Micromamba](https://waipangsze.github.io/2024/03/02/Conda_Micromamba/)

---

How to **speed up the micromamba install process** (both installing micromamba itself and, more importantly, creating environments + installing packages with it).

# 1. Install micromamba as quickly as possible
The fastest ways to get micromamba on your system:

- **Recommended (one-liner, works on Linux/macOS/Git Bash on Windows):**
  ```bash
  "${SHELL}" <(curl -L micro.mamba.pm/install.sh)
  ```
  This is the official quickest method.

- **macOS with Homebrew** (very fast if you already have brew):
  ```bash
  brew install micromamba
  ```

- **Manual download (fastest for scripts/CI, no shell init needed initially):**
  ```bash
  # Linux x86_64 example
  curl -Ls https://micro.mamba.pm/api/micromamba/linux-64/latest | tar -xvj bin/micromamba
  ./bin/micromamba shell init -s bash -r ~/micromamba   # or your preferred prefix
  source ~/.bashrc
  ```

After installation, run `micromamba self-update` to get the latest version (newer releases often include solver and download improvements).

Set a custom root prefix early if you want (e.g., on a fast SSD or large disk):
```bash
export MAMBA_ROOT_PREFIX=~/micromamba   # or /fast/ssd/micromamba
```

# 2. Make package/environment creation much faster
Micromamba (and mamba) is already dramatically faster than classic conda thanks to its C++ solver (libsolv) and parallel operations. Here are the key optimizations:

- **Use only `conda-forge`** (biggest single speedup):
  ```bash
  micromamba config append channels conda-forge
  micromamba config append channels nodefaults   # disable defaults channel
  micromamba config set channel_priority strict
  ```
  Mixing channels (especially defaults + conda-forge) slows down the solver a lot.

- **Create environments from a YAML file** (instead of many separate `install` commands):
  ```bash
  micromamba create -n myenv -f environment.yml -y
  ```
  Or inline:
  ```bash
  micromamba create -n myenv python=3.12 numpy pandas -c conda-forge -y
  ```

- **Enable parallel downloads and fast settings**:
  Micromamba already uses multi-threading for downloads and solving by default. You can tune it with these config options (add to `~/.condarc` or `~/.mambarc`):
  ```yaml
  # In ~/.condarc
  solver: libmamba          # usually default
  channel_priority: strict
  # Optional performance tweaks:
  # fetch_threads: 0        # 0 = auto (uses all cores; try higher/lower if network is bottleneck)
  # repodata_threads: 0     # auto for repodata fetching
  ```

- **Leverage the shared package cache**:
  Micromamba caches downloaded packages and repodata (channel indexes) in `$MAMBA_ROOT_PREFIX/pkgs/`.  
  - First install is slower (downloads everything).  
  - Subsequent installs are **much faster** because it reuses hard links from the cache.  
  Keep the same `MAMBA_ROOT_PREFIX` across projects for maximum reuse.

- **Offline / pre-download mode** (great for repeated setups or air-gapped machines):
  ```bash
  # On a machine with internet
  micromamba create -n myenv --download-only -f environment.yml

  # Then copy the pkgs cache or use --offline on target machine
  micromamba create -n myenv --offline -f environment.yml
  ```

- **Other practical tips**:
  - Avoid running installs while already inside an activated environment (can cause repeated activation overhead in some cases).
  - For CI/Docker: Use the official `mambaorg/micromamba` Docker image or the `setup-micromamba` GitHub Action — it installs in ~1 second.
  - On slow networks: The bottleneck is often downloading repodata/packages. Use a local mirror or proxy if possible (advanced).
  - Clean up occasionally: `micromamba clean --all -y` to free space (but keep cache for speed).

