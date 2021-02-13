+++
title = "CCSDS Length Field"
[taxonomies]
categories = ["ccsds", "space", "nasa"]
+++
One of the things I want to do with this blog is discuss topics that
are relevant to programming embedded systems for aerospace. For
this post, I will offer some experience with the CCSDS Space
Packet Protocol primary header.


I have been using this header for about 8 years, and I currently
see the CCSDS length field definition as an overall bad design.


## CCSDS Space Packet Protocol Length Field
If you aren't familiar with this packet definition, it is a simple
6 byte header ending with a 2 byte length field. The problem is
that the length field is not the full length of the packet, nor
the length after the header. Instead, the protocol is defined
such that there much be at least one byte of data after the header,
and all packet length field values are valid. This means that
the length field is the remaining length of the packet after
that first required byte.


In other words, you will often see this implemented as the full
packet length (header and data) minus 7. The 7 comes from the
length of the primary header (6 bytes), plus one for the
one required byte.


I believe the intent here was that the length field is alway potentially
valid- if the length was the full packet or the data section length,
then small values may be invalid. By making all values valid, 
we can get slightly larger packets (not really that important, in
my opinion), and we don't end up having to always check that the
length field is not smaller then a certain number.



## Problems
There are two reasons that I think this is not helpful. First, I often
have a series of bytes that might or might not be a packet. The more
checking that one can do the better, and 0 is a common value for invalid
data. This means that if 0 where an invalid length, we could determine
that a given sequence of bytes was an invalid packet more easily, instead
of having to assume that 0 is probably invalid anyway, although knowing
that in principal it could actually be a tiny packet.


The second reason is that the definition indicates that the data length
of a packet can be 65536 bytes- not 65535 as one might expect. The full
packet length is 65542, a completely random number that is not a helpful
power of two.


This means that we should technically be using 32 bit integers for packet
lengths, instead of the 16 bit integer that the length is stored in within the
packet.  In practice, it is very unlikely that I would find a valid packet with
anywhere near that length value. This means that I actually store the lengths
in 16 bit integers, and length fields that are too large need to be used to
indicate an invalid packet before the 7 byte offset is added, to avoid the
possibility of overflow.


## Conclusion
This length field definition is not exactly a huge deal, but ever CCSDS
packet library I've seen seems to note how strange it is, and the packet
streaming libraries I've written always have to deal with this somehow,
often by implicit assumptions that the length field, in reality, has
only certain values and not the technically valid ones allowed by the
standard.

