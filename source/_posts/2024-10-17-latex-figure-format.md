---
layout: post
title: LaTex | Figures
categories: [LaTex]
tags: []
author: wpsze
date: 2024-10-17 10:00:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://upload.wikimedia.org/wikipedia/commons/thumb/4/45/LaTeX_project_logo_bird.svg/474px-LaTeX_project_logo_bird.svg.png
banner_img: https://upload.wikimedia.org/wikipedia/commons/thumb/4/45/LaTeX_project_logo_bird.svg/474px-LaTeX_project_logo_bird.svg.png
---

# Figures/Graphics

## Vector files.
Mathematical formulas establish points on a grid to build vector images. Versatility is key for vector files because they can adjust in size countless times without losing resolution. That’s great for images that need to be regularly resized. Learn more about vector files: **SVG files, STL files, EPS files.**

## Raster files.
A raster image file is comprised of a fixed number of color pixels. This means if the file is resized, its resolution could be compromised. Most images found online and in print are raster image files, which can be opened with many programs. Read more about raster files: **GIF files, PNG files, JPEG files.**

## What is all this important? 

Graphics are different from text fonts, they can't be handled by TeX's font mechanisms; instead, there are special LaTeX packages to deal with them. But, like fonts, there's still a fundamental difference between device-independent or vector graphics (like PostScript drawings) and raster graphics, which are resolution- or device-dependent. Different mechanisms (and different packages) are needed to include the different kinds of graphic files.

Because the graphicx package used with the 

- **latex command** requires all graphics to be in **EPS format**; 
- **pdflatex command**, perversely, requires graphics to be **either JPEG, TIFF, PNG, or PDF — but not EPS**.

So if you compile your LaTeX source file with the latex command, you'll have to convert any non-EPS files to EPS format. 

{% note warning %}
**But, if you use pdflatex, you'll have to convert any EPS files to either PNG or (preferably) PDF format.**
{% endnote %}

## In LaTex,

```latex
\usepackage{float}
\usepackage{graphicx}

\begin{figure}[H]
\centering
\includegraphics[scale=0.5]{universe}
%\includegraphics[width=\textwidth]{universe}
%\includegraphics[scale=1.2, angle=45]{universe}
\caption{The Universe}
\label{fig:universe}
\end{figure}
```

| Parameter | Action                                                            |
|-----------|-------------------------------------------------------------------|
| H         | Place exactly at spot in source text                              |
| h         | Place approximately at spot in source test                        |
| t         | Place at top of page                                              |
| b         | Place at bottom of page                                           |
| p         | Place on page for floats only                                     |
| !         | Override internal LaTeX parameters for determining float position |


## High quality images

{% note warning %}
Warning! Notice that this picture looks pixelated, especially at this scale. One must be careful to use high enough quality images. Preferable are **vectorized graphics** which will scale without pixelation.
{% endnote %}

{% note warning %}
**Save as .pdf**
{% endnote %}

![Difference between .png and .pdf (zoom in 500%). For printing purposes, the best file pdf format to use is a PDF.](https://i.imgur.com/3zjwQQS.jpeg)

## Subfigures

- <https://www1.cmc.edu/pages/faculty/aaksoy/latex/latexfive.html>

When one wants to put multiple subfigures inside a subfigure, one must use two packages, **caption and subcaption**. There are other packages such as _subfigure and subfig, however, these are no longer considered standard_.

Try the code below to see three subfigures within a figure, each with a separate caption in addition to a global caption.

```latex
\documentclass{article}
\usepackage{float}
\usepackage{graphicx}
\usepackage{caption}
\usepackage{subcaption}

\begin{document}
\begin{figure}[H]
	\centering
	\begin{subfigure}{.3\textwidth}
		\includegraphics[width=\textwidth]{atom.png}
		\caption{Atom 1}
	\end{subfigure}
%%%%%%%%%%%%%%
	\begin{subfigure}{.3\textwidth}
		\includegraphics[width=\textwidth]{atom.png}
		\caption{Atom 2}
	\end{subfigure}
%%%%%%%%%%%%%%
	\begin{subfigure}{.3\textwidth}
		\includegraphics[width=\textwidth]{atom.png}
		\caption{Atom 3}
	\end{subfigure}
	\caption{Atoms are fun!}
\end{figure}
\end{document}
```

## Wrapping text around figures

- <https://www.overleaf.com/learn/latex/Inserting_Images>


For the commands in the example to work, you have to import the **wrapfig** package. To use **wrapfig**, include the following line in the document preamble:

```latex
\usepackage{wrapfig}
```

This makes the wrapfigure environment available and we can place an \includegraphics command inside it to create a figure around which text will be wrapped. Here is how we can specify a wrapfigure environment:

```latex
\begin{wrapfigure}[lineheight]{position}{width}
  ...
\end{wrapfigure}
```

| Specifier | Specifier | Actioin                                              |
|-----------|-----------|------------------------------------------------------|
| r         | R         | right side of the text                               |
| l         | L         | left side of the text                                |
| i         | I         | inside edge–near the binding (in a twoside document) |
| o         | O         | outside edge–far from the binding                    |

**The uppercase version allows the figure to float. The lowercase version means exactly here.**

It's also possible to wrap the text around a figure. When the document contains small pictures this makes it look better.

```latex
\begin{wrapfigure}{r}{0.25\textwidth} %this figure will be at the right
    \centering
    \includegraphics[width=0.25\textwidth]{mesh}
\end{wrapfigure}

There are several ways to plot a function of two variables, 
depending on the information you are interested in. For 
instance, if you want to see the mesh of a function so it 
easier to see the derivative you can use a plot like the 
one on the left.
```

![Wrapping text around figures](https://sharelatex-wiki-cdn-671420.c.cdn77.org/learn-scripts/images/a/ac/InsertingImagesEx8Overleaf.png)

# Journals | Graphics Requirements

## American Geophysical Union (AGU)

- https://www.agu.org/publications/authors/journals/text-graphics-requirements#figures

Figures

AGU recommends that figures be prepared in one of the following formats: 

- **JPG, TIFF, EPS, PS or PDF. **
  
Figures should be embedded in the main manuscript for submission but uploaded separately at resubmission or revision (because separate files are needed for production). For revision, each figure file must be complete and contain all parts of a single numbered figure. In other words, a figure with multiple parts must be consolidated into a single cohesive file. Do not include figure captions or figure titles (e.g., “Figure 1”) as any part of the graphic itself. Separate files for different parts of a figure cannot be accommodated.

# References

1. [PS 檔案](https://www.adobe.com/hk_zh/creativecloud/file-types/image/vector/ps-file.html)
   1. **PS 代表 PostScript，是一種向量圖形檔案**。其特點是可以將數位圖形和文字備妥進行列印。您可以將 PS 檔案直接傳送至印表機，而無需使用應用程式開啟檔案。不過，只有為數不多的選項可以開啟 PS 檔案，使其成為影像方面功能最少的檔案類型之一。由於 PS 檔案比部分相關檔案類型稍舊，使用者經常將 PS 檔案轉換成 PDF，讓處理工作更簡單流暢。Adobe 在 1980 年代開發 PS 檔案，協助電腦使用者輕鬆將文字和圖形轉換成印刷副本。這在當時是一大創舉，對方才萌芽的桌面排版 (Desktop Publishing，DTP) 產業來說更是如此。因此，當早期的 Apple 印表機搭載 PostScript 功能時，DTP 在全球出現爆炸性成長，記者、學生和業餘愛好者都可以將手動打字的稿件轉化成令人耳目一新的出版品。現在，除了專門出版品之外，出版品直接在網路上發行的比率已超越印刷形式，使得 PS 檔案格式不再像以往普遍使用。不過，作為圖形格式，PS 仍是最直覺易用的檔案類型之一。
   2. **PDF 在網路和印刷領域上，是 PS 檔案最廣受支援的下一代檔案**。
   3. 傳統的的文件慣用 PS 或 EPS 圖檔，這種所謂的「描邊圖檔」非常適合數學或統計圖，可以為圖形加上文字或其他線條，檔案小而且清晰，編譯完成的檔案也是 PS 檔（近年來逐漸改成 PDF 檔，以利網路傳輸）。因為網路興起及多媒體的表現日漸重要，才有壓縮圖檔的出現，其中以 JPG 圖檔最為流行，適合各種靜態圖形。
2. [EPS 檔案](https://www.adobe.com/hk_zh/creativecloud/file-types/image/vector/eps-file.html)
   1. 壓縮式 PostScript (Encapsulated PostScript) 於 1980 年代晚期由 Adobe 推出後，便成為設計產業早期選用的影像格式。此格式的設計宗旨是協助影像和插圖與佔有主要比重的文字創作相整合。
   2. **雖然 EPS 檔案大多已由 PDF 等更新的檔案格式取代**，這種幾乎相容於所有系統和軟體的舊版格式仍具備實用性。
   3. **PDF 可編輯且相容於更多作業系統**。但是，如果您使用較舊的列印裝置，可能會發現 EPS 是更理想的選擇。
3. [SVG 檔案](https://www.adobe.com/hk_zh/creativecloud/file-types/image/vector/svg-file.html)
   1. 可縮放向量圖形 (SVG) 是一種適合網頁使用的**向量檔案格式**。與 JPEG 等像素型點陣檔案有所不同，向量檔案是透過以網格點線為基礎的數學公式儲存影像。這表示 SVG **這類向量檔可以大幅調整尺寸卻不會損失品質**，**因此很適合用於標誌及複雜的線上圖形**。 
   2. SVG 檔案的歷史可追溯到 1990 年代晚期，全球資訊網協會 (W3C) 曾邀請開發人員針對新的向量圖形格式提案。當時協會收到六項提案，最後成功協助 W3C 推出 SVG 格式。
   3. SVG 花了一段時間累積知名度。這種格式在過去鮮少人使用；直到 2017 年，人們發現在新型網頁瀏覽器上使用 SVG 的好處，這種格式才逐漸受到歡迎。現在，SVG 已廣泛用於 2D 網站影像，因為大多數使用向量檔案的瀏覽器和繪圖應用程式都可輕鬆處理此檔案格式。
4. [TIFF 檔案](https://www.adobe.com/hk_zh/creativecloud/file-types/image/raster/tiff-file.html)
   1. TIFF 代表標籤影像檔案格式 (Tag Image File Format)，是一種用於儲存點陣圖形和影像資訊的電腦檔案。TIFF 是攝影師偏好使用的格式。如果您想避免失真檔案格式，TIFF 便是編輯作業前相當易用的高品質影像儲存格式。
   2. **是一種不失真的檔案壓縮形式，這表示其檔案大小比多數同類檔案來得大，但影像品質不會受損**。
   3. Aldus Corporation 於 1980 年代中期推出 TIFF 檔案格式，用於桌面排版。TIFF 不僅能保留高品質資料，也可以直接從電腦發布內容。這種檔案設計成桌上型掃描器的通用格式；而之前的作業硬體 (視品牌和型號而定) 只支援數量有限的檔案格式。
5. [PNG 檔案](https://www.adobe.com/hk_zh/creativecloud/file-types/image/raster/png-file.html)
   1. PNG 的全名為可攜式網路圖形 (Portable Network Graphic)，是一種點陣影像檔案。因為 PNG 檔案類型能夠處理有透明或半透明背景的圖形，因此特別受到網頁設計師的喜愛。此檔案格式未申請專利，因此可使用任何影像編輯軟體開啟 PNG 檔案，而無需取得授權。
   2. PNG 影像格式在 1995 年推出，由 IT 專家 Oliver Fromme 取名為 PING，隨後縮短為 PNG。
   3. PNG 的發展傳承自 GIF 格式，這種格式在 PNG 首度推出時已存在八年。GIF 有幾項缺點，像是需要專利授權、色彩種類有限 (僅 256 色)，導致發展速度無法跟上不斷進步的電腦螢幕解析度。為了避免這類問題，PNG 檔案採用無專利設計，並提供更加豐富的色彩選擇。與 GIF 不同的是，PNG 是單一影像格式，不支援動畫。
6. [GIF 檔案](https://www.adobe.com/hk_zh/creativecloud/file-types/image/raster/gif-file.html)
   1. GIF 代表圖形交換格式 (Graphics Interchange Format)。GIF 是一種點陣檔案格式，專為主要出現在網際網路上的較基本影像所設計。**每個檔案最多可支援每個像素 8 位元**，而且可包含 256 種索引顏色。**GIF 檔案也允許合併影像或影格，以建立基本動畫**。
   2. GIF 檔案格式是由電腦科學家 Steve Wilhite 和他在美國科技公司 CompuServe 的團隊於 1987 年 6 月所打造的。
   3. GIF 檔案已進化成能夠提供更多**動畫功能**。例如，1995 年 Netscape Navigator 瀏覽器問世之後，創作者獲得了循環播放 GIF 動畫的能力。Facebook 在 2015 年開始支援 GIF，Instagram 則是在 2018 年開始支援。
7. [JPEG 檔案](https://www.adobe.com/hk_zh/creativecloud/file-types/image/raster/jpeg-file.html)
   1. JPEG 代表聯合圖像專家小組 (Joint Photographic Experts Group)，這個國際組織在 1980 年代末和 1990 年代初對此格式進行了標準化。自從攝影師開始在數位相機和其他複印設備上拍攝及儲存影像以來，JPEG 就一直是數位影像的首選檔案格式。
   2. 在 1986 年，顯示器技術還無法產生螢幕上的圖形。就在那時候，一個稱為國際標準化組織 (ISO) 的團隊開始研究如何將逼真的圖片帶到世界各地的小螢幕上。
   3. **失真壓縮可能會節省空間，但是在處理高度壓縮的影像時，品質會受到影響**。具有清晰邊緣和線條的影像在壓縮後會失去一些清晰度。
   4. 嚴謹的攝影師仍然不願以 JPEG 格式拍攝，因為他們想要保留所有影像細節以供後製處理或列印使用。不過，這種檔案格式仍然是主流人士的最愛。