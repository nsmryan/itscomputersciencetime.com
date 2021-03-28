+++
title = "Forth in Anger"
[taxonomies]
categories = ["Language", "Programming", "forth", "work", "embedded"]
+++
I have had a soft spot for the Forth programming language for at least 10 years
now. One of my favorite personal projects was writing an implementation of
Forth in Z80 assembly for my TI-83 calculator, complete with an interpreter,
loading files, and drawing on the screen.  I've done Arduino projects with
Forth, Advent of Code problems, and other projects, but I wouldn't say that
I've really used it "in anger" before now.


I have recently been thinking about the iteration speed of my embedded
systems development, and I've been able to get some good returns using
TCL on the host side and Forth on the target side. I repackaged
[pforth](https://github.com/philburk/pforth) so I could statically link
it, complete with an initial Forth system as text in the binary,
and then link in my embedded software as another static library.
Then I can use functions from my software at the interpreter, as
long as I do the work to expose them. I also redid the way
that C functions are exposed to pforth a bit to make this easier-
there is nothing wrong with the way its done normally, but I 
wanted pforth to be part of my program, not the other way around.


What all of this made me think about was that for the first time
I was using Forth to solve an engineering problem. Something
messy and real-world where I just needed results in the lab.
I was curious if I would find Forth useful in this context,
or if I would find it a problem.

I'm happy to say that for the problems I actually faced, I found
Forth to be perfectly useful and fast. I could add functions to 
test my hardware, try things out again and again until I got
them right (due to the fast iteration speed), and even do
the little tasks like print out some memory or compare values within
collected data with no problem.


Part of this is being fairly familiar with Forth, and part of
it is that Forth is good at the kinds of things I wanted to do-
talk to hardware, inspect memory, string together functions in
different ways quickly. The other part is just Forth's style of
being factored into small pieces (one line usually), and combined
upwards into useful functionality. I could break the small
problems I faced down quickly, test things out, and build
up what I needed. I can't say for certain what I would have
found for larger problems, but I didn't actually face those problems,
so I won't comment.


The moral of the story here is that I'm very pleased with the results,
and I plan on continuing to keep this Forth build of my software for
iterating on logic, for talking to hardware, and to ensure that
my software is factored well enough to use it as a library if
necessary.


While doing all of this I did a survey of language implementations that
where available, including TCL, JimTCL, Lua, pforth, libforth, zforth,
picol, and more. I hope I can write up some thoughts on all of these
some time, as they are all points within this design space with some
interesting tradeoffs in completeness, complexity, portability, standard
library size, memory use, etc.

