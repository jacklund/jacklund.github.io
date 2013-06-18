---
date: 2013-05-30 11:30
title: I Hate LaTeX
tags: latex, documents, pandoc, markdown
layout: post
comments: true
---
No, I really do. It's not hyperbole. I hate it.

It's frickin' 2013, people. We should not have to deal with languages that look like this:

    \renewcommand{\href}[2]{#2\footnote{\url{#1}}}
    $endif$
    $if(strikeout)$
    \usepackage[normalem]{ulem}
    % avoid problems with \sout in headers with hyperref:
    \pdfstringdefDisableCommands{\renewcommand{\sout}{}}
    $endif$
    \setlength{\parindent}{0pt}
    \setlength{\parskip}{6pt plus 2pt minus 1pt}
    \setlength{\emergencystretch}{3em}  % prevent overfull lines
    $if(numbersections)$
    \setcounter{secnumdepth}{5}
    $else$
    \setcounter{secnumdepth}{0}
    $endif$

and parsers that give errors like this:

    paragraph ended before Gin@iii was completed.

The reason I'm even messing with this is that I want a way to convert [markdown](http://daringfireball.net/projects/markdown/) to PDF. Should be easy, right?

Guess again. Just about everything either converts from markdown to HTML, then to PDF, which renders pretty badly, or from markdown to LaTeX to PDF, which renders rather nicely. Except, in order to tweak it (like adding a logo to the title) you have to hack LaTeX. Ugh.

One of the most flexible converters, [pandoc](http://johnmacfarlane.net/pandoc/), which, don't get me wrong, is a very nice package, requires you to tweak a ``default.latex`` file to modify the default format, hence my problem: I just spent around an hour and a half just trying to get a frickin' logo on the cover page where I want it (there's a LaTeX package called "titlepic" which puts it where _it_ wants, but if you want it elsewhere, you're SOL).

Again, it's 2013 and document creation shouldn't be frickin' rocket science! The whole point of writing in markdown is that we don't want to have to mess with complex document formats. I should be able to take a markdown document, pass it into a converter, give it some options in a syntax that doesn't look like 80's-era gobbledygook, and get a decent-looking document out the other end.

*facepalm*
