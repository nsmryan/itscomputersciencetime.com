+++
title = "Work- The Low Tech C Build Tool"
[taxonomies]
categories = ["Language", "Programming", "C", "build", "libtcc", "tcc", "make"]
+++
It seems that every programmer eventually writes one of more of the following:
a text editor, a ray tracer, a compilers, or a build system. The number of
build systems out there is almost a running joke in compuing.


With that said, my build system is called 'work', named for similarity with
'make'. I didn't want to create something just like every other build system,
so I thought I would try to sidestep all of their complexity and create a 
"low-tech" build system. No dependency resolution, no complicated rules,
no implicit flow control. Just a C program that builds a C program.

Building software with a program written in the program's own language was
inspired by Jonathan Blow's Jai and Andrew Kelley's Zig builds, although not
operating in the same way. I had this thought whileI was writing a Makefile,
building a bunch of C files, and wondering why I couldn't just loop through
a list of files and build them in turn. The implicit Makefile rules can
become complex, and I often just want to build a few files and link them-
nothing fancy.


I had recently discovering tcc, the tiny C compiler, which is
pretty awesome for its size, speed, and the fact that it can be used as a
library. I was especially interested in the idea of a C compiler as
a simple library with only a handful of function. It seems like there
are a lot of neat things one could do with this, and a build system is
among them.


## How does it Work?
The concept of Work is that you write a C program while simply calls
a few library functions to build up commands to run, and then
runs them. This does not involve building a dependency graph and 
passing control off to the resolver- you build the commands you want
and run the commands, with help in handling flags, linking, etc.


The build program, called 'work.c', would be compiled from scratch
every time using libtcc, which is about 10 times faster then gcc
in my experience and can compile small programs fast enough to feel
about instantaneous. This program would be built and linked with
a libwork library, and could call functions to build and run
commands. I never completely fleshed this idea out as I ran into
troubles, but this is the idea.


What makes this interesting to me is the idea of a nearly instant
build, assuming you build your C program with tcc and not gcc
or clang. The question I wanted to answer is whether programs like
make are really necessary, or just a product of a slow toolchain.
In principal you could rebuild your whole program very quickly,
or add little bits of logic into your work build to only
rebuild certain portions. 


If this turned out to work well enough, perhaps it could be a much simpler,
lower tech build solution. I know that using tcc isn't an option in many cases,
and in general the concept might be completely bad, but I think its interesting
enough that you might be able to sidestep all of incredible subtly of build
systems, dependency resolution, dynamic dependencies, changes in files, actual
vs declared dependencies, all the kinds of problems that seem to plague all
build tools.




