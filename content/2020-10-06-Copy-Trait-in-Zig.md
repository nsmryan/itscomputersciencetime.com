+++
title = "A Copy Trait in Zig"
[taxonomies]
categories = ["zig", "rust", "traits", "metaprogramming"]
+++
# Copy Trait for Zig
[This repo](https://github.com/nsmryan/zig_copy) contains a simple implementation of Rust's Copy trait in Zig, using the
std.meta.trait.TraitFn implementation of traits.


The purpose is mostly to play around with Zig's meta programming through comptime type
reflection, and to explore my interest in languages where all possible shapes of a
type can be inspected.


If you look at [Zig's traits](https://github.com/ziglang/zig/blob/master/lib/std/meta/trait.zig)
you can see the core concept is:
```zig
pub const TraitFn = fn (type) bool;
```
In other words- a trait is a function from types to booleans. Traits/Protocols/TypeClasses
can be considered predicates in the type system, taking types to true or false, where true
indicates that a type implements the trait.


Zig's implementation of this concept feels very much in line with the rest of Zig's design-
it is pragmatic and uses basic concepts like functions with no need for built in support.


I'm sure this limits the languages ability to perform checks such as Rust's orfan rule, or
some of the more complex Haskell concepts, or the ability to create a Zig version of Hoogle,
or Rust's trait documentation. It also makes the use of traits seem more like what I believe
is done in Go, where a type can implement a Protocol as long as it has the correct
definitions, rather then the explicit way this is done in Rust and Haskell. Whether this is
good or not is a whole bag of tradeoffs- its certainly not what I'm used to.


However, I think the intent is to get much of the advantage
of other language's innovations while keeping the Zig version simple and built within the
language instead of build into the language. This is a signficant tradeoff, and one I will
be interested to watch within the Zig community.


## The Implementation
The implementation of the Copy trait is a single function that switches on the given type.
Some simple types like u8 or bool result in an immediate return of true, while others like
a struct or union require recursing on the types of each field (or the child type in the case
of an array).


Overall the implementation is probably too trivial to really say any more- you use the 
[typeInfo](https://ziglang.org/documentation/0.6.0/std/#builtin;TypeInfo) to get the
type of type (the kind) and that guides whether to return or decompose the type
into smaller types.


I think it would be interesting to try to implement ever more complex concepts in this system-
not necessarily to use them for real, but just to explore what can be done with Zig's comptime.


