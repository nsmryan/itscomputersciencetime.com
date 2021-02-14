+++
title = "The Humble Language TCL"
[taxonomies]
categories = ["Language", "Programming", "C", "tcl"]
+++


I've recently discovered the TCL language. Its a language that I've only
vaguely heard about up until a few months ago, but something compelled me
to try it out, and I've had a great time with it.


I find it to be a pragmatic, humble language which integrates into other
programs, integrates other programs into it, does not get in your way or
prevent what you want to do, and seems to have a culture of being used in
industry to solve real world problems. Even the name "Tool Command Language"
indicates that it is not the main event- its the command language you
use to get things done, such as the scripting language for software
products.


I've been writing a great deal of C recently, and trying to use mostly reliable
tools like make, gcc, bash- things I have everywhere and will continue
to work for years or decades to come, so finding a language that seems
similarly minded, and which integrates so easily with C came at a good time.


This also happened at an opportune time at work, where I had a C program that I
had developed which talks to an infrared camera. It was written to provide a
GUI or to be used on the command line as I knew it would be used in MatLab
scripts. I wanted to have a test which used this program in a script to ensure
that it would work for its users- a perfect testbed to learn a new language.


I would not say that TCL is my new favorite language, or that it is
what I would chose in many places, but it has really hit a certain
spot in my toolchain better then anything else, and I'm glad I 
took the time to learn it.


## Scripting
I knew that this program was going to be scripted from MatLab by another group,
and felt the need to have a good integrated environment to do this kind of
automation, exploratory work with plots, data analysis, scripting, etc, but I
just don't want to do MatLab. I would prefer something open source, and
something that doesn't try to take over and control your development
environment (I live in the command line), your file formats, doesn't need a
license server.


Scripting my program turned out to be trivial.  I didn't even need the
[expect](https://wiki.tcl-lang.org/page/Expect) library, I just wrote a few
functions and I was suddenly able to use my program interactively. The actual
test script was trivial at that point, and even simple thing I needed was in
the standard library.


Once I had my test written, I started to go further until I had test, data
processing, plots, interactive sessions with my device, all kinds of things.  I
have certainly used languages with interpreters before, but I think this time I
had a certain combination of factors on my side: I was developing something in
the lab (it doesn't need to be perfect), I needed to learn about the device so
the interpreters fast iteration was great, and I've been interested in reliable
programming more then fancy programming recently so TCL's slightly strange ways
didn't bother me. After all, I like Forth and Haskell, so I'm no stranger to
strangeness in programming.


I found that TCL generally had perfectly useful APIs for the tasks I needed,
with servicable binary parsing, easy integration with SQlite (which started its
life as a TCL extension), plotting, file system manipulation, everything I needed.
I'm also very pleased with how easy it is to extend TCL- it can be embedded into
a C program, but its even easier to embed C programs into it. I ended up using
[Ffidl](https://www.elf.org/ffidl/) for my C FFI stuff, but I'm confident that
I could figure out the C extension API and do things that way if I needed to.


The plotting actually wasn't trivial for me to work out, but there
are a number of libraries included in MagicSplat like Plotchart, xyplot (built
on Plotchart), and especially RBC which make this easy. It took me some time
to understand the API and examples, especially as I'm not that familiar with Tk,
but now it seems very easy to get a quick plot up while I'm working.


## MagicSplat
I started out using the tclsh in MSYS2, but it is really not good for development.
The interpreter is bare bones, debugging is very minimal- its just not a good
way to program. Instead I came across [MagicSplat](https://www.magicsplat.com/tcl-installer/index.html),
which in addition to having a funny name, is a well featured TCL distribution
for Windows. For what I'm doing, I want batteries included, so MagicSplat
fits the bills with lots of GUI stuff built in, database integration if I needed
it, Ffidl for FFIs, all sorts of stuff.

One of the things that attracted me to TCL was that it seems to have a good
story for packaging programs that you can copy to another computer and just run. There
are TclKits, starpacks, and other- its actually very confusing for a beginner.
I've had some troubles with distributing Python program recently, especially GUI
programs, so the promise of distributing programs, even with libraries and GUIs in
play, is very nice.

Here is a recent post on creating distributions for a TCL program:
https://www.magicsplat.com/blog/starpack-example/

## TCL Wiki
The TCL Wiki is a strange place for newcomers. Its a combination of documentation,
links for the community with libraries and blogs, and conversations over
years and years, many of them quite old. There are missing links and libraries
that are clearly not maintained anymore, but also a lot of very useful libraries
and extensions that solve practical problems.


There are some gems of understanding or design choices hidden away in there, and the format
actually does provide some context to libraries or discussions that a more formal
documentation system might not. Its still strange, more like a community discussion
more then a polished product, but a very useful resource.

## Commands

The core concept in TCL is commands. Everything is a command. What makes this
interesting to me is that anyone who uses a shell is familiar with commands as
the human interface to computers.  The fact that this is the normal way to
program in TCL means that you are always making interfaces for yourself to use.
Its quite nice to build up a little library to describe what you want to do,
and to be able to create your own custom little command languages to talk to
your software and hardware.


One aspect of TCL that appeals to me is that it feels a bit like Forth. There
is not much in the way of syntax, and you end up with commands which are easy
to type without puncuation. I can feel the craftsmanship of Forth, the
factoring, the programmability. I can also see how it could lead to problems
with being too dynamic or creating custom syntax that is hard to use, but I
don't intend to go down that route too far.


When I write a TCL program I have that feeling of creating a language to
express my problem. I was wrapping the VISA and SCPI instrument control
languages the other day, and I felt like I had an instrument control language.
I could then wrap this further and have an oscilloscope control language, a
function generator language, etc. Its that old, Lispy, bottom-up programming of
languages on languages. It actually makes me want to try to learn Lisp or
Scheme again.

## Weirdness

TCL is certainly not without some weirdness. The whole "everything is a
string", while not exactly true, can be both convenient and a source of bugs-
there is no way to ensure that your data has a certain form.


The upvar and uplevel stuff is clearly useful, but seems dangerous. It strikes
me as something a command language might do- it allows you do to things you
would otherwise not be able to without integrating a lot of new features into
the language, but it doesn't seem like the cleanest path. This is similarly
true with things like creating closures, which are just text strings- you can
do it if you want, but a built in or more integrated solution might be
preferrable.


Strings are the universal interface, and while they are not great in many ways,
it does make a lot of tasks easier if you can just handle a string as a list,
an array, a number, or a string as you go.


In some ways this makes TCL just a really good shell language. Bash scripting
beyond calling commands is too painful for me, but TCL fits this kind of simple
script with a bit of logic, automating a few tasks, really well.


I find I have trouble with structured data in TCL. There is struct::record, and
object systems, which are probably fine once you get used to them, and dicts.
I've generally used dicts, but I've noticed that many libraries use one
array per structure field, and use a unique name to index into the dictionary
with the field name. In general the array system seems useful, but awkward.


## Some Links
Here are a few interesting TCL links I've come across:

    * https://colin-macleod.blogspot.com/2020/10/why-im-tcl-ish.html
    * http://antirez.com/articoli/tclmisunderstood.html
    * https://github.com/antirez/Jim
    * https://wiki.tcl-lang.org/page/Sugar
    * https://chiselapp.com/user/dbohdan/repository/picol/index
    * https://zserge.com/posts/tcl-interpreter/

## TCL as a Humble Language

I now see TCL as a useful, humble language. It doesn't promise to rewire your
brain like Lisp or Haskell (although it might do this), it doesn't promise to
solve every problem, or try to control how you develop. It feels like it
doesn't need to be the center of attention- for example Python as a managed
language feels more walled off.  TCL seems like it was born in the fires of
real world problems, like it was intended to be used in anger. It lets you
change it, reprogram it, inspect it, trace or change things you if and when
want.


With all of this said, its not as if TCL is my favorite language. I have a tool
chest of languages and libraries at this point which cover my embedded systems
work, lab work, data processing, visualization, GUIs, hobby work, etc. What
surprised me about TCL was how it provided this fast iteration, accessable, and
easy environment for when I want things to be fast and easy.


Talking about programming, especially languages, is tough. There are so many tradeoffs, and
differences in values (even within a single organization, project, or within a program), which
change over time and throughout a program's lifecycle. It could be argued that TCL freedom
and the "easy" part I mentioned will eventually result in poor practices, or unmaintainable code
in the long run, or other such things. What I'm finding is the for certain tasks, especially lab work,
the values I have align nicely with TCL. I need to integrate many things, I need to talk mainly
text protocols or simple binary ones, I need to talk to C, I need to quickly automate things.
When these problems come up I am very likely to pick up TCL.


For this environment and the values I have when working this way, TCL is a good fit. I really
am having a lot of fun, and getting a lot done, with it. I could see myself getting better
and better at it, and providing a lot of value through TCL.

