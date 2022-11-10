---
title: "Unicode with xelatex"
date: 2022-11-10
tags: ["unicode", "latex", "xelatex"]
categories: TOOL
cover: 
    image: 
---

[Unicode](https://zh.wikipedia.org/wiki/Unicode)丰富了日常使用的字符集, 让我们可以使用一种简单便捷的方式表达各种奇怪的字符, 比如想要在输入一个雪人[Snowman](https://unicodeplus.com/U+2603), `U+2603`

<center>
<font size=18>☃</font>
</center>

latex作为日常使用的排版工具, 怎么在latex中插入一个unicode? 一般来讲, 应该`Ctrl+C`, `Ctrl+V`就可以, 我们来看看下面的代码

```latex
\documentclass[utf8]{article}

\begin{document}
    \huge
    Unicode char \texttt{U+13105} is  𓄅
    
    Unicode char \texttt{U+16E4} is ᛤ

    Unicode char \texttt{U+25E6} is ◦
\end{document}
```

然而输出的PDF中并没有包含所有的Unicode字符, 看起来这个问题还存在一定的共性[unicode-characters-not-displaying-in-xetex](https://tex.stackexchange.com/questions/45796/unicode-characters-not-displaying-in-xetex), 而且似乎是个字体问题.

![](2022-11-10-22-08-15.png#center-medium)

> Tex Engine使用[xelatex](https://en.wikipedia.org/wiki/XeTeX#Example)

我们来试试更换为[FreeMono](https://en.wikipedia.org/wiki/GNU_FreeFont)字体


```latex
\documentclass[utf8]{article}

\usepackage{fontspec}
\setmainfont{FreeMono}

\begin{document}
    \huge
    Unicode char \texttt{U+13105} is  𓄅
    
    Unicode char \texttt{U+16E4} is ᛤ

    Unicode char \texttt{U+25E6} is ◦
\end{document}
```

![](2022-11-10-22-26-48.png#center-medium)

Ha!现在ᛤ已经可以正常显示了, 𓄅却变成了一个奇怪字符. 其实这是因为在FreeMono字体中缺失字符𓄅, 而ᛤ之所以能显示是因为FreeMono包含了这个字符.

因此我们可以发现, 在xelatex中使用Unicode, **需要使用正确的字体**, 即字体中要包含应相应的unicode字符. 

那新的问题来了, 怎么知道什么字体包含了𓄅? 逛了一圈stackoverflow发现[fileformat.info](https://www.fileformat.info/search/)可以解决这个问题. 

𓄅其实在Egyptian Hieroglyphs中, 找到相应的字体[Noto Sans Egyptian Hieroglyphs](https://fonts.google.com/noto/specimen/Noto+Sans+Egyptian+Hieroglyphs)

最后一步, 我们要加入缺失的字符, 并为𓄅指定正确的字体

```latex
\usepackage{newunicodechar}
\newfontfamily{\substitutett}{Noto Sans Egyptian Hieroglyphs}
\newunicodechar{𓄅}{{\substitutett 𓄅}} 
```

最终就可以正常显示所有的Unicode字符了.

![](2022-11-10-23-02-30.png#center-medium)

**Reference**

https://jwodder.github.io/kbits/posts/unicode-latex/
