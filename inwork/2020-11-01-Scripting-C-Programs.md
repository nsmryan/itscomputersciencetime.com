+++
title = "Scripting C Programs"
[taxonomies]
categories = ["Language", "Programming", "C", "Lua", "forth", "tcl"]
+++


I've been thinking about how to improve the use of the programs I write at
work, so this post is an in-progress update on some thoughts on writing tools
and interacting with diverse hardware and software systems used by multiple
people in multiple configurations over a period of time- clearly not a trivial
problem, but one that I run into fairly regularly.


For some context- I work with embedded systems, many of which are custom
(detectors, controllers, systems for prediction and execution of a scientific
task such as a series of data collections or a calibration). Some of these are
part of lab setups or testing configurations, while other systems are flight
systems meaning that they are operated in the air or in space. I am not going
to talk much about flight systems in this post- this is more about how the
development, testing, and support role of software in a research context where
new systems are being developed by specialists in other fields, and where
software is needed to facilitate their work. In this case, they may not be
programmers, or they may be capable of some programming, but usually they do
not have a wide or deep view of software and need help creating systems that
fill their needs over of period of time (usually several years, but perhaps
much longer).


## Approaches
I've used several different approaches over time for this kind of task.
I've worked with someone that uses all MatLab scripts, I've written my
software in Simulink so a control theorist could integrate with it
directly, I've work in pure C for everything, and I've work with
frameworks with their own configuration and scriptings (Ruby in one,
a limited custom language in anther). I've also done some scripting in
Python, and certainly written code in many other languages for
processing, creating configurations, visuals, etc.


Currently I am usually using a mixture of C in a Unix environment 
(under Windows my necessity, so MSYS2 and sometimes Cygwin) for
most tasks, using a variety of small libraries to boost productivity
of my own design or liberally licensed open source. For GUIs
I often use LabWindows because it is an excellent environment,
I can create GUIs very quickly (I've used it enough that its
quick), and it has support for equipment that I sometimes need
to interact with. The LabWindows APIs are cleaner than Windows
ones, and it simple APIs for sockets, data structures, files, threading,
queues, etc that, while not always perfect, and functional.


C is good in this environment because it is the native language
of many of the devices I use, and despite its limitations, I can
do things that I need like lay down precise memory layouts, perform
tasks quickly, or talk to hardware directly. Other languages
very often has an impedance mismatch between the language and 
memory/hardware, which is a constant barrier to cross. Other
languages that do not present this barrier would be Forth, Zig,
and to some extent Rust (although that is a somewhat lacking capability
for Rust in my current experience). I don't like having additional
moving parts, so removing this barrier is important, the lack of
garbage collection, and just in practice the fact that 3rd party
hardware often has a C interface that would need to be wrapped to
use in another language, leads me to use C in most cases.

## Limitations
However, this is not ideal in several ways. I can make GUIs with LabWindows,
and there are distribution options, but you ultimately need the NI CVI runtime
installed to use them. This is not a deal breaker. The worse problem is that
you need a license to develop with the environment, and only a few people I 
work with have a license. The other significant problem is that while C
is good when it matches the level of your problem, when you have larger
problems, or goals that change quickly, it can be a burden.


There is a flexibility problem as well- if you have multiple devices, do you
make a single application to control them all, or individual applications?  If
you make individual ones, they might need to be commanded or included in other
applications in some way. This is all possible with some foresight, but I think
we can do better. I've gotten to the point where I can write applications that
can be used a libraries in larger applications, and can present a CLI or GUI,
allowing scripting or human use, but this is not yet enough. I think this
process could be fleshed out to make it repeatable, but I think there is a
better way then following this path much further.


## Desirements
To try to get at what I really want here.
I want to have a 'preferred' set of tools for when I have control over my
environment, while being flexible between projects where I have only partial
control.


I want to talk to hardware directly,
to encode binary protocols, use 3rd party hardware and APIs, and talk the
native language of my own embedded devices (C).


I want to make GUIs and command line tools, sometimes short running,
sometimes long running. Sometimes these need to do some statistics, create
various images, and provide live 'quicklook' visuals.


I want to be able to add pieces to these systems, and yet to have it
come up and close down easily. Part of this is that I often don't need
the same features between projects, and I don't want to carry around unused code.
The pieces would ideally be disposable. I'm not sure what parts are not- 
perhaps some kind of message passing core, or perhaps literally nothing.



I don't want to rely on anything too large, or that locks me into a language or
OS ecosystem, as I often need to switch technologies on different projects.


I want software that is reliable- I want it to work the same in 6 months, and
after years.  I want to be able to establish my development environment on
different systems, Linux and Windows. I also want to be able to distribute my
software to other people's computers, as well as lab computers, without a huge
ordeal, licenses, or even, ideally, separate runtimes. This is all just ideals,
but it has influenced my choices recently- if I program in plain C, often with
cross-platform libraries, I feel like I can trust my software to a certain
extent. If I need an extended environment I take on the LabWindows requirement,
and get a "good enough" setup, although lacking flexibility and speed of
change, and accessability to my collegues except 1. other software engineers or
2. through GUIs.


Python approach, talisman approach, trying to generalize

## Some Reusable Pieces
Limit monitoring with red/yellow/green/blue limits. This does not
need to display limit information, or even act on it, as long as
another component can do this. My current plan for this would be to 
write a component in C for flight software, and re-use it in ground
software.


Another piece is some kind of current and historical data retrieval for
display and analysis.
