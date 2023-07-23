---
layout: post
title: Specifying Xcode version for Github Actions.
---

I'm attempting to use [Ranges](https://en.cppreference.com/w/cpp/ranges) in my projects that build across platforms, but Xcode support for ranges landed in 13.4.1.  The default stable version of Xcode in Github actions is older than that.

If you look to the Github actions [runner pages](https://github.com/actions/runner-images/tree/main) and look at the details for [MacOS](https://github.com/actions/runner-images/blob/main/images/macos/macos-13-Readme.md), you can see that several versions of [Xcode are installed](https://github.com/actions/runner-images/blob/main/images/macos/macos-13-Readme.md#xcode).  Now it is just a matter of running xcode-select to pick the right one.

I stumbled upon [setup-xcode](https://github.com/maxim-lobanov/setup-xcode) that does basically this.  I simply had to add this to the my workflow yml:

```
    - name: Installing Xcode (MacOS)
      if: matrix.config.os == 'macos-13'
      uses: maxim-lobanov/setup-xcode@v1
      with:
        xcode-version: '14.3.1'

```

and I was now using the latest version of Xcode.  Very nice!


