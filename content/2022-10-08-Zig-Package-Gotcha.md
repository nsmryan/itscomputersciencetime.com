+++
title = "Zig Package Gotcha"
[taxonomies]
categories = ["zig", "pacakges"]
+++

I ran into a confusing error message in [some Zig code](https://github.com/nsmryan/zig_playground/commit/652eed6679efc07ef69d5e24daa9bae91a0f992b) today:

```bash
./src/board/map.zig:5:14: error: unable to find 'math'
const math = @import("math");
```

I found this very confusing- I'm new to using Zig packages, but I had added all
of my project's packages to my build, and my unit tests all built and passed.
Why is this particular package called 'math', which is part of this project,
now suddenly a problem?


I found the answer [here](https://github.com/ziglang/zig/issues/855), which
explains that you have to not only register your package dependencies, but
remember to register the sub-dependencies between packages.

I could see in my command line output from 'zig build' that the 'pkg-begin' and 'pkg-end' entries
where in there, but I didn't realize that they needed to specify dependencies
as well.

A good example of how to do this in a 'build.zig' file, with a neat trick on
how to specify your packages, can be found
[here](https://zig.news/xq/zig-build-explained-part-3-1ima). Looking at this
article now I see that they even specify a subdependency of 'interface' for
their 'lola' package which can be used as an example.

