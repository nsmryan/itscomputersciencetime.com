+++
title = "Hew and Inlay"
[taxonomies]
categories = ["Rust", "CLI", "Programming", "Embedded", "Binary"]
+++
A few years ago I wrote a pair of command line tools called
[Hew](https://crates.io/crates/hew)
and [Inlay](https://github.com/nsmryan/inlay).
The idea is that Hew is a rougher tool, and Inlay a finer
one, for different use cases.


## Hew
The simpler tools is called Hew.
It is basically a simple 'xxd' tool
which translates from hex to binary and back, but with no options for
things like an ASCII display or byte offsets like xxd.


The purpose of this tool is to inspect binary files quicky, and translate
back and forth between hex and binary in case you want to generate,
modify, or inspect a file in one format or the other. Hew
is faster then xxd, and has options for modifying the encoding
and decoding process for different word and line widths.


## Inlay
Inlay is the more complex tool. It can translate between text files
and binary files, with simple binary encodings.
It supports varying bit width integers, floats and doubles,
and little and big endian values.


The concept is to describe the simplest binary structures in a few
lines of a configuration file, allowing for bit fields and
the most common situations, and to quicky create csv (or other
text files) from a binary, or create a csv that you want packed
into binary.


Example applications would be to inspect a binary file with a 
structure that you don't yet have tools to process (such as from
an instrument with a format that is not common, but is simple),
or to create a configuration table for a device from a simple text file,
where the device expects a binary file.


There is a good bit of documentation in the README about options,
examples including a bit field, and different output formats..
