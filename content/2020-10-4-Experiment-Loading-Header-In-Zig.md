+++
title = "Loading C Headers with Zig"
[taxonomies]
categories = ["Zig", "Language", "C", "Embedded"]
+++
# C Headers in Zig
I've been continuing my experimentation with the Zig programming language, and this post is an 
update on one of the things I would love to be able to do with Zig. One of the big differences
between Zig and C is that Zig can inspect types at compile time, inspecting enum variants, 
struct field names, offsets, sizes, etc.


This is a huge deal for low level programming- I have so often wanted some minimal reflection
in C so that I could take structures that define the layout of packets which other systems
must interpret, and generate some description of these structures directly from my C
headers. C simply does not have this capability, leading to the following solutions which
I have attempted to varying degrees:


  1. Generate your headers from another system, such as [CCDD](https://github.com/nasa/CCDD) or
  [COSMOS](https://github.com/BallAerospace/COSMOS) configuration files.
  2. Parse C files using, say, [pycparser](https://github.com/eliben/pycparser),
  [tree-sitter](https://github.com/tree-sitter/tree-sitter), or [cparser](https://github.com/tree-sitter/tree-sitter)
  or perhaps LLVM. I have the option to limit the contents of these structures to simple cases, like
  primitive integer and floating point types, to keep this simple.
  3. Use some kind of type information record, such as dwarf symbols. I have not had luck understanding
  [libdwarf](https://www.prevanders.net/dwarf.html), although a python library like [pyelftools](https://github.com/eliben/pyelftools)
  might be the easier option.
  4. Use C's very, very limited ability to inspect types- the offsetof macro- to manually create tables of struct fields, their types,
  and their offsets. This actually does work, and I've used the tiny lever that offsetof gives you to create several useful libraries at work.
  5. Load the C header in a language with enough reflection, and leverage that language's FFI to perhaps even provide the
  type information back to C. This is where Zig comes in.


## Zig
Okay, so lets [try this in Zig](https://github.com/nsmryan/zig_ctest). You can run this code with:
```bash
zig run main.zig -I.
```
Note that zig takes standard arguments like -I, -l, -L, etc, which is pretty nice. I believe there is a stream where Andrew Kelley discusses this.


The linked repo has a file called test.zig which declares a zig struct:
```
pub const TestStruct = struct {
    field0: u8,
    field1: u32,
    field2: u8,
};
```
with a few fields, and a C header called test.h:
```c
#include <stdint.h>

typedef struct {
    uint8_t field0;
    uint32_t field1;
    uint8_t field2;
} TestStruct;
```
which declares the equivalent structure in C.

The main.zig file then loads test.zig, iterators through the types defined in that module (in Zig, modules are turned into struct containing
a field for each top-level definition which is an interesting approach).

When we find a structure, we create one knowing its field names. This is a little hacky, and I would not do it in a final program, but this is just
for demonstration.

## Zig and C
After all of this nice compile-time introspection, we attempt the same thing with the test.h file included as ctest. See the try\_iterate\_cimport
function for my attempt, but essentially what I found was:

    * Imports are struct with no fields, but with a 'declarations' array containing the included definitions.
    * The declations for a Zig module work just fine, but any attempt to access a cInclude's declarations, even printing them, is a segfault
    with a TODO. I haven't found the location of this segfault, but clearly these are not representated uniformly with respect to the Zig modules.


## Conclusion
Given that this desire to inspect types appears to work well for Zig types, and in fact in other tests appears to work well if you already know the
names of the types you want to access, I think Zig will eventually be capable of this kind of reflection. As of 0.6.0 however, this is not possible.
Perhaps with the advancement of the stage2 compiler, these things will become more uniform.


I may continue to try this out by accessing types that I know are there, rather then attempting to do with this no prior knowledge. I will try this
again in future versions of Zig as it really is a big deal (I don't think I've really expressed how great this would be for me).


Oh well, Zig is young, and I know my experiements will occasionally result in dead ends!
