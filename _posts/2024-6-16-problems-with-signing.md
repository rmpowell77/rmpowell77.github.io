---
layout: post
title: Problems with trusting and signing.
---

It looks like the most recent versions of [CalChart that I've been producing](https://github.com/calband/calchart/releases/tag/v3.7.0) have a problem that when you open them you get popup that says:

```
“CalChart” is damaged and can’t be opened. You should eject the disk image.
```

Now previously with [v3.6.8](https://github.com/calband/calchart/releases/tag/v3.6.8) this would say:

```
“CalChart” cannot be opened because the developer cannot be verified.
```

I'm currenlty working with the assumption that with v3.7.0 I updated the version of the OS I built with.  After reading a couple of docs:

 * https://eclecticlight.co/2023/03/13/ventura-has-changed-app-quarantine-with-a-new-xattr/ 
 * https://www.reddit.com/r/macsysadmin/comments/13vu7f3/app_is_damaged_and_cant_be_opened_error_on_ventura/
 * https://discussions.apple.com/thread/3145071?sortBy=best
 * https://github.com/Ecks1337/RyuSAK/issues/34

I learned about the `xattr` command.  If I were to run:

```
xattr -rc  /Applications/CalChart-3.7.0.app 
```

this fixes things, but it leaves a bad taste in my mouth.  

What I'm wondering is why the issue moved from "trust" this developer to "it's broken."

The way I see it, I have two options:

 * Figure out what the problem is so the old workaround of "trust this developer" is something that people need to do.
 * Set up a developer profile.

I'm going to start investigate what it takes to do development the right way.


