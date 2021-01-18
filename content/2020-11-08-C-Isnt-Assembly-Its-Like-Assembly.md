+++
title = "C isn't Assembly, but its Like Assembly"
[taxonomies]
categories = ["Language", "Programming", "C", "Assembly"]
+++
This post is just a simple thought-


C is sometimes referred to as a portable assembly language.
There are several articles argueing that this is not true, and
this rings true for me and my experiences with C, where it does
not really match the hardware architecture (at least, not anymore),
where it does not give enough control, where it does not really
map down to assembly in the way one might expect.


I think there is a small modification that makes this statement true-
C is not portable assembly, C is *like* portable assembly. 


What I mean by this is that on modern processors, the Instruction Set
Architecture (ISA) is not executed directly. It is the interface between
software in the hardware, and gets interpreted into an even finer microcode 
language that is actually executed. The ISA is the specification, the interface,
the constant that leaves both sides able to talk and change without
constant deep knowledge of the other side.


Compare this to C- how many languages have C as their main, if not only, FFI?
How many languages compile down to C, or are implemented in C, or run on
operating systems written in C? You can implement system calls yourself
with a little assembly, you can compile to assembly and use an assembler,
you can use Clang or equivalent. However, in general C seems like it is the
lingua franca of computing. It is the baseline, the language level, a step
up from machine code, above assembly, but below almost all other languages.


In this way C serves a similar purpose to assembly language- just as assembly
is an abstraction that serves as a language layer underneath the world of software
and above the world of hardware, C is below the world of high level software, 
matching it up to hardware and operating system specifics and providing
some buffering from the details below.


This is a bit half-baked, but I think the idea holds up fairly well. C does
not really match hardware, its closer to being the foundation of software.

