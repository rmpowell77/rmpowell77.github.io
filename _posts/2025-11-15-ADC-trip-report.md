---
layout: post
title: ADC 2025 Trip Report
---

The [ADC 2025](https://audio.dev/conference/) conference wrapped up earlier this week, and I wanted to share my thoughts.  As always, The conference was a highly informative and engaging event that brought together experts in the field of audio development. Like last year, it featured a range of sessions, workshops, and networking opportunities that provided valuable insights into the latest trends and technologies in audio development (see [my previous trip report](https://rmpowell77.github.io/ADC-trip-report/) for more details.  

## Day 1 — GPU Audio Workshop

I started the conference attending the [GPU Audio](https://www.gpu.audio) [workshop](https://conference.audio.dev/session/2025/gpu-powered-real-time-audio-source-separation/). The presenters demonstrated how to build an audio source separator inspired by the [HS-TASNET work from L-Acoustics](https://arxiv.org/abs/2402.17701), using their SDK to run the model on the GPU. Unfortunately, the speakers were unable to attend in person and had to present over Zoom.

The session also felt a bit overly ambitious: while the technology is impressive, I think a simple “Hello World”–style introduction to their framework would have made for a more accessible and impactful workshop.


## Afternoon — ADCx Sessions

The afternoon was devoted to the ADCx talks—rapid-fire 18-minute sessions meant to provide quick introductions to a variety of topics. A few standouts:
 
Josh Coalson (FLAC) gave an [excellent history of the FLAC format](https://conference.audio.dev/session/2025/a-history-of-flac-the-free-lossless-audio-codec/). One line that stuck with me:

> “If you have an idea, likely many people have the same idea. What helps you succeed is the way you execute it.”

David Su’s [“50 Ways of Making a Sine”](https://conference.audio.dev/session/2025/a-sine-by-any-other-language/) was a fun survey of generating a sine tone in different programming languages. It had a very cute ending—definitely worth watching once the recordings are released.

Christian Luther's [“Mind the Spike”](https://conference.audio.dev/session/2025/mind-the-spike/) was a great talk analyzing how performance issues can emerge, using Zipf’s law to understand performance statistics and identify where problems may hide.

## Scheduling, Lock-Free Structures, and Memory Models

Rachel Susser gave a very clear and well-diagrammed talk on [efficient task scheduling in a multithreaded audio engine](https://conference.audio.dev/session/2025/efficient-task-scheduling-in-a-multithreaded-audio-engine/). She compared several scheduling strategies—including work stealing—with helpful explanations of where each approach can struggle, especially when many threads wake simultaneously.

Dave Roland’s talk on [lock-free queues](https://conference.audio.dev/session/2025/lock-free-queues-in-the-multiverse-of-madness/) explored multiple implementation techniques and the different performance characteristics they exhibit. Very practical and insightful.

Timur’s session on [std::memory_order](https://conference.audio.dev/session/2025/demystifying-stdmemory_order/) was probably the first time the C++ memory model really clicked for me. Understanding that memory orders describe an abstraction of 3 different aspects of memory complexity (the compiler reordering, the CPU microcode reordering, and the caching behavior) was a eye-opening. A really great talk.

## Strongly Typed Units for Audio

Roth Michaels presented a compelling exploration of using [strongly typed units in audio software](https://conference.audio.dev/session/2025/using-strongly-typed-units-in-digital-audio-software/). The inclusion of non-linear units like dB was especially interesting—I'm curious to see how that design direction evolves. The vocabulary-style approach to physical units could lead to safer and more expressive code, and it was great to see all examples rooted in audio contexts.


## Keynotes

Both Keynotes were engaging, challenging, and memorable.  Julian Storer's [ADC 2015 to 2035](https://conference.audio.dev/session/2025/adc-2015-to-2035/) was both a walk down memory lane and a chance to peek a little into the future.  And Lu Wilson's [How Many Heads Do You Need](https://conference.audio.dev/session/2025/how-many-heads-do-you-need/) was unlike anything I was expecting. I think this keynote challenged my understanding of how to be creative when coding, and I felt like I was watching a completely abstract way of making music with programming that I hadn't even imagined existed.

## Wrapping up

As always an excellent conference, a great venue, awesome technical ideas to learn from, and a wide variety of developers to talk with! I hope that I get a chance to meet you at this conference next year!



