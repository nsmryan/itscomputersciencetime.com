+++
title = "Zig Extract Bits"
[taxonomies]
categories = ["zig", "binary", "Programming", "Embedded", "bits"]
+++

I put together a strange function in Zig today which [extracts a range of bits](https://github.com/nsmryan/zig_bit_extract/blob/master/bit_extract.zig)
from a given integer. This works for integers up to 64 bits and works by synthesizing
a bitfield pointer and then dereferencing it to use Zig's internal bit shift and mask
code to get the correct bits out.

While shifting and masking bits is not particularly novel, the implementation plays
two tricks which make it fun:

  1. It generates two of the argument types from the first argument, resulting in arguments
     that are always valid. The resulting types can only represent integers within the range of
     valid bits. Although it is still possible to generate ranges that exceed the integer size, these
     seem to be safe and just result in extra 0s.
  2. The code uses 'inline else' twice to effectively move the bit width and bit offset into compile time.
  
  
Both of these tricks have tradeoffs. Generating the arguments types makes the function a little tricker
to use potentially, since the argument type is not immediately apparent. It also means that the byte
size of the given integer must be a power of 2, which is commonly true but not required in Zig. This is
required to make the bit range and offset always completely match the integer, which I wanted to restrict 
the code generation.

I already had to use setEvalBranchQuota to get comptime to generate enough branchs to cover all possible
shift and mask possiblities, and opening up the sizes would just generate more possibilities.

However, if I don't use this trick, as in bitExtractSimple, than the function doesn't work on smaller integer
types because of the generated branch which use bit offsets outside of the statically known integer bit width.



The second trick may be the worst part here- the compiler is going to generate literally a few thousand branches
(2 ^ 12) in order to cover every possible combination of shifts and masks. I don't know what this looks like
internally in Zig: whether it generates these only once (I suspect this is true), or regenerates the whole
thing for each call site, but its a lot of code that will mostly be unused.

I haven't profiled this to see whether there is any actual performance improvement to moving these parameters into
compile time like this. I was mostly interested in the idea of presenting a function that could extract
bits from an integer using the concept of bit field pointers in Zig. At the time of writing this is
an [undocumented feature](https://github.com/ziglang/zig/issues/14361) of Zig which I've been playing around with,
and managed to wrap in a function in this case.

