+++
title = "Protoflight Flight Software"
[taxonomies]
categories = ["C", "nasa", "flightsoftware", "embedded"]
+++

One of my long term, occasional hobby projects is a piece of software
called [protoflight](https://github.com/nsmryan/protoflight). This is
a very much a work in progress, and something I only spend small amounts of time
on very rarely, so I wanted to post about it.


This is a very small, toy software system modeled off of flight software systems
that I have encountered while at NASA. The main inspirations are [CFS](https://github.com/nasa/cFS),
and the flight software system I have been working on at Langley Research
Center, with smaller inspiration from other systems.


The github README for protoflight gives an overview of the project, but the
main idea is to develop a tiny system with the core concepts of a flight
software system.  I wanted to explore this space while keeping things as small
as possible.  For a sense of scale- protoflight as a whole is likely to remain
smaller then many single CFS apps, and at least an order of magnitude smaller
then CFS itself, or even CFE (or really even just OSAL).


I like the idea of this software as a model of a real system. One could study
such a small system, modify it, add new features, as an exploration of a design
concept. You could also use it as a way to understand a practice that is not
necessarily obvious such as how to do unit tests in embedded systems, how to
do a small OS abstraction layer, how to implement common mechanisms in these
systems such as a message bus, packet headers, fault detection, etc.


I want it to be an example of the best practices I am aware of for flight
software. This includes portability from tiny systems, Linux, and MSYS2, real
time and non-real time systems, with or without a file system.
I want to implement code metrics, unit tests, test coverage, system tests,
consistent style, static analysis, strict compilation flags, etc. The
hope is that in such a small system it will be possible for me, as an
individual, to do all of this.



The project is currently buildable with some basic functionality, and unit
tests for all modules. It has not been run on an embedded system, and there
is no ground system yet. I originally wanted to set up [COSMOS](https://cosmosc2.com/),
but I may do something much simpler to keep with the small size feeling.


There is a great deal of work to do here. I hope to occasionlly spend a few
hours on it, and eventually to have a very well tested, minimal, but high
quality system.


If you are interested, feel free to check out the README, read through the code,
and try the build. If you are really interested, I would welcome contributions.

