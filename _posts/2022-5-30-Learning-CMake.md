---
layout: post
title: Learning CMake
---

This morning I spent time just sitting and reading about CMake.  A lot of this infomration is well known and probably obvious to anybody who's been dealing with CMake, but hey, we all gotta start from somewhere, right?

I started making my way through the CMake documentation at [https://cmake.org/cmake/help/latest/](https://cmake.org/cmake/help/latest/).

Some gems:

> [introduction-to-cmake-buildsystems](https://cmake.org/cmake/help/latest/manual/cmake.1.html#introduction-to-cmake-buildsystems):
a project may specify its buildsystem abstractly using files written in the CMake language. 
> CMake input files are written in the "CMake Language" in source files named CMakeLists.txt or ending in a .cmake file name extension.


> [variable-references](https://cmake.org/cmake/help/latest/manual/cmake-language.7.html#variable-references) 
> An environment variable reference has the form $ENV{<variable>}. See the Environment Variables section for more information.

> [lists](https://cmake.org/cmake/help/latest/manual/cmake-language.7.html#lists)
> a string may be treated as a list in certain contexts,

So lists are just special strings?  Interesting.

>[lists](https://cmake.org/cmake/help/latest/manual/cmake-language.7.html#lists)
a string may be treated as a list in certain contexts,
> In function() implementations, avoid ARGV and ARGN, which do not distinguish semicolons in values from those separating values. Instead, prefer using named positional arguments and the ARGC and ARGV# variables. When using cmake_parse_arguments() to parse arguments, prefer its PARSE_ARGV signature, which uses the ARGV# variables.

Gotta keep this in mind.

> [directories](https://cmake.org/cmake/help/latest/manual/cmake-language.7.html#directories)
> “This file may contain the entire build specification or use the add_subdirectory() command to add subdirectories to the build.”

This allows directories to be the general "separation of concerns" - means cmake tends towards putting thing in directories as separation.

> [Requirements for a Library](https://cmake.org/cmake/help/latest/guide/tutorial/Adding%20Usage%20Requirements%20for%20a%20Library.html)
> Remember INTERFACE means things that consumers require but the producer doesn't.


> [variable-references](https://cmake.org/cmake/help/latest/manual/cmake-language.7.html#variable-references)
> Directory Scope
> Each of the Directories in a source tree has its own variable bindings. Before processing the CMakeLists.txt file for a directory, CMake copies all variable bindings currently defined in the parent directory, if any, to initialize the new directory scope. 


So CMake is a set of commands that run after one another.  And directories can be added.  Ok, let's see if this works.

```

➜  testing_cmake1 ls
CMakeLists.txt subdir
➜  testing_cmake1 cat CMakeLists.txt      
cmake_minimum_required(VERSION 3.10)

# set the project name
project(testing_cmake1)

message("Before Project: ${PROJECT_NAME} CMake Project: ${CMAKE_PROJECT_NAME}") 

add_subdirectory(subdir)

message("After Project: ${PROJECT_NAME} CMake Project: ${CMAKE_PROJECT_NAME}") 

➜  testing_cmake1 cat subdir/CMakeLists.txt 
cmake_minimum_required(VERSION 3.10)

message("subdir: Before Project: ${PROJECT_NAME} CMake Project: ${CMAKE_PROJECT_NAME}") 

# set the project name
project(subdir)

message("subdir: After Project: ${PROJECT_NAME} CMake Project: ${CMAKE_PROJECT_NAME}") 

➜  testing_cmake1 mkdir build; pushd build; cmake ..
/tmp/testing_cmake/testing_cmake1/build /tmp/testing_cmake/testing_cmake1 /tmp/testing_cmake /tmp/testing_cmake/doubleit /tmp/testing_cmake/doubleit/build /tmp ~/Development/github.com/rmpowell77/rmpowell77.github.io.git ~/Development/github.com/rmpowell77 ~/Development/github.com/rmpowell77/rmpowell77.github.io.git/_posts ~/Development/github.com/calband/calchart.git/build5
-- The C compiler identification is AppleClang 13.1.6.13160021
-- The CXX compiler identification is AppleClang 13.1.6.13160021
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working C compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/cc - skipped
-- Detecting C compile features
-- Detecting C compile features - done
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/c++ - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
Before Project: testing_cmake1 CMake Project: testing_cmake1
subdir: Before Project: testing_cmake1 CMake Project: testing_cmake1
subdir: After Project: subdir CMake Project: testing_cmake1
After Project: testing_cmake1 CMake Project: testing_cmake1
-- Configuring done
-- Generating done
-- Build files have been written to: /tmp/testing_cmake/testing_cmake1/build
```


Basically gave me what I was looking for.  In subdirectories it inherits all the variable names, and then creates new ones when they are created.

Next, I'm going to work through the [tutorial](https://cmake.org/cmake/help/latest/guide/tutorial/index.html).  It’s great.


Very cool tool: [ccmake](https://cmake.org/cmake/help/latest/manual/ccmake.1.html#manual:ccmake(1))




