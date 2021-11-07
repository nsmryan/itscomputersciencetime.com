+++
title = "CCSDS Primary Header Forth"
[taxonomies]
categories = ["Language", "Programming", "forth", "ccsds", "space"]
+++

I recently created a little github repository with an implementation of the CCSDS Space
packet protocol primary header in Forth. This is a simple packet header with a handle
of fields. I like to implement this header in different languages as a test of handling
endianness, bitfields, and binary data in each language.


I realized that I had not done this experiment in Forth, which is particularly
good with binary data. The implementation is here: https://github.com/nsmryan/ccsdsforth.


I will leave the README as my notes on the project. Its a small thing, and less than
30 lines of code, but it was at least interesting to explore the different options
and write a little smidgen of Forth.

