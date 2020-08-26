+++
title = "What Software is Made Of"
[taxonomies]
categories = ["Language", "Programming", "Embedded"]
+++
This post is about the construction of software. This is about
the materials and process with which software is constructed.
It is mostly intuition and feeling, but there is some perhaps
practical aspects as well.


Its difficult to make physical analogies for software- they usually fall apart
quickly- but they provide a deep kind of intuition that I think still has
value.


## Materials
The materials that a program is constructed from have a lot to do
with the choice of programming language. This is not the final
say in how a program is constructed, but does set a lot of the
context and point the author in certain directions, provides
certain tools and structures, and suggests certain practices.


### Python as Legos
Python, for example, is sometimes described as lego blocks. I
think there is some truth to this, in both positive and negative
ways. It is easy to put lego blocks together, and it is easy
to put Python code together. Lego blocks stick together as long
as they are basically compatible (they have the little stud things),
and Python functions accept arguments of any type, as long as they
provide the interfaces used by the function. It is very difficult to
decide whether a large, complex lego structure will fit correctly
into another one if only using inspection- you have to try it out.
SImilarly in Python, the full list of properties a function requires
of its arguments are difficult to determine statically (they may
require understanding its dynamic behavior), so its common to
rely on running the code (such as with unit tests).


I'm not putting Python down, but rather saying that there is something
about the material of Python that seems to me that it makes a difficult material
to build large structures out of. Having to refactor large Python programs
seems like a nightmare, just like making large scale changes to 
a lego project seems like it is fraught with small incompatilibities
between structures, spacing, and alignment.

### C
C is another story. I don't know what the right material is for C, but
it has been described as a scalpel- very precise if used correctly, very
sharp if you make a mistake. This is somewhat true, although once you
need a certain level of control, C does leave some to be desired, but
the general point seems correct. When I use C, it requires discipline
and restraint. I only use certain structures, follow many rules, and
generally write fairly robust programs with a great deal of effort.

