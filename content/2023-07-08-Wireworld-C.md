+++
title = "Wireworld in C"
[taxonomies]
categories = ["C", "tigr", "wireworld", "Programming"]
+++

I stumbled on [Wireworld](https://www.quinapalus.com/wi-index.html) recently.

I've been playing with [tigr](https://github.com/erkkah/tigr) recently, which is a lot of fun.

Combining these is an implementation of [wireworld in C](https://github.com/nsmryan/wireworld)
which can be used to paint circuits with the mouse buttons, stop and start the simulation
with space.


I have played around with implementing electron generators and a few gates but not much else.


Wireworld is an amazingly complex system given that it is a cellular automata with just a handful of
simple rules. I was able to implement this in less than an hour with tigr, and with another hour I had
worked out how inputs works and added mouse and key controls, and played around with making some circuits.


I'm a little curious how the computer they discuss in the Wireworld article would run on a modern computer,
and how it could be optimized.


If you want to play around with my project, just build using 'make' and run with 'make run'. It should work
on Windows, Linux, and MacOS since I just reused the tigr example Makefile.

