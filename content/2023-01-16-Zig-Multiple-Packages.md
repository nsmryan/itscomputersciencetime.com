+++
title = "Zig Multiple Package Projects"
[taxonomies]
categories = ["zig", "Programming", "testing"]
+++

I've been writing a lot of Zig recently, and trying to keep my code in small, focused files
in small, focused modules. I find that if I'm not careful my code becomes spralling very quickly,
and progress slows to a crawl. I also very much like knowing what code depends on what other code,
and minimizing these dependencies so that, for example, most of my codebase for my game is not dependent on
SDL2 at all, and it is clear where the SDL2 dependence starts and ends.


Zig provides a lot of flexibility in file grouping. This is one of the things I
like about Zig- it feels like it gives you mechanisms and lets you decide what
to do with them. For example, the Zig standard library creates a tree of
imports where the 'std.zig' file imports each standard library component, which
are separate files, which each may import files from a directory of the same
name as the component. This creates a kind of 'top level' interface to the
standard library through 'std.zig', a second layer through the \*.zig files,
and a third 'within component' layer of directories with the same name as
'.zig' files which are not imported directly except by the cooresponding '.zig'
file.


Another mechanism that Zig provides for grouping code is a package consisting
of a root '.zig' file which can include other '.zig' files in the same
directory or subdirectories of itself, as well as a list of dependencies on other packages.
I had done something similar in my [Rust
Roguelike](https://github.com/nsmryan/RustRoguelike) codebase which is split
into multiple 'workspaces' which are individual crates. In [my Zig
version](https://github.com/nsmryan/zig_playground) I wanted even more fine
grain separation, so I split things up into small packages.


This post has some notes on this design, including how to test a multiple-package project in Zig.
Note that this is about multiple packages within a single project, not about including packages with
a package manager. I'm currently vendoring my dependencies without using a package manager.


## Package Architecture

I have split up my Zig codebase into packages, even though normal Zig files would also work with a similar strategy to the
Zig standard library. The reason is that I prefer to explicitly define the dependencies between parts of my codebase, and I like
the idea of being able to package these components up one day as for-real Zig packages when the package manager exists.


The code is split into directories, each of which is a package. The packages each contain at least one
Zig file with the same name as the directory, acting as the root file in the package. This file may contain code or types,
but primarily it contains references to the files in the package. I've chosen to not reexport all of this sub files with
'usingnamespace', instead I just reference them by name so I have to be explicit about the location of definitions.


The build.zig file then has to describe the dependencies between packages with 'step.addPackage'.


I admit I'm not completely sure that this is better then just importing Zig files and being careful to keep them
organized, but I do like the explicit dependencies and the conceptual separation into packages.


## Testing Multiple Zig Packages

One problem I came across when trying to split Zig code into packages was how to get 'zig build test' to actually
run all tests within a package. Part of the problem is that Zig does not run package tests by default so that you
are not running transitive dependencies tests when you just want to run your own code's tests. The other difficultly
is Zig's lazy compilation strategy, where just having tests defined does not mean they get run without extra work.

This was surprising to me- the 'test' keyword is built into the language, but I was not seeing my tests run,
despite trying a number of different designs.


Ultimately what I came up with was this:

    1. Create a top level 'test' which imports all packages, such as:
```zig
test {
    _ = @import("src/math/math.zig");
    _ = @import("src/utils/utils.zig");
    _ = @import("src/board/board.zig");
    _ = @import("src/core/core.zig");
    _ = @import("src/drawing/drawing.zig");
    _ = @import("src/engine/engine.zig");
    _ = @import("src/gui/gui.zig");
}
```

    2. Within each package's root file, include all files within the package.
    3. Within each package's root file, add the following code:
```zig
comptime {
    if (@import("builtin").is_test) {
        @import("std").testing.refAllDecls(@This());
    }
}
```

This final step was due to someone on Discord answering a similar question to mine, but I don't recall
where I found the code itself. 
 
 
The concept is that for test build, we use the standard library function [refAllDecls](https://github.com/ziglang/zig/blob/ae69dfe6e739b1b2d4ae76923d04bc69c23b07fa/lib/std/testing.zig#L870)
which ensures that Zig's lazy compilation actually makes use of each file and therefore
finds the tests within them.

This is one of those situations where Zig's lazy compilation process, while I think I understand
why it is important, does seem a bit weird to me. At least as someone newish to Zig it created
a very difficult to solve problem with my code's package organization which tool a lot of digging to figure out.


Note that this also works:
```zig
test {
    @import("std").testing.refAllDecls(@This());
}
```
but it introduces a new test, which means that the number of tests run is a little bloated and doesn't reflect
just the useful tests you have written. The code is simplier, but I prefer to not have these 'extra' tests.


