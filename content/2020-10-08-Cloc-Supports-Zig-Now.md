+++
title = "Cloc Supports Zig Now"
[taxonomies]
categories = ["zig", "cloc", "github"]
+++
The source lines of code tool [cloc](https://github.com/AlDanial/cloc) now supports the
[Zig](https://ziglang.org/) language.


I was writing a little Zig program and wanted to get a line count, and looking around
there are indeed some SLOC (source lines of code) tools that support it. However,
I usually use cloc since we have standardized on this tool at work, so I figured I would
see what was necessary to add a language.


It turns out to be very simple- I added Zig myself as a test and only had to add a few
lines here and there to note comments and things- but then I made a
[github issue](https://github.com/AlDanial/cloc/issues/523) asking
if Zig could be added. It turns out that there is a
[standard practice](https://github.com/AlDanial/cloc#requesting-support-for-additional-languages-)
for asking for new languages which is very easy. The maintainer responded quickly and
in a day or so Zig was added to the master branch.


This was a very quick an simple thing, but I'm a little proud for making it happen.
I also learned to check a little deeper before making an issue in case there is 
documentation on how to submit a particular request.

