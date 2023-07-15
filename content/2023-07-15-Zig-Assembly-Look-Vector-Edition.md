+++
title = "Zig Assembly Look Vector Edition"
[taxonomies]
categories = ["C", "Zig", "programming", "vector", "simd", "assembly", "performance"]
+++
This is a follow up to the previous post about inspecting some Zig assembly using Godbolt
for the problem described in [these](https://owen.cafe/posts/six-times-faster-than-c) [two](https://owen.cafe/posts/the-same-speed-as-c) posts.


This time I tried out Zig Vectors in order to generate some SIMD instructions. I have wanted
to try Zig vectors for a while, but also I was wondering whether we could get better
performance then the hand written assembly this way.


## Vector Version

The vector version can be found here: https://godbolt.org/z/16rsh84eo
I found that 512 length byte vectors provide the best performance.


Technically this code is slightly incorrect- it can read too much memory if the input
is not aligned to 512 bytes, and can use data after the 0 that should stop it.
j

However, I fed it 512 aligned inputs and ignored this fact, assuming that it would
produce a small performance impact. I'm not actually sure whether this is true
in this case were we don't know where the 0 will occur. 


Either way, I get a result of 1000 runs taking 0.103 seconds with 1 million
character input. This is in fact better then the hand written assembly-
perhaps around twices as fast. This is surprisingly little gain- I expect
that there are ways to get better results from vector instructions than I have
in my clumsy attempt.


The resulting code is quite interesting. Zig allows variable length vectors
while generating instructions to match the target hardware.


In the case of the Godbolt results I got 64 bit instructions. It looks like a length or 512
bytes is the best case for my laptop, perhaps because it amortizes the cost of branch instructions.
j

There are quite a lot of instructions generated, both in number and variety. Vector moves,
xors, tests, add, comparison, creating mask registers, extraction, and bitwise instructions.

