+++
title = "Replacing C"
[taxonomies]
categories = ["Zig", "Language", "C", "Embedded"]
+++
# Replacing C
This post contains thoughts using C as one's primary programming language, and what
attributes would make a language a suitable replacement for C. I will try to express
why I use C, and what I would need and what I would want from a replacement.


Its important to start by establishing that I would like to replace C- I use it out of
practical concerns, and to a lesser extent a hard-won appreciation for it as a
kind of C apologist. I use C because it has technical and social aspects to it that,
given my best judgment in choosing my tools as an engineer, I find that it is the best
tool for my job. This is a very complex topic, and I will not be able to do justice to
it here, but I will try to touch on some aspects.


This will not be just able adding features- it is easy to say that C needs generics, or
lambdas, enums (sum types), or even OOP. What I'm hoping for it a language that remains
simple and understandable, where control flow is, at least mostly, statically known, where
most code contains very few concepts.


This is a delicate balance between power and determinism,
abstraction and control. Every feature has a cost, and too much power is, for
some problems, as much of a problem as too little.


## Why Use C in the First Place?
My professional programming experience has been at NASA Langley Research Center, where I have
worked on a science instrument that is currently attached externally to the International
Space Station (SAGE3), a high-assurance, independent UAV geofencing system (SafeGuard),
a research level robotic arm for in-space assembly (TALISMAN), and I am currently
working on my first CubeSat project (ARCSTONE), as well as a few other things here
and there.


There are not all that many choices in this arena. Many languages that want to
inhabit the "safer systems language" area of the programming language landscape do
not survive for long (being a new language is hard), and its very rare for one to
reach escape velocity. I have been interested in Rust for exactly this reason, and
while I enjoy Rust, and I'm glad to see it take its place in this landscape. However,
I would not say that it actually meets the full list of things I would want from a
safe systems language. Its amazingly close- closer
than it would seem given that it was designed to write a web browser, not embedded
systems, but its not perfect.


I have used C and C++ for these systems, as well as a host of other languages for tools,
data processing, and ground system software. For onboard systems, however, there
are only so many options. You can use C, a language with almost
no means of abstraction, you can use C++, the immense, complex, powerful language
with plenty of footguns, or Ada, which appears to be far
safer than the alternatives, and to have features designed for high quality
software that are far more difficult or impossible in C/C++. There are others-
ATS comes to mind, perhaps FORTH, and a variety of languages very similar to C that are
designed to remove some of its issues (Checked C, C2, C3, etc).


Ada is interesting in many ways, and probably deserves better treatment then I can give it.
It seems to have a cultural and perhaps marketing problem- even with what appears to
be an attempt to make the language more appealing, it doesn't seem to gain traction
in most sectors. I expect a lot of this is just people going with what it known
and more commonly used (easier to find people, APIs already available, etc),
and a lack of willingness to pay the upfront cost of
investing in Ada even if it might pay off over time. I wish I could do a better job
with the pros and cons of Ada, but my current feelings are that I want as much
verification and safety as I can afford (in time, money, mental investment, etc),
and so far Ada's investment cost has outweighed the benefits I am able to
imagine (I admit I could be completely wrong).


I want to make sure to mention Zig here- haven't written enough of it to make
an informed decision, but it has a lot of promise along the lines that I'm talking
about here. 


I recently had the chance to take over an embedded software system, and design
and implement it as I saw fit. I found that I did not use or want almost
any features from C++ (except perhaps reference parameters, occasional
default parameters, and the extra type checking) so I converted the codebase from
C++ to C. I am very happy with the change, and I think it exemplifies
why I use C. 

  1. C's limitations in abstract are grating when the benefits of abstraction
  are high, but when the abstraction level of C itself is approximately the
  level that is needed to solve your problems, the inability to raise this
  level keeps things simple.
  2. I know that there are no memory allocation behind my back, as I 
  3. I can parse the code easier with external tool. C++ is particular bad for this use.
  4. I can generate code without too much trouble.

## Why Replace C in the Second Place?

## Why Don't We All Just Use Assembly
If the level of abstraction is an issue, a common retort is to say that maybe
we should all just use assembly. This is not 

## Requirements

### Control Over Memory Layout
Its okay if memory layout can be left to the compiler, but I want to be able to
control this myself as well.


Currently explicit layout of a struct is is only possible in C with compiler
extensions like "__attribute__((packed))" or "#pragma pack(1)". I would like
to have a standard way to control packing, at the very least.


Standard bitfields are another must. They should control load and store bytes that
are needed, and should be standardized so different compilers and architectures
do not suddenly break your definitions.

Tied in with all of the above is some kind of help with endianness. This does
not necessarily have to be within type definitions- perhaps on variables, or
accesses- but it would help a lot to have a way to control endianness. Even
something like a generic byte swap function that works on a subset of types
where an endianness swap is well defined. I believe a function like this
could be written in Zig, which is intreguiing.


One potential aspect of memory layout would be a way to tag types or variables as
being "hardware" memory vs RAM memory. This is somewhat like 'volatile' in C,
but potentially with architecture specific capabilities like ensuring that
accesses to those addresses occur in order to ensure the hardware's semantic, or
a way to describe those semantics. Clearly I don't have this fully worked, but
its also likely the most marginal of these ideas.

### Restricted Allocation/Free
In embedded work, its common to restrict the use of memory allocation. I would
like to be able to specify when allocation is allowed, perhaps finely (per function)
or perhaps coursely (per file, say), or perhaps both. Either way, I would
like to know for sure that allocation was controlled, and not have to
manually check for malloc/calloc/free.


This is an area that Rust has almost the same problem as C++, where it is not
so easy to check for allocations. Rust does have no\_std, but it would be
nice if the 

## Desirements

### Constant by Default
I've been using Rust for several years, and I've found that the choice of
constant by default for variables is better then mutable by default. It
restricts the set of variables that I have to think about within a function,
and people never actually mark constants 'const' in C.

### Memory Aliasing
It would be nice to have more restricted potential 

### Slices
It would be a nice-to-have to be able to group an array as a pointer, size,
and length in a generic way, potentially requiring language support.

This would reduce the complexity of a lot of C code, where the array and size
information are related, but there is no way to say this except to create
a structure which either 1. uses void\* and requires casting, or 2. needs
to be defined for every type you want to use. The second can be done with
macros, but I would prefer to avoid the need for complex preprocessor macros
if at all possible.


