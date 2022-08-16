+++
title = "Zig Symmetric Shadowcasting"
[taxonomies]
categories = ["roguelike", "zig", "rust", "python"]
+++

I now have a [Zig translation](https://github.com/nsmryan/zig_shadowcasting) of my 
[Rust translation](https://github.com/nsmryan/shadowcasting) of a very nice [Python algorithm](https://www.albertford.com/shadowcasting/)
for field of view!


This is part of a journey that I'm on where I am looking more seriously into Zig for personal projects.
Projects like this have been helping to give me a more nuanced and balanced view of Zig, which
has a lot of good, but of course also trades off some compared to Rust, C, C++, etc.

In this case I was translating my Rust version of this algorithm, so this was a
comparison of a somewhat generic algorithm from Rust to Zig. I will try to
write up my findings as I explored a series of designs.


I started out trying to maintain the generic interface from the Rust version.
The algorithm asks for a starting position (say of the player) and a callback
which can be used by the algorithm to determine if a given tile blocks vision,
as well as a callback that the algorithm calls to indicate that a given
position is visible.  This second callback could append the given tile position
to a list, mark it as a flag in a map, etc.


The Rust version uses closures to allow the user to capture their map and
result type, which do not need to appear in the function signature at all. This
was a little tricky as closures have some complex rules in Rust, and I found a
note in the code about how I wanted to provide a better interface, but I
couldn't get some lifetime annotations worked out. I understand why Rust makes
closures complex, and its not a functional programming language, but it does
make this kind of design difficult.


For Zig, I quickly realized that I was going to have to take in the map and
result, and function pointers, and just pass these explicitly to the user's
functions instead of assuming that they are captured in a closure.


My first design was a function that constructed a struct type, where the
function takes in the map and result type.  This kind of design seems to come
up in Zig fairly often, and in this case it does have some advantages. This
staging of type resolution lets you compute and compare types without having to
do everything within a single line of a function's type signature.

## First, Getting Things to Compile

I ran into a number of issues in the translation. Most of the bugs where small, simple
issues, but some are worth writing down.


TODO write up:
RowIter compared to Rust iter
if statements instead of lambdas
error sets as part of the API, and need to handle with callbacks
the std lib big rational type. In rust i used a dep, but I don't want to in zig. using for testing, but hand coded a new type


## The ComputeFov Generic Struct

Note that the InErrorSet seemed to be required so that the compute_fov function could be defined before the user
provided the 'mark_visible' function pointer, which is allowed to provide errors.
```zig
pub fn ComputeFov(comptime Map: type, comptime Result: type, comptime InErrorSet: type) type {
    return struct {
        const Self = @This();
        const ErrorSet = InErrorSet || error{Overflow};

        map: Map,
        result: Result,

        pub const IsBlocking = fn (pos: Pos, map: Map) bool;

        pub const MarkVisible = fn (pos: Pos, map: Map, result: Result) ErrorSet!void;

        pub fn new(map: Map, result: Result) Self {
            return Self{ .map = map, .result = result };
        }

        pub fn compute_fov(self: *Self, origin: Pos, is_blocking: IsBlocking, mark_visible: MarkVisible) ErrorSet!void {
            ...
        }
    };
}
```

This concept is fine, and does unpack concepts into multiple lines so you can read the 'IsBlocking' type signature and see what the 
error set is more easily then packing everything into a single type signature.

However, I didn't think that there was any real need to define a struct with a single usable function that the user then just immediately calls.
I think I could have refactored this to remove the 'new' function and just have the 'compute_fov', which would mean a call would look like:

```zig
	// Construct the type by providing the user's map, result, and error set, and then pass in the map, result, origin position, and function pointers.
    var compute_fov = ComputeFov([]const []const isize, *ArrayList(Pos), ErrorSet).compute_fov(tiles[0..], &visible, origin, is_blocking_fn, mark_visible_fn);

```

I figured I would explore a few more designs that remove this layering and just provide a single function to the user.

## A Generic Function Signature

I then tried a single generic function:

```zig
pub fn compute_fov(origin: Pos, map: anytype, visible: anytype, is_blocking: anytype, mark_visible: anytype) !void { }
```

This worked, but rubs me the wrong way a bit. The map, result type ('visible'),
and function pointers are all 'anytype'. I would have to explain to the user
what they needed to provide, including the function signatures of mark_visible
and is_blocking. 


Errors would still be caught at compile time, but the lack of information in
the type signature means you rely on documentation, similar to macros in Rust,
or functions in a dynamic language. Again, compile errors are still better, but
I was reluctant to leave the design like this.


I also seem to remember that the error type could not be inferred because
'compute_fov' calls a function called 'scan' which itself calls 'mark_visible',
and Zig could not infer that 'scan's error would be a union of its own errors
and mark_visible, and then use this to infer 'compute_fov's error set.


I ended up having about 20 lines of type level assertions trying to specify the
shape of all of these any types in some detail. I realize that I could have
just left them abtract, but I felt like a user was pretty likely to run into
weird problems if their types where not just so, and I didn't want to put that
on them.


## An Explicit, Generic Function Signature

I then tried to expand out my types as explicitly as I could:

```zig
pub fn compute_fov(origin: Pos, map: anytype, visible: anytype, is_blocking: fn (Pos, @TypeOf(map)) bool, comptime ErrorSet: type, mark_visible: fn (Pos, @TypeOf(map), @TypeOf(visible)) ErrorSet!void) (error{Overflow} || ErrorSet)!void {
```

This leads to a very long type signature. I believe it could be cleaned up with
some type functions like 'IsBlocking(comptime map: type)', but I didn't get
that far. It would have looked something like:


```zig
pub fn compute_fov(origin: Pos, map: anytype, visible: anytype, is_blocking: IsBlocking(@TypeOf(map)), comptime ErrorSet: type, mark_visible: MarkVisible(@TypeOf(map), @TypeOf(visible)), ErrorSet!void) (error{Overflow} || ErrorSet)!void {
```


Instead I ran into an unexpected aspect of type inference in Zig, which is that
when I tried to call this function with a slice the inferred type was an array 

```
    // tiles has type: [4][]const isize.
    // mark_visible_fn has type: fn (pos: Pos, tiles: []const []const isize, visible: *ArrayList(Pos)) !void.
    // is_blocking_fn has type: fn (pos: Pos, tiles: []const []const isize) bool.
    try compute_fov(origin, tiles[0..], &visible, is_blocking_fn, Allocator.Error, mark_visible_fn);
```

For example:
```
./src/main.zig:391:54: error: expected type 'fn(Pos, *const [4][]const isize) bool', found 'fn(Pos, []const []const isize) bool'
    try compute_fov(origin, tiles[0..], &visible, is_blocking_fn, Allocator.Error, mark_visible_fn);
```

The type of the slice 'tiles[0..]' was inferred to be a array of fixed size, even though I explicitly use the '[0..]' syntax to make it a slice!

This would not be a problem except that this map type is later used in the 'is_blocking' and 'mark_visible' function signatures, which require a slice,
so Zig complained that its inferred type for map did not match the argument types of the given function pointers, which is true.


I got stuck on this for a while, and ended up getting a lot of help on the Zig Discord.


### Help on Zig Discord

Three people helped me with this: 5-142857, pablo, and Tetralux. I shows the code [here](https://github.com/nsmryan/zig_shadowcasting/tree/minimal_inference)
as a minimal example of the problem.

It was quickly pointed out to me that the compiler had use the more general type of a pointer to an array because that type can be coerced to a slice.

This does make sense, even if it was surprising at first to see my array's size inferred there.

We talked through a series of solutions, including requiring the user to provide a slice, and then asking for its element type, or asking the user
to provide a struct type which contains functions with a certain signature that I would call within 'compute_fov'

I did not want to restrict the genericness of the function in this particular way, and I felt bad asking the user to do all of this extra work in order
to call my function- it felt like a rather complex contract between caller and callee.


After some discussion I felt like I was better off making the function less generic. One solution that did solve the original problem was to 
make a struct type holding the slice, and then pass a pointer to this type to 'compute_fov' and the other functions. That way the type was already
resolved by explicitly stating it in the struct definition, and the compiler could not infer a different type. However, at this point
I was ready to just make the code less generic.


Then Tetralux came up with a solution of a very different nature that I think it worth repeating:

```zig
fn NormalSlice(comptime T: type) type {
    var ti = @typeInfo(T);
    if (ti != .Pointer) @compileError("wanted slice or pointer to an array; given '" ++ @typeName(T) ++ "'");
    ti.Pointer.size = .Slice;
    ti.Pointer.is_const = true;
    return @Type(ti);
}
```
This would be used within the function signature of 'compute_fov', and would normalize the user's type into a slice if it
was a pointer.


I wanted to allow non-slice types, so I would have maybe used something like:
```zig
fn NormalSlice(comptime T: type) type {
    var ti = @typeInfo(T);
    if (ti == .Pointer) {
        ti.Pointer.size = .Slice;
        ti.Pointer.is_const = true;
        return @Type(ti);
    } else {
        return T;
    }
}
```

I thought that this was a pretty nice solution in a way- I did not even think to do this kind of explicit type level programming,
and if I had I'm not sure I would have come up with this.

I feel like I'm used to trying to convince a compiler to infer the right types itself, instead of explicitly acting on the
type to get what I want. This seems like a good concept to have in the tool belt.


However, as Tetralux stated at the time, and I agreed, the level of abstraction here was not really warrented, and makes the code
harder to reason able. This is especially true if I were to use this NormalSlice function. Its possible that I could clean up
my type signature, split out the logic into separate type level functions, and end up with something that looks clean. However,
it would still require a good bit of thought to understand what it was doing.

## A Simpler Design

I decided to restrict the way that results are recorded rather then leave that to the user. This resulted in the following
signature:
```zig
// The error type can now be precomputed. Note that the 'is_blocking' function cannot return an error, and we removed
// 'mark_visible' so there is no longer a need to accomidate a user's choice of error type.
const Error = error{Overflow} || Allocator.Error;

// The new compute_fov function takes a pointer to an arraylist of positions. This allows the user to re-use
// the same arraylist every call if they want.
// After calling 'compute_fov' the user just has to translate an array of positions into whatever format they
// want.
pub fn compute_fov(origin: Pos, map: anytype, visible: *ArrayList(Pos), is_blocking: anytype) Error!void {}
```

This design has the disadvantage of allocating memory for the array list, which is not necessary if the user
had a way to store visiblity in the map itself or something similar. However, the user at least gets to provide
the ArrayList, so they could provide a fixed buffer or something similar, or preallocate enough positions 
for the whole map if that was a problem.

In general I expect this will not really be a problem, and for my own code I expect it not to matter. I hope to
use this code at some point, so I will see for myself whether the comprise was worth it.



## Alternate Designs

I expect that there are a number of other possible designs here. 


It is clear to me that Zig provides enough features that there is a large
design space compared to C. In C I would either have taken function pointers
that used "void*" types for user data, or I would have just used fixed data
structures, such as a bit map or similar, and put the responsibility on the
user to arrange those structures before and after calling my code. The only
other C design I could think of would be a huge macro solution which somehow
specializes to given user types, but I am not interested in trying this out.



We may be able to duplicate the C design in Zig using 'anyopaque' types instead of void*.
I'm not sure whether the casting will all work out, but perhaps it could be done
just as in C.



One final design I did consider was converting this whole thing into an iterator
which yields visible tiles. Perhaps there could even be some kind of protocol
in the return type which allows the iterator to ask the user about whether a
tile blocks, and to receive a bool answer before then yielding either a
visible position or another question about whether a tile blocks.

The problem with this design is that the algorithm is recursive. I believe I would
need to keep effectively an array of explicit stack records recording the
recursion depth and current function state. This is just too much work,
and doesn't seem to provide enough benefit for my current needs, but it is
at least a possible way to design one's way out of this callback problem.


Speaking of yielding results, there may be a way to use async as a kind of
coroutine style which inverts the control flow back to the user rather then
calling down into callbacks.  This is somewhat appealing - I don't really like
taking callbacks if I can avoid it, but I think bringing in async is too much
complexity for a simple algorithm. I would rather simplify the algorithm then
explore this at the moment, but it may be worth trying some time.


# Zig Thoughts

I feel like this project was a good exploration of Zig for me- it is not yet really
using Zig in anger, but it is close. I ran into some subtlies, had to ask for help,
and eventually chose to change my goal to better fit Zig rather then translate it
verbatim.


I found some good and some bad here. I really like seeing how allocators are
ubiquitous in Zig APIs, I liked how at any place in the code there are fewer
complex concepts that tend to come up compared to Rust, and I found that Zig's
documentation, while incomplete, was usable along with the standard library
source code itself. I like the lack of hidden control flow.


One thing that is a little concerning here is that if I had not happened to use a slice type for my map, I would not have
seen the type inference problem. I would have probably written up and used this code without ever knowing that a small
difference in types would trigger this mismatch between inferred and given types.
This would cause a compilation error, not a runtime error, but it would prefer to not leave a time bomb in my libraries
just waiting for someone to go slightly off the beaten path.


It seems like Zig code is a slightly different material then C code. In C, I would expect that a function
that compiles will not produce a compilation error one day when it is called in a different way.
I'm having a hard time articulating the difference, but I don't find that this happens in Rust or Haskell-
there is something about Zig that is a little more 'adhoc', perhaps because those other 
languages are closer to the theory of parametric polymorphism.

I did also run into one memory management issue with an ArrayList where I passed the ArrayList itself
to a function and then used the original variable to check the result. This does not work, as the
function's copy of the ArrayList likely allocated memory, invalidating the pointer in the original
variable. I quickly realized my mistake, but Rust would never have allowed the code to compile.

