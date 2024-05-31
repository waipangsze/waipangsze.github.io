---
layout: article
title: VSCode with Markdown editor
categories: [Linux]
tags: [Markdown, VSCode, editor]
author: wpsze
---

Working with [Markdown files in Visual Studio Code](https://code.visualstudio.com/docs/languages/markdown) is simple, straightforward, and fun. Besides VS Code's basic editing, there are a several Markdown-specific features that help you be more productive.

# Markdown preview

You can also right-click on the editor Tab and select Open Preview (Ctrl+Shift+V) or use the Command Palette (Ctrl+Shift+P) to run the Markdown: **Open Preview to the Side** command (Ctrl+K V).

# Outline view

The Outline view is a separate section in the bottom of the File Explorer. When expanded, it shows the symbol tree of the currently active editor. For Markdown files, the symbol tree is the Markdown file's header hierarchy.

# Extension

- markdownlint
- Markdown All in One
- Markdown Preview Github Styling
- Markdown Preview Enhanced
- Markdown Table Prettifier

# Markdown syntax 

## Bold/Italics

- I just love **bold text**./I just love *bold text*.
- I just love __bold text__./I just love _bold text_.
- Love**is**bold./Love*is*bold

## Blockquotes

> It is Markdown.

> It is also Markdown.
>
> same here.
> > indent
> > > indent

> #### The quarterly results look great!
>
> - Revenue was off the chart.
> - Profits were higher than ever.
>
>  *Everything* is going according to **plan**.

## Lists

1. First item
2. Second item
3. Third item
    1. Indented item
    2. Indented item
4. Fourth item

Or,

- First item
- Second item
- Third item
    - Indented item
    - Indented item
- Fourth item

Or,

1. First item
2. Second item
3. Third item
    - Indented item
    - Indented item
4. Fourth item

## Code and Highlighting

```sh
#!/bin/sh
echo "Hello world"
```

```python
import numpy as np
```

```cpp
#include <iostream>
using namespace std;

int main() {
  cout << "Hello World!";
  return 0;
}
```

```c
#include <stdio.h>
int main() {
   // printf() displays the string inside quotation
   printf("Hello, World!");
   return 0;
}
```

## Table

| Syntax | Description |
| ----------- | ----------- |
| Header | Title |
| Paragraph | Text |

## Links

To create a link, enclose the link text in brackets (e.g., [Duck Duck Go]) and then follow it immediately with the URL in parentheses (e.g., (https://duckduckgo.com)).

It is [GNU Compiler Collection (GCC)](https://en.wikipedia.org/wiki/GNU_Compiler_Collection).

## URLs and Email Addresses

<https://www.markdownguide.org>\
<waipangsze@gmail.com>

## Task List

- [x] Write the press release
- [ ] Update the website
- [ ] Contact the media

## Image

1. Open the file containing the Linux.

    ![Tux, the Linux mascot](https://upload.wikimedia.org/wikipedia/commons/thumb/3/35/Tux.svg/150px-Tux.svg.png)

2. Close the file.

## Math block

Inline math: $x^2$

$$
\displaystyle
\left( \sum_{k=1}^n a_k b_k \right)^2
\leq
\left( \sum_{k=1}^n a_k^2 \right)
\left( \sum_{k=1}^n b_k^2 \right)
$$
