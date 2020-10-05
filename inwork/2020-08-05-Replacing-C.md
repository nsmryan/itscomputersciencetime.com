+++
title = "Replacing C"
[taxonomies]
categories = ["Zig", "Language", "C", "Embedded"]
+++
# Replacing C
This post contains thoughts using C as one's primary programming language, and what
attributes would make a language a suitable replacement for C. I will try to express
why I use C, and what I would need and what I would want from a replacement.


Its important to start by establishing that I *would* like to replace C- I use it out of
practical concerns, and to a lesser extent a hard-won appreciation for it.
I use C because it has technical, engineering and social aspects to it that,
given my best judgment in choosing my tools as an engineer, I find that it is the best
tool for my job. This is a very complex topic, and I will not be able to do justice to
it here, but I will try to touch on some aspects.


This will not be about adding features- it is easy to say that C needs generics, or
lambdas, real enums (sum types), or even OOP. What I'm hoping for it a language that remains
simple and understandable, where control flow is, at least mostly, statically known, where
most code contains very few concepts. There are plenty of programming languages with
advanced features, and that is great, but I think we also need ones without those features
to do certain jobs.


This is a delicate balance between power and determinism,
abstraction and control. Every feature has a cost, and too much power is, for
some problems, as much of a problem as too little. With great power comes
great responsibilty.


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
systems, but its not perfect. This has led me to wonder what kind of language
*would* be perfect- not because I expect such a language to come along, but
so that I can at least judge languages based on some criteria.


I have used C and C++ for these systems, as well as a whole host of other languages for tools,
data processing, and ground system software. For onboard systems, however, there
are only so many options. You can use C, a language with almost
no means of abstraction, you can use C++, the immense, complex, powerful language
with plenty of footguns, or Ada, which appears to be far
safer than the alternatives, and to have features designed for high quality
software that are far more difficult or impossible in C/C++. There are others-
ATS comes to mind, perhaps FORTH, and a variety of languages very similar to C that are
designed to remove some of its issues (Checked C, C2, C3, Embedded C, etc).


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
imagine (I admit I could be completely wrong). I would also say that Ada does not
appear to be a minimal language- it seems to include more advanced constructs then
C for better or for worse (again, I truely don't know if its better or worse).


I want to make sure to mention Zig here- haven't written enough of it to make
an informed decision, but it has a lot of promise along the lines that I'm talking
about here. It will be a long while before it is mature enough to compete with
venerable C, but its quite interesting so far.


I recently had the chance to take over an embedded software system, and design
and implement it as I saw fit. I found that I did not use or want almost
any features from C++ (except perhaps reference parameters, occasional
default parameters, and the extra type checking) so I converted the codebase from
C++ to C. I am very happy with the change, and I think it exemplifies
why I use C. 

  1. C's limitations in abstraction are grating when the benefits of abstraction
  are high, but when the abstraction level of C itself is approximately the
  level that is needed to solve your problems, the inability to raise this
  level keeps things simple.
  2. I can control memory allocation easier then other languages, including C++ or Rust.
  3. I can parse the code easier with external tool. C++ is particular bad for this use.
  4. I can generate code without too much trouble.
  5. I can keep things consistent and keep a subset of C without having to make a huge number
  of decisions and reviewing code constantly for violations. Again having fewer features makes
  creating a subset for a particular problem easier.
  6. Almost all problems are solved in about the same way, helping with readability and review.
  If you want to solve a problem, you have structs, enums, #defines, variables, functions, and
  code constructs like 'while', 'for', 'do', and 'if', and thats about it. There just isn't that
  much choice.

## Why Replace C in the Second Place?
If I find C to be the best choice of language currently, why replace it at all? Just because
I choice to use C doesn't mean it doesn't have its share of issue. It is full of sharpness, 
hard to detect undefined behavior, weirdness. It takes a lot of expertise to keep
it manageable, and you always have to be on the lookout for subtle problems. Its full
of weirdness and constructs that are rare and could probably be just removed if not
for the need for backwards compatibility. There are things that I just wish were
easier- if packing structs was part of the language instead of incompatible extensions
in different compilers, if bitfields were portable, if perhaps endianness were easier to
deal with.


I can understand the C++ communities interest in building over top of C to create
abstractions that do not allow for C's problem. This is a complex issue, but I have
preferred to stay with C's finite set of issues instead of building abstractions with
new and exciting issues, but this strategy accepts the need for constant vigilance
in my own code and all code I use.


A small increase in capability might result in huge gains compared to C- for
example I could use a little more reflection then afforded to by C. 


C also does not have the ability to express many of the things one knows about a program-
I may have a structure containing pointers to a buffer, but have no way to express a concept
such as 1. this pointer should not be freed because it is part of another allocation,
or 2. this pointer should not be used after passing it to a certain function because
its memory is now controlled by a library, 3. a memory area may not be initialized, but it must
be by the time a certain function ends. In other words, some of the things that Rust helps with
issues caught by Rust, undefined behavior or memory sanitizers, or Zig, or perhaps something like
[Frame-C](https://frama-c.com/), but perhaps concepts that cannot be expressed in any of them.


## Why Don't We All Just Use Assembly
If the level of abstraction is an issue, a common retort is to say that maybe
we should all just use assembly. This is just too much of a simplification- sometimes
assembly *is* the correct level of abstraction, but not for most problems. Sometimes
C is at the right level of abstraction, but not for all problems. Just because
it is rare to find problems in one's own domain where these levels of abstraction
are appropriate doesn't mean that they don't exist.


I guess the point here is that I want to solve certain problems with a certain level of
abstraction that I find appropriate with as little power as possible.

## Requirements
I will try to lay out some of the things I would want in this mythical language that replaces C.

### Control Over Memory Layout
There are a number of different situations we may have in a low level language- we
might want to leave memory layout to the compiler (perhaps to lay out in an efficent manner),
we might want to control the layout ourselves (to talk to hardware or another system). There
might even be other concerns, such as wanting to ensure a certain struct has no padding,
or wanting a field order regardless of padding.


Currently explicit layout of a struct is is only possible in C with compiler
extensions like "__attribute__((packed))" or "#pragma pack(1)". I would like
to have a standard way to control packing, at the very least.


Standard bitfields are another must. They should control load and store bytes that
are needed, and should be standardized so different compilers and architectures
do not suddenly break your definitions. Ideally we could also control whether the
fields of a bitfield pack least to most significant bit or most to least, but if we
only have one direction it should be consistent.


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
nice if the language had the option to mark certain functions or files with
information about whether they could allocate, and to check this statically.

## Desirements

### Constant by Default
I've been using Rust for several years, and I've found that the choice of
constant by default for variables is better then mutable by default. It
restricts the set of variables that I have to think about within a function,
and people never actually mark constants 'const' in C.


Zig's answer is also fine- neither const nor mutable is the default, and you
have to specify what you want in all cases.

### Slices
It would be a nice-to-have to be able to group an array as a pointer, size,
and length in a generic way, potentially requiring language support.

This would reduce the complexity of a lot of C code, where the array and size
information are related, but there is no way to say this except to create
a structure which either 1. uses void\* and requires casting, or 2. needs
to be defined for every type you want to use. The second can be done with
macros, but I would prefer to avoid the need for complex preprocessor macros
if at all possible.

### Language Levels
I have this feeling that a good C replacement would have the option to enable or
disable features within some unit- function or file most likely. This might come
in the form of increasingly featureful "language levels" that enable things like
1. accessing memory buffers, 2. accessing global variables, 3. allocation,
4. deallocation, 5. any kind of stack-unwinding feature involved in the language
design, 6. use of advanced features depending on the language (traits or whatever
the language has), 7. use of pointers. The idea would be to restrict as much
software as possible to the lowest level possible and to be able to perform
more strict checking on these subsets.


### Simple Parsing and Analysis
I would like to use a language where new checked, static analysis, etc was easier
then C and much simpler then C++. Fewer corner cases, perhaps parsing built in
to the language, or language level access to a program's own code might do it.
The idea would be to enforce in-house rules such as that certain functions must
be deterministic, or allocation is only allowed at startup, or that
functions in a certain file do not access memory through pointers.


The idea is that there is a lot of checking and correctness left on the table in
C and other languages that I would like to express.


## Conclusion
There is probably much more to say about all of this, but I figured I would just post
this and maybe come back some other day. For the near and mid term I expect to continue
with C, hopefully mitigating its issues, and to monitor Rust and Zig in case they 
are close enough to my ideal to supplant C in this space.
