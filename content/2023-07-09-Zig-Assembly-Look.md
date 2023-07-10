+++
title = "Zig Assembly Look"
[taxonomies]
categories = ["C", "Zig", "programming", "assembly", "performance"]
+++
After reading [these](https://owen.cafe/posts/six-times-faster-than-c) [two](https://owen.cafe/posts/the-same-speed-as-c) very interesting posts
on the assembly output of clang and gcc on a small problem, and how to achieve
better results, I wondered what this would look like in Zig (using Zig master on godbolt).



## Set Up

The HN comments mention 'zig cc', but not what the equivalent code in Zig would produce,
so lets see what happens. Zig is a little different from C, so some things like signedness
and rules around overflow are going to be different. I suspect that this is part of
why C compilers generate some of the instructions that they talk about in the post.
I didn't try to replicate their types exactly, but rather to write what I would expect
to write in Zig to see how it compares.

I'm ignoring the fact that NULL terminated strings are uncommon in Zig- perhaps
this string comes from a C API or was written into memory without keeping
track of the size, and we need to process it looking for NULL without a slice.


## Original Code

The original C code translated to Zig is here: https://godbolt.org/z/EedzaGqvb
The assembly output is almost identical, except for one really interesting fact-
the 0 case was automatically moved to the end. This is the post's first observation,
and I'm really not sure why Zig manages to figure this one out. Is it actually
an optimization pass that realizes that we may be looking for a null terminator
even though the character type is u8? Or does it just order by some other criteria
and it happens that 0 is last?


Swapping the 's' and the 'p' seems to [indicate](https://godbolt.org/z/Tsoaq3451) that
the cases are sorted in decreasing order of switch value. I wonder if this has any
advantage except for ensuring that 0 is last? It seems like it is clearly intentional,
but I can't think of a reason except for optimizing for NULL terminators. If anything
I think in binary data 0 and other small numbers are more common and it would be
better to check for those first, except for 0 in ascii text.


Some further testing shows that the switch cases are not simply sorted, but split
up to catch groups of cases, as in https://godbolt.org/z/xYMKfbfoj where
there is a '>' comparison to split greater and less than 'r'.
From this I would say there are probably a set of rules for ordering cases which I
do not know anything about.


## Just Don't Branch

Try to replicate the "Just Don't Branch" section using the psudeocode, translated
into Zig, does not produce the cmove instructions, but rather a series of cmp, je
which is no better https://godbolt.org/z/scedMYTer .


## Freeing a Register

Try to free a register by using a variable set to 0, 1, or -1 does result in sete and a cmove instruction,
although arranged a bit differently https://godbolt.org/z/5vxE7rc9b .


## Lookup Table

Using a lookup table instead of switches results in https://godbolt.org/z/nqsf1rqfr which does
have the mov/add/mov sequence, although with two movzx and different code around it.
Its interesting how C manages to do better then Zig on a simple static array- the designated
initialization syntax is really quite simple. For an array requiring more complex calculations,
Zig's comptime would be better in the sense that C would not support this at all, but still interesting.

## Equals to Bool

Another idea came to me when thinking about vectorizing- just use == to get a 0 or 1, multiple the 'p' value by
-1, and add them all up.
With Zig 10 https://godbolt.org/z/13fWfE1v3 , and with Zig master (11) https://godbolt.org/z/1M4jK7q4T

## Performance

Now for the fun part. The original code in C using the original post's [benchmarking](https://github.com/414owen/blog-code/)
gives 3.996 seconds on my machine for the clang build. I used the clang build because Zig uses
LLVM currently, and the original poster seemed to focus on clang more anyway.

For Zig I wrote my own [benchmarking](https://github.com/nsmryan/zig-c-perf-test) which matches
the concept from the original post except it only creates one executable and lets you choose the algorithm
with a command line argument. It was built using "-Drelease-fast=true" to match the "-O3" in the original Makefile.

I used both Zig 10 and Zig 11, and provide both results as 10/11.

The straight Zig translation gives me 3.98/4.7, so basically the same result. This isn't too surprising, although I would
have expected Zig's rearranging of the 0 branch to get me a slight speedup.

Using 'if' instead of a switch gave me 3.864/4.58 seconds, so a very slight speedup.

The 'freeing a register' version gives me 0.774/0.937. This is much faster then I expected. Comparing the Zig and C assembly
I think this may be because Zig managed to get a tighter loop- there is only a single conditional jump because the fall
through behavior is to return. In other words, the action of the loop is all in the cmp/cmove followed by loading the 
next byte and a conditional jump to to either continue the loop or fall through to a 'ret' to end the function.


Finally the lookup table runs in 0.526/0.65 seconds. This is pretty comparable to the C versions output. In fact,
the original post's C version takes 0.525 seconds on my machine, so the Zig and C take basically exactly the same amount of time.
There is enough variation in the results that the 0.001 second difference is not likely significant.


It is interesting that Zig 11 does worse on all benchmarks. However, this is an unreleased version, and I'm not really sure what
changes are going into this version. Hopefully I can rememeber to benchmark the release when it occurs.

## Conclusion

I'm not sure what the conclusion is here really. Zig manages to produce very similar assembly, using a similar set of instructions
in each case, although often in slightly different ways.


Zig does perform that initial re-arrangement of the cases, although it didn't seem to matter much, and it does well on the 'freeing
a register' case where it outputs better assembly by 1 jump. The fastest C and fastest Zig where basically identical,
and the original poster only gets a speedup after this from writing assembly.


One avenue I did not pursue is vectorization. This is interesting in Zig because it makes SIMD a lot more approachable, so it is more
likely that I would be able to vectorize and get a speedup I would not see if I wrote in C. On the other hand, I didn't write the
vectored Zig code (yet...) so I don't know what the result would be.
