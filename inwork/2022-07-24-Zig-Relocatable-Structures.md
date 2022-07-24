+++
title = "Relocatable Structures in Zig"
[taxonomies]
categories = ["Programming", "zig", "memory management"]
+++

I have a new small Zig project: https://github.com/nsmryan/zig_sealed_and_compact.

This is an exploration of memory management in Zig. In this case the repository contains functions for
making structures relocatable in memory even if they contain pointers.

The motivation here is that I was thinking about things I would do in the [roguelike](https://github.com/nsmryan/RustRoguelike) I
have been working in, but that I don't know how to do in Rust.

In this case, I like the idea of keeping my main game state in a single contigious buffer using a
[stack/bump/fixed buffer allocator](https://ziglang.org/documentation/master/std/#root;heap.FixedBufferAllocator) and perhaps occasionally copying it between two buffers as a 
kind of garbage collection scheme to fill in reallocated spaces that the fixed buffer allocator cannot reuse.
This is similar to a copying garbage collector like Haskell's, but simpler and without any metadata that an
actual garbage collector would require.

The other thing I would want to do is to just take this buffer and copy it so I can restore a previous game state,
or to save it to a disk as a kind of trivial save game concept. For this to work, the pointers within the buffer
should not point outside of the buffer, and they should be updateble to the current location of the game state if
it has been moved, copied, or reloaded in a new instance of the executable.


The idea of pointers that do not point outside of a closed system of structures came from the Haskell library
[compact](https://hackage.haskell.org/package/compact). The idea of then making pointers relative to the start
of the buffer is just the concept of a relative pointer (see [this discussion](https://www.gingerbill.org/article/2020/05/17/relative-pointers/)).


## Implementation

The implementation consists entirely of type level comptime programming. We just traverse the structure of the
given type, and in some cases the value as well (such as to get a slice's length), and update or reallocate fields.

I used [this page](https://ziglang.org/documentation/master/std/#std;builtin.Type) about the Type type to figure out
how to inspect a type, and the [main documentation page](https://ziglang.org/documentation/master) for builtin functions
like '@ptrCast', '@alignOf', '@bitCast', etc.

For the 'compact' function, this just means using the Allocator function 'dupe' and following child types for arrays, pointers, and optionals, and
looking through fields of structs and unions, and searching for further allocators. When an allocation is found 
(a pointer type), it is replaced with its duplicate and recursively searched for further allocations.


For seal and unseal, instead of duplicating allocators, we recalculate their pointer values. The pointers become relative
offsets to a given location (say, the start of a buffer in which the memory is allocated). This results in pointers that 
are invalid, but does not require changing the structure- the same memory for the pointers is reused for these relative
pointers.

One side note- Zig did not like me doing this when a pointer pointed to the start of the buffer, resulting in
a 0 valued pointer. I just added 8 to the relative offsets to put them at a nicely aligned location that was not 0, which seems
like a hack and could, in principal, result in an integer overflow at the end of memory, but seemed like a reasonable
compromise to me.


Some types where easier then others- optional took me some time to figure out in particular. Trying to make sure I had
the right pointer to the right location resulted in a number of crashes, some seemingly more severe then usual.

The main interesting thing with structs was getting access to a field by name using '@field", which allows you to
generically traverse a structures fields using its '@typeInfo' and field names, and then get field values or pointers to
fields with '@field'.

Unions are an interesting case- while with a struct you have to inspect every field, with a union you only have to figure
out which field is active and act on only that one. This is as simple as looping through fields and checking their names.
Untagged unions are ignored if they could contain a pointer- we can't tell if they do or not, so they result in a compilation error.


One personal note is that I think that this is the first time I have defined my own error set and used it in a library. This
is very easy to do, and I have continued to like the Zig error handling concept in general.

## Zig Thoughts

Its interesting to me that Zig's comptime system is powerful enough to express something like this, where C has no
concept of introspection even at compile time except in debug data. Zig's type system is simple enough to consider the
different possible constructs, partially because type parameters are already expanded when using a type, so there are no
higher kinded types or lifetime parameters or anything to make things complex.

However, I did not attempt to deal with some of the edge cases of the type system. Frames and AnyFrames for async,
function pointers and bound functions, opaque types, C pointers, or multi value pointers are all either impossible
or ambigous on how to deal with them in this context.

Its interesting to contrast the trait/typeclass style with Zig comptime type inspection. I was able to write a single
function that can act on most types, even types that have not explicitly been included in the set of 'sealable' or
'compactable' types. WIth a trait/typeclass system, types have to be included in the set, but when they are
they can be given a manual implementation to handle some of the edge cases like C pointers in a type-specific way.

Another difference is that traits often imply some kind of laws about the structure of a type or the operations of the
trait. However, in Zig's comptime it is very different to express when my functions will work and when they won't. Ideally
you would get a compilation error if the functions don't work with a custom error message ('@compileError"), but why
they don't work will still be up to the user. In addition, I'm not sure what would happen in certain cases that I did not
explicity forbid, like compacting a Frame, which may cause havok if used.


Ultimately I'm quite pleased that the concept seems to work. The appeal of Zig to me is that it is a small language
that seems to push me towards a feeling of craft in my programming, similar to C, TCL, and Forth. I like that I can
do my low lever memory management and handle things myself, and to think thoughts that are unavailable in high level languages,
while not having to use C. I find C both too restrictive in some siutations, and allowing to much freedom in others, and
while I would not say that Zig is perfect or that it completely meets my desired C replacement criteria, it gets points for
supporting a library like this one.


## Limitations

See the repositories README.md file for limitations.

One limitation that is specific to games is that while a copy of a game state is a fast and simple way to 
manage game saves, it does not provide any kind of versioning or self describing structure that would otherwise
be useful to load saves from previous versions of the game. To some extent this could be added to the buffer
created by seal, say by appending a header with a version.

I don't yet know how all of this would play out in a real application. Hopefully I will try it one day with a complex type,
perhaps with fields defined by other libraries or the standard library, and I will figure out if the limitations are
acceptable or not. Certain limitations like C pointers might be okay- I may only use this on a subset of my whole game
structure, for example, or reoganize the data into a GameSave structure with only the desired data, or something like that.

