---
layout: post
title: More clean-up of munkres-cpp
---

Last time I was looking to create a Submodule of 


```
cl : command line error D8021: invalid numeric argument '/Wextra' 
```

A quick search turned up [this on Stack Overflow](https://stackoverflow.com/questions/2274006/cmake-invalid-numeric-argument-wextra).

> Your CMakeLists.txt defines compile flag -Wextra for GCC and then CMake tried to use that on cl (the Microsoft compiler) too. Fix the CMakeLists.txt so that it tests for compiler before setting warning flags, i.e.
>  
> ```
> # Set default compile flags for GCC
> if(CMAKE_COMPILER_IS_GNUCXX)
>     message(STATUS "GCC detected, adding compile flags")
>     set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++98 -pedantic -Wall -Wextra")
> endif(CMAKE_COMPILER_IS_GNUCXX)
> ```

Ok, so looks like I should clean up the compiler options as long as I'm at cleaning up other things.  My guess is that incrementing the version of CMake to use also changed this behavior.

So I'll add that change to fork of `munkres-cpp` and rebase and post.
