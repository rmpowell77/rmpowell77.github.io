---
layout: post
title: Fun with Text!
---

Last night I was thinking about how to do text processing better.  At work we constantly need to look through logs.  I frequently would like to high information I don't need and only show what's relavent.  However, I also keep reaching for ```less```, which is a good tool, but getting a little long in the tooth.  I was starting to think of a tool that could be like ```less``` but could high and show information.  Of course, like any computer hacker, instead of looking for one I started to think about writing one.  This lead me to scour the web for information about text processing.

I stumbled upon [```kilo```](https://github.com/antirez/kilo), a small C program for doing text processing.  I had great fun forking the code and playing around with it.  And then I found Jeremy Ruten's [Build Your Own Text Editor](https://viewsourcecode.org/snaptoken/kilo/), which is sort of a tutorial on how ```kilo``` works.

Some more resources I haven't had a chance to read yet are:

https://invisible-island.net/ncurses/ncurses-intro.html
https://vt100.net/docs/vt100-ug/chapter3.html

I'm probably not going to write my own tool for my original goal, but it's very cool to see what existing tools are out there.

