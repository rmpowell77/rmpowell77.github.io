---
layout: post
title: Fixing up munkres-cpp for CalChart
---

I work on [CalChart](https://github.com/calband/calchart), the Show Charting software for the University of California Marching Band.  The project is written in C++ and uses [CMake](https://cmake.org) to build.  There's a couple of libraries used to build the final executable.

The libraries the projects are built to use included as git submodules.  As I've been learning more about CMake I've become interested in the [`FetchContent`](https://cmake.org/cmake/help/latest/module/FetchContent.html).  I think it would be really keen to move all the submodules over to `FetchContent`.  Currently with submodules you specify that the project is a dependency via the [`add_subdirectory`](https://cmake.org/cmake/help/latest/command/add_subdirectory.html) command and git tag it references in the `.gitmodules` file.  With `FetchContent` it  there the dependencies and git tag are specified in the CMake file.

[CalChart](https://github.com/calband/calchart) is already set up with CMake and submodules, so I need to clone out recursively to work with it:

```
➜  git clone --recursive git@github.com:calband/calchart.git ~/Development/github.com/calband/calchart.git
```

Now let's set up [`munkres-cpp`](https://github.com/saebyn/munkres-cpp) to use `FetchContent`.  I'm going to add these lines to the CMakeList.txt file:

```
FetchContent_Declare(
  munkres-cpp
  GIT_REPOSITORY https://github.com/saebyn/munkres-cpp
  GIT_TAG        1fb2cb55fe59388c481689ca2db0a01ac05a0bd1
)

FetchContent_MakeAvailable(munkres-cpp)
```

And remove this line:

```
add_subdirectory(submodules/munkres-cpp EXCLUDE_FROM_ALL)
```

I'm also going to have to modify the CMakeList.txt file in the `src/core` directory -- CalChart is set up as a hierarchy of directories, `src/core` building a core library of CalChart business logic.  Since I'm using FetchContent I don't need to have an include directory, but I should include just the library needed:

```
diff --git a/src/core/CMakeLists.txt b/src/core/CMakeLists.txt
index aaaad56..72afa05 100644
--- a/src/core/CMakeLists.txt
+++ b/src/core/CMakeLists.txt
@@ -79,12 +79,6 @@ add_library(
 target_compile_definitions(calchart_core PUBLIC YY_NO_UNISTD_H)
 SetupCompilerForTarget(calchart_core)
 
-target_include_directories(
-  calchart_core
-  PRIVATE
-  ${PROJECT_SOURCE_DIR}/submodules/munkres-cpp/src
-)
-
 # this is a hack to get contgram to build
 target_include_directories(
   calchart_core
@@ -94,5 +88,5 @@ target_include_directories(
   ${PROJECT_SOURCE_DIR}/src/core
 )
 
-target_link_libraries(calchart_core PRIVATE nlohmann_json::nlohmann_json)
+target_link_libraries(calchart_core PRIVATE nlohmann_json::nlohmann_json munkres)
```
 


Now that we've done this step, we just need to remove the instances of `munkres-cpp` from the `submodules` folder:

```
git rm submodules/munkres-cpp 
```


Now let's give it a shot and run the build command.  This will probably take a while.

```
➜  calchart.git.new git:(main) ✗ mkdir build ; pushd build ; cmake .. ; cmake --build .
```

After about 10 minutes I saw that it had failed:

```
/Users/richardpowell/Development/github.com/calband/calchart.git.new/src/core/e7_transition_solver.cpp:16:10: fatal error: 'munkres.h' file not found
#include "munkres.h"
         ^~~~~~~~~~~
1 error generated.
```

It looks like `munkres-cpp` isn't quite ready to be its own module.  The `munkres-cpp` project was added to CalChart a few years back when students at the University introduced a Transition Solver, which allowed complicated marching patterns between shows.  It used `munkres-cpp`, which I'm not sure if it's abandon-ware, but I haven't seen too many updates to it.  I think it's failing here because its `CMakeList.txt` file isn't formed correctly, and doesn't specify the correct `target_include_directories` for when included as a subdirectory.  So I think what I'll do is fork off the `munkres-cpp` project, fix it up, and then add that as a `FetchContent` project.

First, I'm going to fork <https://github.com/saebyn/munkres-cpp>.  I do so by going to that github page and then pressing the Fork button to bring it to my own area. 
![forking munkres-cpp]({{ site.baseurl }}/images/forking_munkres-cpp.png "forking munkres-cpp")

Now that I have done, that I'm going to clone <https://github.com/rmpowell77/munkres-cpp> to a place I can work on it.  I generally like to have all the projects I clone mirror the path from where they came:

```
➜  ~ git clone --recursive git@github.com:rmpowell77/munkres-cpp.git ~/Development/github.com/rmpowell77/munkres-cpp.git
Cloning into '/Users/richardpowell/Development/github.com/rmpowell77/munkres-cpp.git'...
remote: Enumerating objects: 562, done.
remote: Total 562 (delta 0), reused 0 (delta 0), pack-reused 562
Receiving objects: 100% (562/562), 131.10 KiB | 1.02 MiB/s, done.
Resolving deltas: 100% (304/304), done.
```

Now that I have the project I'm going to make these changes:

```
diff --git a/CMakeLists.txt b/CMakeLists.txt
index bf7032a..d374a3a 100644
--- a/CMakeLists.txt
+++ b/CMakeLists.txt
@@ -1,5 +1,5 @@
 # Common variables.
-cmake_minimum_required  (VERSION 2.8)
+cmake_minimum_required  (VERSION 3.12)
 project                 (munkres-cpp)
 set (munkres-cpp_VERSION_MAJOR 2)
 set (munkres-cpp_VERSION_MINOR 0)
@@ -38,6 +38,7 @@ add_library (
 install (TARGETS munkres                DESTINATION lib     PERMISSIONS OWNER_READ OWNER_WRITE GROUP_READ WORLD_READ)
 install (FILES ${MunkresCppLib_HEADERS} DESTINATION include/munkres PERMISSIONS OWNER_READ OWNER_WRITE GROUP_READ WORLD_READ)
 
+target_include_directories(munkres INTERFACE ${PROJECT_SOURCE_DIR}/src)
 
 # Binary example
 set (MunkresCppBin_SOURCES ${PROJECT_SOURCE_DIR}/examples/main.cpp)
```

The first is to update the version of CMake required as this was giving me a warning when using it in other projects.  The second is actual fix I'm interested.

Next, let's give it a smoke test to make sure everything worked out ok:

```
➜  munkres-cpp.git git:(master) ✗ mkdir build ; pushd build ; cmake .. ; cmake --build .
~/Development/github.com/rmpowell77/munkres-cpp.git/build ~/Development/github.com/rmpowell77/munkres-cpp.git ~ ~/Development/github.com/rmpowell77 ~/Development/github.com/rmpowell77/fun_with_wxWidgets/build2
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
-- Configuring done
-- Generating done
-- Build files have been written to: /Users/richardpowell/Development/github.com/rmpowell77/munkres-cpp.git/build
[ 33%] Building CXX object CMakeFiles/munkres.dir/src/munkres.cpp.o
[ 66%] Building CXX object CMakeFiles/munkres.dir/src/adapters/adapter.cpp.o
[100%] Linking CXX static library libmunkres.a
[100%] Built target munkres
```

Ok, so it seems all good now.  Let's commit and push to github:

```
➜  build git:(master) ✗ git commit -a -m "Adding target_include_directories to allow easier usage in subdirectory in cmake."   
[master bba6501] Adding target_include_directories to allow easier usage in subdirectory in cmake.
 1 file changed, 2 insertions(+), 1 deletion(-)
```

Now I need to go back to CalChart project to change the CMakeList.txt to point at the new repository I've made:

```
include(FetchContent)
FetchContent_Declare(
  munkres-cpp
  GIT_REPOSITORY https://github.com/rmpowell77/munkres-cpp
  GIT_TAG        bba650174a75585a62a3c00b1bfe0559daa8c674
)
```

And now it looks like it works!  

Next we need to push the changes to CalChart:

```
➜  calchart.git git:(dev/fetch-munkres) ✗ git commit -a -m "Converting munkres from submodule to FetchContent."
[dev/fetch-munkres f58ba9e] Converting munkres from submodule to FetchContent.
 4 files changed, 10 insertions(+), 12 deletions(-)
 delete mode 160000 submodules/munkres-cpp
➜  calchart.git git:(dev/fetch-munkres) git push --set-upstream origin dev/fetch-munkres

Enumerating objects: 15, done.
Counting objects: 100% (15/15), done.
Delta compression using up to 8 threads
Compressing objects: 100% (8/8), done.
Writing objects: 100% (8/8), 842 bytes | 842.00 KiB/s, done.
Total 8 (delta 7), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (7/7), completed with 7 local objects.
remote: 
remote: Create a pull request for 'dev/fetch-munkres' on GitHub by visiting:
remote:      https://github.com/calband/calchart/pull/new/dev/fetch-munkres
remote: 
To github.com:calband/calchart.git
 * [new branch]      dev/fetch-munkres -> dev/fetch-munkres
Branch 'dev/fetch-munkres' set up to track remote branch 'dev/fetch-munkres' from 'origin'.
```

Creating the PR causes the current CalChart automation to trigger: 
![CalChart automation]({{ site.baseurl }}/images/forking_munkres-build.png "CalChart Automation")

Finally what we should do is create a friendly patch back to the original munkres-cpp project.  I do so by clicking the contribute button in <https://github.com/rmpowell77/munkres-cpp>:
![Contributing to munkres-cpp]({{ site.baseurl }}/images/forking_munkres-contribute.png "Contributing to munkres-cpp")

Filled out the PR and posted for John Weaver.  Thanks, John!

