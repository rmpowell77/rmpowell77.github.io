---
layout: post
title: Problems with Visual Studio Code debugging
---

I was hitting an issue when hitting a breakpoint in Visual Studio Code, the debugger would freeze and I'd see a process like lldb-mi going out of control.  [This post](https://stackoverflow.com/questions/69443991/visual-studio-code-stucks-in-the-beginning-when-debug-c-code-on-mac) on stack overflow was incredibly helpful in pointing out that I was using the wrong debugger.

