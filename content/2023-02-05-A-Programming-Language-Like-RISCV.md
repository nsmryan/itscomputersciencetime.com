+++
title = "Programming Language Design in Extensions"
[taxonomies]
categories = ["C", "Zig", "Programming", "RISCV"]
+++

After reading the [RISC-V instruction set reference](https://riscv.org/wp-content/uploads/2019/06/riscv-spec.pdf)
I wished for a programming language with a similar design.

What I mean by this is a programming language designed with a 'base' set of
functionality, along with a series of 'extensions', and perhaps layers, of functionality
that can be added.

This post tries to explore this idea a bit. Note that this is not novel- clearly RISC-V and
other ISAs are doing this, and languages like [Ivory](https://github.com/GaloisInc/ivory)
use an effect system that seems to achieve a similar result. What I would like to see would be something
like C or Zig designed this way.


## Base Level

The base level of a language would include something like functions with primitive types (u8, s16, f32, f64, etc),
arithmetic, assignment, bit shifts, and bitwise operations. Already there are some considerations to note, such
as integer overflow. I think the play here would be to either make these situations well defined, make sure
the programmer can detect them, or provide a runtime that has options to trap them. I would prefer the first
so that the language is always fully defined, but I realize that some things are left to the platform in case
the underlying ISA doesn't handle them the same way, or additional instructions have to be emitted to ensure
something. I would prefer determinism here over performance.


The base language may also include basic types like enums and structs, and perhaps tagged unions. I suspect
it would be better to omit untagged unions to avoid memory weirdness like reading trap floating point values,
but perhaps this is not necessary to restrict if you can ensure that these structures only include a small number of
types.

Going even further, perhaps the base language should omit floating point entirely and only allow integer types.


Note that this does not include global variable access, allocation, or pointers. I think that these would not be 
available in the base language, although I'm not positive. Its possible that for a language like Zig,
some pointer types would be available and not others: slices and Zig pointers, but not raw pointers
and maybe not sentinal pointers for example.

I imagine that all code would be in the base language, and the programmer could try to fit
as much code in this layer as possible. Perhaps files or functions could be annotated to indicate
that they do not use extensions, and the compiler could statically check this.


Its possible that such strict contraints could lead to better code generation, as the compiler
could ensure that a range of situation does not occur, but I have not proof of this.


There may need to be some other aspect to this, like 'extern' and section information to allow
linking. I haven't thought that through at all.

Also, certain mechanisms like Zig's optional and error systems seem like they could be part of the
base language. I'm not sure I see much value in omitting them except for perhaps ABI reasons.


## Extensions

There would have to be a large number of extensions, and I imagine a "full" extension that covers
all language features. Examples of extensions could include:

```
	1. Floating point numbers, if not in the base language.
	2. Global variables access.
	3. Pointers, especially splitting out raw pointers or pointer casts from the base language.
	4. Allocation.
	5. I/O, perhaps broken down more finely, although it seems hard to classify without a language
	   mechanism like an effect system.
	7. For Zig, comptime. This would be forbidden from the base language to ensure that 
	   programs written specifically for this base language remain simple and reviewable.
       For C, this would be the preprocessor.
	9. Inline assembly, FFI, ect.
	10. More complex types like untagged unions and packed structs may need to be in an extension.
```


## Conclusion

Clearly I haven't thought this through all the way, but the idea does come from a experience. When
I write embedded systems code I wish I could express more restrictions then I am able to- I want
to be able to say that no part of a codebase, or a module, including the use of libraries, makes use
of a certain concept. Currently I have no way to do this except by inspection.


I would also love to have a language where the definitions, like C header files, cannot use complex
syntax. Perhaps this includes restricting nested definitions, anonymous definitions, preprocessor
use, etc so that its simple to write a parser for the language and extract definitions from source.
There is an effort to make extracting type information simpler for C binaries called
[CTF](https://lwn.net/Articles/795384/)


There are some other options besides language design that might help here- 'house rules' extensions
to the compiler or an analysis stage that ties in which checks certain conditions, or a compiler
that allows the user to track its actions (Jai seems to have this?). However, the RISC-V strategy
seems like a nice, formalized way to get this to happen.

Obviously this is all half baked, but I prefer to have thoughts down in blog posts then swimming in
my head.
