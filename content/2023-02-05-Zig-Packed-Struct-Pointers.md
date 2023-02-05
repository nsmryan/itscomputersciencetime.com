+++
title = "Zig Packed Struct Pointers"
[taxonomies]
categories = ["C", "C++", "Programming", "Embedded"]
+++

While working on one of my [side projects](https://github.com/nsmryan/zig_playground)
which uses another of my [side projects](https://github.com/nsmryan/zig_tcl) I realized
that I don't really understand how pointers withi bitfields (packed structs) work in Zig.


Looking around for documentation, I found that this is [not yet](https://github.com/ziglang/zig/issues/14361)
documented, so I started to investigate. This post contains what I've learned as a reference
until the official language documenation includes this information.


## Packed Structure Pointers

Currently these pointers can be created in two ways:

  1. The language reference shows: https://ziglang.org/documentation/master/#toc-packed-struct where a pointer is created from a field in a struct.
     This looks like a normal pointer to a field, but if the field happens to be in a packed struct then it is a special kind of pointer.

  2. There is an undocumented way to do this with 'align'

There should be a third way to do this using @Type to take a std.builtin.Type structure and create a pointer from it,
but this is [currently not implemented](https://github.com/ziglang/zig/issues/14568#issuecomment-1418153371).

Both of the currently available ways to create these pointers are given below (based on the language reference example):

```zig
    const std = @import("std");
    const expect = std.testing.expect;

    const BitField = packed struct {
        a: u3,
        b: u3,
        c: u2,
    };

    pub fn main() !void {
        var bit_field = BitField{
            .a = 1,
            .b = 2,
            .c = 3,
        };

        // Simply create a pointer to the field itself.
        try expect((&bit_field.b).* == 2);
        
        // Here we use the alternate syntax of 'align' to provide three argument instead of the usual one.
        // Usually 'align' takes only the alignment of the type in bytes. Here we give:
        // 1. The alignment of the type in bytes.
        // 2. The bit offset into the type at which to load the field.
        // 3. The size of the 'host pointer size' in bytes. This is the size of the integer to load when extracting the bit field.
        // 
        // This third field was the hardest to figure out. mlugg on Discord explained to me that since
        // packed structs are implemented as a native integer, with automatically generated masks and shifts
        // to extract bits from within this integer, we need to know the integer width in order to first
        // load the integer, and then use the second argument to 'align' to shift that loaded integer,
        // and finally use the size of the value being pointed to (u3 here) to mask out only the desired
        // bits.
        try expect(@ptrCast(*align(1:3:1) u3, &bit_field).* == 2);
    }
```

I played around with this concept [here](https://github.com/nsmryan/zig_bit_extract) to wrap this pointer concept in a function
as a test of my understanding.


I think this implementation of packed structs seems like a good idea, and I'm looking forward to a time when the
bugs are worked out and the pointer types include bit offset and host pointer size information.
