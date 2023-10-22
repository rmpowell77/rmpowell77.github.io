---
layout: post
title: Learning WASM
---

I've been interested for a while in "modernizing" [CalChart](https://github.com/calband/calchart) so that it works in the browser instead of being a native app.  There's been some attempts at this in the past, like the [CalChart Resign](https://github.com/calband/calchart-redesign), but these have been attempts to fully re-write CalChart.

There's a lot in an application that isn't the UI portions, like do/undo systems, logging, debugging, shape management, animations, and saving/restoring.  Being able to build that from scratch is non-trivial.  So instead I'm looking at an approach that allows us to avoid that.

A web app is inticing because it has the promise of distribution to multiple platforms without requiring people to install anything.  I've been concerned that installation in particular will be a problem for CalChart because of signing concerns that seem to be coming for most platforms.  Additionally, wxWidgets has unusual formating problems for High Density Interfaces on Windows, and if we can avoid that problem, it would be great.

So I'm thinking that building up a basic application in WASM may be what we need.  The CalChart Core could be a good way to start this project, building up a simple viewer around it.  It'll be a long term effort, but it will be fun to try follow some guide like the ones I've found here: https://medium.com/@tdeniffel/pragmatic-compiling-from-c-to-webassembly-a-guide-a496cc5954b8

Let's see how this goes!


