+++
title = "Work- The Low Tech C Build Tool"
[taxonomies]
categories = ["Language", "Programming", "C", "build", "libtcc", "tcc", "make"]
+++
It seems that every programmer eventually writes one of more of the following:
a text editor, a ray tracer, a compilers, or a build system. The number of
build systems out there is almost a running joke in computing.


With that said, my build system is called 'work', named for similarity with
'make'. I didn't want to create something just like every other build system,
so I thought I would try to sidestep all of their many subtle complexity and
create a "low-tech" build system. No dependency resolution, no tree or graph or
anything, no complicated rules, no implicit flow control. Just a C program that
builds a C program.

Building software with a program written in the program's own language was
inspired by Jonathan Blow's Jai and Andrew Kelley's Zig builds, although not
operating in the same way. I had this thought while I was writing a Makefile,
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
instantaneous. This program would be built and linked with
a libwork library, and could call functions to build and run
commands. I never completely fleshed this idea out as I ran into
troubles, but this is the idea.


What makes this interesting to me is the idea of a nearly instant
build, assuming you build your C program with tcc and not gcc
or clang. The question I wanted to answer is whether build systems
like make are necessary, or just a product of a slow toolchain.
In principal you could rebuild your whole program very quickly,
or add little bits of logic into your work build to only
rebuild certain portions. 

## Internals
The work interface is pretty small- there is the work state
structure WrkState which holds your tcc state, and the
WrkTarget which contains all the information about a command
you want to run.

The work build system is specialized for building C programs,
and other system's language programs,  so while you can have
a general command (say to generate code, or create a build
directory, etc), commands can also be tagged as creating
a static or dynamic object, exe, etc. 


A WrkTarget encodes which compiler flags (or generally which 
command line flags) you want, the library paths, the include
path, input files, everything that goes into compiling a C
program. It also has a pointer to a parent WrkTarget, which I
intended to use to help with grouping flags and with outputing
flags from one module to another, to communicate compilation
information outside of a module.

The WrkTarget can be run, which builds up a command string
using all the options and the command line tool defined in the
WrkTarget. It then simply calls 'system' to run the command. The
concept was that you would use tcc so that the lack of caching
would be less of a problem, but nothing enforces that.


The general flow here is as follows:
  * The user runs the 'work' command.
  * Work finds a work.c file in the current directory (or it is provided a file name).
    This mirrors how a Makefile is run by make.
  * Work compiles this file using libtcc at runtime, and links in libwork to provide
  the functionalty used in the work.c file.
  * Work then runs the work.c file's wrk\_main function, providing the WrkState and
  a WrkTarget if you want to pass in extra flags that can be used in the build.
  * The wrk\_main function builds the project by creating WrkTargets and calling
  wrk\_target\_build. There commands can generate object files, shared objects, static
  objects, executables, and anything else.
  * Ideally the wrk\_main function could call into other work.c files for building
  submodules, but I never made it this far. I considered having wrk\_main return
  an array of the files it built to help other modules use them. For example a
  module may return the .o files for the parent module to link into its executable.

This concept does work- I was able to get far enough for work to build itself- I
ran into trouble with libtcc compiling code that crashes and seems to be undebuggable.

## Example
The [example work file](https://github.com/nsmryan/work/blob/master/example/work.c)
shows what it takes to compile a single file this way.


You have to create your work target, add your file and flags, and
then call wrk\_target\_build. This call executes your command, 
building the main.c example file. The nice thing here would have
been that you could defined WrkTargets with groups of
flags and reuse them, such as by linking then with the parent flag,
and it would be very explicit which commands you used, and which
files you compiled. If there was a problem, you could debug
with a debugger/printf/logging like normal.

There is a more extensive example
that [compiles work itself with itself](https://github.com/nsmryan/work/blob/master/work.c).
This shows looping through flags and files, and the clear, explicit
control flow you get from using C to build your code.


I was planning on having some convienence functions to create
targets from the environment, using variables like CC, CFLAGS,
LDFLAGS, and LDLIBS, which you could then override if necessary,
or provide defaults. Perhaps you could even build a target
through the environment, and then link it to a target with defaults,
such that anything that was not defined (say, there is no CC defined),
gets filled out by the set of provided defaults. I never made
it this far however.


## Troubles
I likely won't finish this project. I ran into some issue where
I believe I have a bug in code that is compiled at runtime, and
therefore not visible to gdb. While I like this project,
I am not invested enough to restructure it to allow better
debugging, or to figure out some subtle issue causing the crash.


This is really a shame, but it was just a proof of concept anyway,
so this writeup is the real result of the work I put into it.


## Conclusion
If this turned out to work well enough, perhaps it could be a much simpler,
lower tech build solution. I know that using tcc isn't an option in many cases,
and in general the concept might be completely bad, but I think its interesting
enough that you might be able to sidestep all of incredible subtly of build
systems, dependency resolution, dynamic dependencies, changes in files, actual
vs declared dependencies, all the kinds of problems that seem to plague all
build tools, simply by making things really, really fast.

