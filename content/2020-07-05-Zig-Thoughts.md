+++
title = "Zig Thoughts"
[taxonomies]
categories = ["Zig", "Language", "C", "Embedded"]
+++
# Thoughts on the Zig Language
I've been looking into the Zig programming language recently, and this post
records some impressions on Zig and its place in the embedded systems programming
world in which I live. This post is largely a comparison of Zig and C because that is
the most relevant comparison for me. There is some Rust/Zig comparison as well, as I
consider Rust the most significant comparison to C for the work that I do.


The main take-away is that Zig is very impressive, and feels like it was
designed to make things nicer for systems programming, including embedded
systems. It addresses issues in embedded programming, and provides control I
wish I had in C. It seems to guide you into writing simple, robust code,
which I've learned to take seriously when choosing a programming language.


I will end the post with some limitations of Zig, but no language is perfect,
and, in general I feel that it has the potential to be a better C in a significant way.
I get a feeling that Zig is a safer C, with better control, intentionally limited extensions
in capability (although ones that might have a significant impact!), with a
pragmatic approach to correctness. It does not always enforce correctness 100%,
but it helps a lot, gives you tools, and lets you apply some discipline.
I'm not sure which way is better, or if it depends on the particular feature, but
I'm willing to try the Zig way.


## Principals

### No Hidden Control Flow
Part of the philosophy of Zig is that there is no hidden control flow. This is a subtle thing
that is common in C++ and Rust, and I respect the attention to detail in Zig to avoid
this- it shows a respect for simplicity and safety.
This is one of the reasons I prefer C to C++ when I have to have tight control over everything
my code does- I know it will not jump around for a while, implicitly calling a series of
functions that I would have to track down to figure out, every time I close a curly brace.


Another example of hidden control flow in C++ is overloading operators- a simple piece of code
in C++ such as:
```c++
a = b + 1;
```
may involve not only an overloaded '+' operator, but even an overloaded assignment. Such an
innocent statement requires knowledge from the types, their interaction (in case 'a' and 'b'
have different types and the resulting function is selected based on their types), possibly
knowledge of an inheritence hierarchy in case some definitions are split between classes,
and probably more.


Another example is exceptions- while Zig uses a try/catch syntax for error handling,
it does not have exceptions. This actually confused me at first- I was surprised to see
the decision to use exceptions in this language, and only later learned that it was just
that the choice of names was similar, not that Zig actually has exceptions!

I will talk about error handling later, but the point is that it does not involve
a control path that is set up dynamically as with exceptions, where the handler of
an exception is determined by the call tree at run time.


I want to note that Zig does have a 'defer' statement, which is used in place
of RAII. This does create a control path that jumps similar to RAII, but it is not
hidden in the sense that you have to put an explicit 'defer' statement in your code
in order for this jump to happen.

I don't have experience using 'defer' for cleanup, but it seems better than RAII to me:
you can control how the cleanup is done (by giving the statement you want to
run), and is fairly easy to check for- the cleanup code should be close to the
creation code, so its easy to check that you included the defer statement. The
problem is that it does not guarentee cleanup- you could omit the cleanup code
accidentally- which is unfortunate, but I don't have the experience to know how
much of a problem that is.

### Handle Every Error
The default in Zig is to handle every error. This is something you can kind of work towards in C,
using a static analysis too like Cobra to look at how you handle errors, and take
manual pains to try to check every return value. In Zig, the language leads you
to handle errors, and gives you tools to do so, which addresses the friction I feel
when trying to write robust code in C.


I think the main take-away here is that in C, the default is to ignore errors- C will not stop
you or, by default, indicate that an error code is ignored- while in Zig
the default is to handle errors. This matters, and is very refreshing to me. It
gives me some hope that if I was using a significant codebase in Zig, that it would not be
full of small errors. Rust also helps with this, but in a different way.

### Keeping it Simple
There is generally a desire to keep Zig simple. I like this, and I think this is one place
where Rust makes me a bit nervous. That said, Zig does have some advanced features, and
certainly has more features then C. I still feel that the simplicity drives design
decisions, and I'm willing to accept some additional complexity over C, but too much
does present a problem.

### Manual Memory Management
Manual memory managment is something that matters a great deal in 1) embedded programming, 
and in 2) latency sensitive programs. I often write code with both of these properties,
so this is a must-have. If Zig was garbage collected, I would not be writing this article.

## Types
There are some differences between Zig types and C types. Zig provides some more 
advanced types, some nice namespacing (like Rust, and C++ except without inheritance), and
some types with more control over layout then in C, which is a huge factor for me.


In general Zig has several various of each type of type- structs, enums, unions, primitives,
all have some variations in layout and use case. This is good- Zig is explicit about these
variations while in C you might simply use a type differently or provide a non-standard
attribute or pragma to get the same effect.

### Enum Control
Enums can be given explicit sizes in Zig! Not just 8/16/32/etc, but any number of bits!
This is pretty neat, but more then that allows enums to be used in data layouts where
they are avoided in C, requiring casting to and from integer types.

This is especially important in Zig when we get to unions (sum types) where the tag
can be given explicitly by an enum type.


As with other types, there is a way to specify C compatibility, which is important.


There is a concept of 'non-exhaustive enums' which I will have to defer to a time when
I've tried them out- so just be aware that this is one variation.

### Struct Control
Structs in Zig are somewhat similar to C- a sequence of fields. Even the basic structs
have some additional features in Zig, such as default values, and each struct
creates a namespace to put functions and constants. I do like this kind of
namespacing- this is how Rust does it, and I find it provides a simple
organization for utility functions related to a type.


As with other types, there are several variations with structs. One of note is that
the basic structs do not have a defined layout- the compiler is free to reorganize
fields, while the 'extern' structs have a known layout, and packed structures are
like __attribute__((packed)) or '#pragma pack' in C.


We will return to this below, but it is interesting to note that in Zig, structs are
values and are assigned to 'const' globals to create a type definition. This is odd,
but seems like something I would get used to, and might have some interesting implications.


It seems that Zig will check that packed structs have fields that can be packed, like
primitives and other packed structs. I'm not 100% sure about this, but this is a problem in
C where packing a struct does not guarentee that its layout is fully packed, in case it
has a field which is a struct that is not itself packed.

The general feeling I get from structs in Zig is that they have nice conveniences like
namespacing, field access, initialization, layout control, etc. Its nice to see
that the language has the things I like to have.

### Polymorphism
Polymorphism in Zig is done in a very interesting way. Essentially, types are values,
and functions can be written that take types as input and types as output. 
For example, a list function might take its element type as input and produce the
type of lists of the given type.


I don't have experience using it, so I can't comment on how it works in
practice. It seems like it might be a way to introduce some additional freedom and
generic code without overhead or exceptionally complex code, but again I would like
to write more about this if I get a better feeling on it.


### Interfaces/Traits Encoding
I just wanted to note that I generally find that Traits/Type Classes/Protocols are
useful and do not have the issues I run into when trying to use OOP for anything.
I like that Rust has a Trait system, but currently Zig does not.

I'm not sure that this is a bad thing- Traits are a form of controlling
dispatch through types, and it can require the programmer to know the
resolution rules, and does create call sites that are not explicit. In C,
almost all function calls are to known functions, unless function pointers are
used (which I use for specific cases, but generally try to limit
for exactly this reason).

It seems like the common practice in Zig is to create a struct of function pointers
and use this as an iterface- this is how iterators are done in Zig for example.
I've done this in C a bit, and it is a powerful, if somewhat manual, way to program.

I don't have a strong conclusion here, except to note that current state of Zig
on the topic of interfaces and trait style systems.

### Bitfields
Bit fields have a consistent meaning in Zig!

I simply do not use bit fields in C anymore, and I recommend new programmers to
avoid them in C. This is because their layout is not guarenteed across compiler
and architectures, and the main use case I have for bit fields is talking to hardware
or between devices. There are some other issues related to how much memory is read
when accessing bitfields, and in general I find it is better to use manual 
shifting and masking with macros in C rather then use bit fields.

In Zig these issues are basically [resolved](https://andrewkelley.me/post/a-better-way-to-implement-bit-fields.html).
Bit fields always have the same layout, there is no special syntax for them, and they
do the thing I would expect them to do for fields that go between byte alignment 
(they are big endian). If you wanted little endian bit fields, I suppose you would
have to reorder the bytes before accessing such fields, but that is true normally.

### Endianness
I haven't looked too deep into endianness in Zig, but I do know that it is not handled
at the type level (except for the one caveat of bit fields as described above). There is
a git issue [discussing this](https://github.com/ziglang/zig/issues/3380).

I'm not sure whether this needs to be done at the type level, or another level, but it would
be nice to have some level of language support for endianness. This is something I deal with
frequently, and it is easy to get wrong.


Generally the strategy is to swap to native endianness at your interfaces, so data internal
to your system can be accessed as normal. There are at least two issues with this. One
is that you have to swap field-by-field as swapping depends on the size of each field,
which usually requires manually writing a function to do this (with nothing to catch
a mistake). The second issue is with data where the format is defined by a standard,
such as CCSDS, and a certain endianness is required. In this case I would write
getter/setter style functions for each field to enforce the correct endiannes for all
processors, but this again requires manual labor each times this situation occurs.


I don't know how this is handled in Zig, or how it might change in the future.
At the least, with type introspection, it might be possible to write a swapping
function that simply swaps endianness of all fields generically?


### Introspection
This one is potentially a big deal- Zig seems to be capable of inspecting the structure
of types at runtime. I have wished very much for this in C, where the exact layout
and contents of types can be very very important (again, in hardware and communication),
and I could do so many interesting things if I could inspect types. You can do so much
in C with just offsetof, and that is a tiny lever compared to full introspection.


This kind of introspection teeters on the edge of 'too powerful' perhaps, where too
much metaprogramming can lead to metabugs, and I would not likely use it in critical code,
but I would use it in code that talks to or deals with critical systems. In those
cases, I would write generators, decoders, limit checkers, plotters, all kinds of
tools around this kind of feature.


Unfortunatly, this feature doesn't appear to be fully documented. I've learned a bit
from example, and from inspecting Zig source code, but I've had a hard time figuring out
what I can do with a given type definition.


### Misc
There are a number of additional features in Zig- see the documentation for details. A
number of situations that arise in programming, such as functions that do not return, or
while loops that exit on error, are covered with explicit syntax.


I also haven't cover the compile type execution system, which is apparently quite useful and
functional. Maybe some other day!

## Error Handling
Zig has a built in mechanism to handle errors, which is quite interesting. This is something
that is pervasive in my C code- move then half of it deals with handling and propogating errors
when I'm writing software that needs to be robust.


I'm not 100% certain on the details here, but it seems like there is a way to add error codes
easily, create and propogate them easily, and that the language ensures that you handle them
when they occur (nice!).


There is some concept of the error codes within a compilation unit, or creating your own error
enum types. I think I will need to use this feature "in anger" to really have thoughts on it,
but like with other parts of Zig, the attention to detail and correctness is encouraging and
gives me hope that this feature is well thought out.


There is some potential that having built in error handling results in friction when you
want to handle errors in a different way- I really don't know. For example, I'm not sure
how you would propogate additional error information in Zig right now (maybe a reference
to a structure as a function parameter?).

## Build System
As with Rust, Zig comes with a canonical way to build code. However, instead of a config file
and separate build tool like cargo, Zig comes built with a build system, where you write Zig
code to build your Zig code. This seems interesting to me- to be honest it makes me want to
write a build system in C where you write C code to build your C code (and I've done some
experiments along there lines...).


I could see this being very nice. You do have to learn the build library, but you can just
write code to do what you want, instead of specifying it in a build tool's specific syntax.
As with manu features, using this "in anger" is required to truely understand its implications,
but it seems like a good step to me.

## Limitations
With all of these positives, lets end with some limitations. Some of these are due to Zig's
age, but I don't want to give it too much of a pass- if you want to use it today, you have to
understand how it is limited.


I expect that I will be missing some things here- I don't know how Zig development feels
day-to-day, only the language level design that I've read about.

### Contracts
There is no reason to expect Zig to include a contract (pre-condition, post-condition) system,
but I think its worth mentioning when talking about languages that can be used in embedded systems
with a focus on safety. Zig seems to emphasize pragmatic safety in error handling, Debug build checks,
and catching common errors in language features and compile time checking, but does not emphasize
extreme correctness in the application of formal methods.


This might be the right way to go in the end- I want as much checking and safety as I can afford,
and I do what I can to use static analysis, levels of testing, reviews, etc to get correctness.
If the language helped a good deal, as Zig seems to, but did not provide that extra level of
formal checking, maybe that would still be good enough. I could also hope that Zig, with its
general ability to provide features that are missing in C and C++, might prove amenable
to formal analysis in ways that are difficult in other languages? Just speculation.

### Rust Checking
Rust and Zig are different languages, and provide different guarentees. I like that Rust simply
enforces certain conditions, like thread safe access to data, limitations on global data, and
helps with safe use of resources with ownership. Zig is not Rust, and does its own set of checks,
perhaps has checks Rust does not, but in general it is not a "big idea" language like Rust, where
the language is deeply caught up in the ownership system, leading to complex lifetime syntax and rules,
and generally causing headaches in order to require certain correctness conditions in most code.

This is a complicated topic, but I do like the feeling that Rust is checking nearly all code for
certain conditions, and the likelyhood of certain issues is very small. I don't like the complexity,
or the feeling that if I need to go outside of the lines I will fall off the cliff into a land
of implicit rules governing how safe and unsafe Rust interact.


Overall I guess its simply worth noting that these languages are different, have different goals,
and have some overlapping community and values. There is room for many languages, and they
don't all have to have the same features. Would Zig benefit from having some Rust mixed in?
Perhaps, depending on your values.

### Packaging
This is worth noting- Zig does not currently have a package system or community website like 
[crates.io](crates.io). This is planned, but as far as I can tell, Zig is like C in that you either
install libraries to your system, link against local compiled versions, or just include library
source code in your source tree and build it into your project. For some programs this is okay-
embedded code frequently needs to compile all code in its build (even the OS in some cases),
but for general use I expect this to be a bit of friction.


I hope Zig can do this well, and provide the care it seems to give the rest of its design, as packaging
tools seem to be pretty significant in a language's adoption and community.

### New
Its not Zig's fault that it is new, but it is new. The language can change, some features are not
implemented, etc. I mention this because some domains require very stringent language use, and only
certain languages have passed the barrier into acceptance (C, C++, Ada), and Zig is far from
ready for this.


## Conclusion
Writing this post has made me very interested in trying Zig out. As I've said, the attention to detail,
the clear understanding of robust programming and practice issues related to embedded systems, the
general feeling of a better C, all come together to make Zig very appealing. No language is perfect,
and I expect to write C for a long time to come, to use Rust for my personal projects, and to use
many other languages outside of embedded systems, but Zig fills a void in the pantheon of languages-
the simple but better C, which has real implications and importance in the embedded world.


If you also find this interesting, the Zig website is ziglang.org, the community seems welcoming,
and the creator, Andrew Kelley (andrewrk), does Twitch streams and has Youtube videos and a few talks.


Thanks for reading!

