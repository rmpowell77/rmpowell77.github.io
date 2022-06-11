---
layout: post
title: Benchmarking with CMake, VSC, and nanobench
---

Years ago I used to be in the habit of going to the Borders in Palo Alto and thumb through the Programming Guides, and eyeing the 1 or 2 shelf on C++ programming.  Most books were the "Teach yourself C++ in 21 days" type books, but once in a while you’d find a real gem like [Scott Meyer’s "Effective C++"](https://www.amazon.com/Effective-Specific-Improve-Programs-Designs/dp/0321334876).  I remember buying that book and completely changing the way I programmed after reading it.

The Borders is gone, replaced by [HanaHaus](https://www.hanahaus.com/paloalto#hanahauspaloalto), a co-working space, but my desire to learn about the latests in Programming and C++ has not diminished.  Fortunately, there’s a huge amount of online resources available to C++ programmers these days.    Some of these I only knew by name a couple of years ago, but this past couple of years prompted me to learn more about them, and a long the way I found some really great resources.

I wanted to have a solid hobby project where I could put some energy into trying out some of these resources I’ve been checking out.  I recently purchased an M1 MacBook Air.  I’ve always been curious about DSP, so I thought trying out some benchmarking tools could be a great way to learn something about this new machine, and also to learn something about what’s new in C++ out there.

I’ve been hearing a lot of great stuff about [CMake](https://cmake.org).  Here are some great resources to know about:
 
 * [CMake Tutorial](https://cmake.org/cmake-tutorial/)
 * [CGold](https://cgold.readthedocs.io/en/latest/index.html)
 * [Effective Modern CMake](https://gist.github.com/mbinna/c61dbb39bca0e4fb7d1f73b0d66a4fd1)
 * [Creating Mac OS X Packages with CMake](https://blog.kitware.com/creating-mac-os-x-packages-with-cmake/)

You can find the source and example files on [my github](https://github.com/rmpowell77/fft_benchmarks).

Let's start with a super simple `CMakeLists.txt`.

```
➜  cat CMakeLists.txt 
cmake_minimum_required(VERSION 3.23)

set (CMAKE_CXX_STANDARD 20)

project(
  fft_benchmark
  VERSION 1.0
  LANGUAGES CXX)

add_executable(fft_benchmark fft_benchmark.cpp)
```

```
➜  cat fft_benchmark.cpp 
#include <iostream>

int main() {
  std::cout<<"hello world\n";
}
```

I'm going to target benchmarking [FFTW](http://www.fftw.org).  First I'm going to check to update my CMake file to use the library:

```
➜  cat CMakeLists.txt 
...
find_package(PkgConfig REQUIRED)
pkg_search_module(FFTW REQUIRED fftw3 IMPORTED_TARGET)
include_directories(PkgConfig::FFTW)
link_libraries     (PkgConfig::FFTW)
```

And for using it I'll create a little struct to wrap the file:
```
struct FFT {
  using complex = std::complex<double>;
  FFT(int log2n);

  void fill_input(std::vector<complex> const &input);

  std::vector<complex> fetch_output() const;

  void execute();
  fftw_plan p{};
};
```

And I'll show it in action with a Cosine:

```
int main() {
  auto fft = FFT(4);

  for (auto i = 0; i < fft.N; ++i) {
    in[i] = cos(i * 2 * std::numbers::pi/fft.N);
  }

  std::cout<<"Input: ";
  std::copy(in.begin(), in.end(), std::ostream_iterator<FFT::complex>(std::cout, ", "));
  std::cout<<"\n";

  fft.fill_input(in);
  fft.execute();
  auto out = fft.fetch_output();

  std::cout<<"\nOutput: ";
  std::copy(out.begin(), out.end(), std::ostream_iterator<FFT::complex>(std::cout, ", "));
  std::cout<<"\n";
}
```

Which gives me these results:
```
Input: (1,0), (0.92388,0), (0.707107,0), (0.382683,0), (6.12323e-17,0), (-0.382683,0), (-0.707107,0), (-0.92388,0), (-1,0), (-0.92388,0), (-0.707107,0), (-0.382683,0), (-1.83697e-16,0), (0.382683,0), (0.707107,0), (0.92388,0), 

Output: (-8.99621e-16,0), (8,-1.08026e-15), (4.39601e-17,4.71028e-16), (-9.05066e-16,-6.90891e-17), (9.95799e-17,-8.88178e-16), (3.66477e-16,6.90891e-17), (2.00969e-16,4.71028e-16), (0,4.59171e-17), (2.10602e-16,0), (0,-3.76406e-17), (2.00969e-16,-4.71028e-16), (3.58201e-16,-6.90891e-17), (9.95799e-17,8.88178e-16), (-9.13342e-16,6.90891e-17), (4.39601e-17,-4.71028e-16), (8,1.07198e-15), 
```

or when plotted:
![cosine]({{ site.baseurl }}/images/cosine.png "cosine")
![cosine-transformed]({{ site.baseurl }}/images/cosine_transformed.png "cosine-transformed")

Ok, time to add some benchmarking.  I was intrigued by this benchmarking software I ran across called [nanobench](https://nanobench.ankerl.com).  So let's integrate it into our test:

```
➜  cat CMakeLists.txt 
...
include(FetchContent)

FetchContent_Declare(
    nanobench
    GIT_REPOSITORY https://github.com/martinus/nanobench.git
    GIT_TAG v4.1.0
    GIT_SHALLOW TRUE)

FetchContent_MakeAvailable(nanobench)

...
target_link_libraries(fft_benchmark PRIVATE nanobench)
```

And now we use it in our code:
```
  for (auto i : { 7, 8, 9, 10, 11, 12}) {
    auto fft = FFT(i);
    auto in = std::vector<FFT::complex>(fft.N);
    for (auto i = 0; i < fft.N; ++i) {
      in[i] = cos(i * 2 * std::numbers::pi/fft.N);
    }
    ankerl::nanobench::Bench().run(std::string("fftw ") + std::to_string(fft.N) , [&] {
        fft.execute();
    });
  }
```

Which gives us the results when configured for release:
```
|               ns/op |                op/s |    err% |     total | benchmark
|--------------------:|--------------------:|--------:|----------:|:----------
|              238.29 |        4,196,629.09 |    3.7% |      0.00 | `fftw 128`
|              536.39 |        1,864,312.35 |    0.2% |      0.00 | `fftw 256`
|            1,226.35 |          815,427.00 |    0.8% |      0.00 | `fftw 512`
|            2,815.12 |          355,224.01 |    0.2% |      0.00 | `fftw 1024`
|            6,104.25 |          163,820.29 |    0.1% |      0.00 | `fftw 2048`
|           14,041.67 |           71,216.62 |    1.9% |      0.00 | `fftw 4096`
```

Nice!  Very easy to get nanobench integrated with this.

Now let's try benchmarking [Accelerate](https://developer.apple.com/documentation/accelerate), Apple's DSP library.  First we add to CMake:

```
➜  cat CMakeLists.txt 
...
find_library(ACCELERATE_LIB Accelerate)
target_link_libraries(fft_benchmark PRIVATE nanobench ${ACCELERATE_LIB})
```

Then we're going to create a struct for managing the Accelerate, and use it in benchmarks here:

```
  for (auto i : { 7, 8, 9, 10, 11, 12 }) {
    auto fft = vDSP_FFT(i);
    auto in = std::vector<vDSP_FFT::complex>(fft.N);
    for (auto i = 0; i < fft.N; ++i) {
      in[i].real = cos(i * 2 * std::numbers::pi/fft.N);
    }
    ankerl::nanobench::Bench().run(std::string("accelerate ") + std::to_string(fft.N) , [&] {
        fft.execute();
    });
  }
```

And finally we get our results:

```
|               ns/op |                op/s |    err% |     total | benchmark
|--------------------:|--------------------:|--------:|----------:|:----------
|              245.92 |        4,066,403.96 |    0.0% |      0.00 | `fftw 128`
|              574.13 |        1,741,766.20 |    0.2% |      0.00 | `fftw 256`
|            1,302.71 |          767,632.98 |    0.1% |      0.00 | `fftw 512`
|            3,014.93 |          331,682.82 |    0.1% |      0.00 | `fftw 1024`
|            6,541.71 |          152,865.13 |    0.1% |      0.00 | `fftw 2048`
|           14,763.67 |           67,733.85 |    0.1% |      0.00 | `fftw 4096`
|              199.10 |        5,022,696.70 |    0.1% |      0.00 | `accelerate 128`
|              474.46 |        2,107,648.73 |    0.1% |      0.00 | `accelerate 256`
|            1,248.76 |          800,791.37 |    0.1% |      0.00 | `accelerate 512`
|            2,803.94 |          356,640.86 |    0.1% |      0.00 | `accelerate 1024`
|            5,786.50 |          172,816.04 |    0.1% |      0.00 | `accelerate 2048`
|           12,906.25 |           77,481.84 |    0.1% |      0.00 | `accelerate 4096`
```

So there you have it.  With a couple of simple external projects like CMake and nanobench I'm able to do some quick benchmarking of FFTw vs Accelerate.




