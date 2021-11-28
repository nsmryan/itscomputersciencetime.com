+++
title = "Some TclWiki Gems"
[taxonomies]
categories = ["tcl", "programming"]
+++

Any new TCL programmer that googles around for answers will quickly come across
the strange place that is the [TCL Wiki](https://wiki.tcl-lang.org/). This is a
community site for TCL with articles on a huge range of topics ranging from individual
commands, concepts, libraries, applications, comparisons with other languages, musings-
it seems pretty free form from an outsider perspective.


My experience has been that sometimes this more sprawling, informal format is frustrating,
when I want the simple answer to a question and it is buried in discussions posted on the wiki.
On the other hand the TCL documentation is quite good and MagicSplat has a documentation index,
so the wiki is really a place for learning about peoples experiences or how they solved problems,
or links to resources.


For this post I just wanted to point out a few little gems that I have come across on the wiki:

[Simple Records](https://wiki.tcl-lang.org/page/Simple+Records) is a nice little way to create a struct
like feature in TCL.


[Thingy](https://wiki.tcl-lang.org/page/Thingy%3A+a+one%2Dliner+OO+system) is a single line struct
system. They refer to it as an OOP system, but it doesn't seem to have any properties I would
associate with such a system.

The entire definition is:
```tcl
proc thingy name {proc $name args "namespace eval $name \$args"} 
```
Which creates a namespace in which to store variables and functions.


[Cparser](https://wiki.tcl-lang.org/page/Parsing+C+Types is a small script that constructs an sqlite
database from C headers. It does depend on packages called yeti and ylex.

I haven't used this, but its kind of interesting that the entire project is simple enough to
post with explaination as a wiki page, and I think its interesting to know about systems for
parsing C code.
