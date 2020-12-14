+++
title = "The Material of Programming Languages"
[taxonomies]
categories = ["Zig", "Language", "C", "Lisp", "Forth"]
+++
This is a weird post about the feeling of programming in different
languages. Each language is like a different material or 
technique, and feelings different. I will be exploring how
certain languages feel to me when viewed as a material.


## C
C is strange- it is not a pure, mathematical artifact. It is
not minimal with no syntax, and yet it is much simpler then
many languages. It is an engineering artifact rather than
a research paper.

C feels, in the systems that I use in practice, not like the
very purest stuff of the universe, but rather the bedrock of
the earth. It not pure, but its practical and immediate.

C can be very precise, but it is also full of small holes. 
Undefined behavior, memory corruption, etc, requires
great discipline and care. You have to design carefully,
and keep many things in your head at a time- memory layout,
responsibility for allocation, the OS, the stack
and the heap, widths and properties of types.

I find that in C, I feel tightly constrained. There are
very few opening for abstraction, and I find that I think
very concretely. The majority of code is structs, enums,
functions, variables, primitive types, #defines,
and typedefs. These are simple tools in some ways.

If you want to build something beyond these, you craft
something from function pointers, perhaps offsetof,
perhaps sizeof in there. Its hard, manual, and error prone,
and ultimately I would not do it in code that I care about.


Sometimes when I write C for embedded systems, I feel like
I can write out a concept in straight lines, with each
piece a single stroke of a brush. It is a rare and
enjoyable feeling. I feel like the code has a single path,
that it is constrained in all places, all parameters
are validated, all return values checked, and the code
almost can't help but be simple. This doesn't mean
it is correct, but it has a chance to be as correct
I ever get in C.

People sometimes refer to C code as a shalpel- precise but
easy to get a bad cut. I think C code written this way,
as single strokes of a brush, feels more like clockwork- 
its delicate, and tiny inprefections could create a mess,
but when it works it works beautifully, sometimes for months
or years, just as it did when you set it into motion.


C can quickly become a tarpit. This is true of other languages
too, certainly, but I find that C is frequently error prone
and requires a great discipline and restraint to make
something robust. Of all languages, I think I've reached the
highest level of mastery over C, and I'm able to build larger
and more structured C programs than I am in other languages.

# Python
Python is very much like legos- its easy to snap things together,
but when they get complex its quite hard to tell if they fit
together. Also, if you want to restructure something large,
you have a lot of work ahead of you.

I don't mean to put Python down. It is easy to build, and
I expect that with experience and great discipline it can be
done much better than what I do.

Python's standard library also makes it a quick-and-easy language.
These are not the properties you want in your big programming
artifacts- if you are building a monolith than 'easy' seems
like a trap.


In some ways, when I go from C to Python, I feel like I am
unable to express myself with precision. Python simply
cannot express the precision of C- it is unthinkable to
feel that a Python program is really correct to me. This
may not be true, but Python's duck typing means that the
true 'correctness' properties of a program are a very complex,
runtime dependent amalgamation which I never truely know.


Strangely, while Python is not the bedrock that C is,
it does feel fairly universal. I expect to be able to
run a Python script, and for it to be incorporated
into many tools and places.



Forth has a very different feeling than any other language.
I feel like a craftsperson, carefully cutting each problem
into slices, building bespoke tools for each situation,
until everything is trivial.

In some sense Forth has a simple syntax, but more generally
Forth doesn't have a single syntax. It can't truely
be broken by spaces- its a wheel within a wheel that
consumes a sequence of symbols, and weaves these symbols
into a program. The wheels are the inner and outer
interpreters, and they can lift the simplest
set of instructions into a language.

The reason there is no single syntax is that as the symbols
are injested, the program is running- it does not parse
and compile separately. The remaining program text
is avilable to the running program, so it can change
how the text is read as it is compiled.

