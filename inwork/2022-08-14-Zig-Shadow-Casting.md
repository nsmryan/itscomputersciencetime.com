+++
title = "Zig Symmetric Shadowcasting"
[taxonomies]
categories = ["roguelike", "zig", "rust", "python"]
+++

I now have a [Zig translation](https://github.com/nsmryan/zig_shadowcasting) of my 
[Rust translation](https://github.com/nsmryan/shadowcasting) of a very nice [Python algorithm](https://www.albertford.com/shadowcasting/)
for field of view!


This is part of a journey that I'm on where I am looking more seriously into Zig for personal projects.

In this case I was translating my Rust version, so this was a comparison of a somewhat generic algorithm from
Rust to Zig. I will try to write up my findings.


I started out trying to maintain the generic interface from the Rust version, where the user provides a map of
any type, and accumulates results in any type. The Rust version uses closures to allow the user to capture their
map and result type, which do not need to appear in the function signature at all. This was a little tricky,
and I found a note in the code about how I wanted to provide a better interface, but I couldn't get some lifetime
annotations worked out.


For Zig, I quickly realized that I was going to have to take in the map and result, and function pointers, and just pass
these explicitly to the user's functions instead of assuming that they are captured in a closure. This is fine with me-
its just more explicit and less fancy.

To do this I came up with a function that construct a struct type, where the function takes in the map and result type.
This kind of design seems to come up in Zig fairly often, and in this case it does have some advantages. This staging of type
resolution lets you compute and compare types without having to do everything within a single line of a function's
type signature.


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
pub fn compute_fov(origin: Pos, map: anytype, visible: anytype, is_blocking: anytype, mark_visible: anytype) !void {
```

This worked, but rubs me the wrong way a bit. The map, result type ('visible'), and function pointers are all 'anytype'. I would have to explain to the 
user what they needed to provide, including the function signatures of mark_visible and is_blocking.


I also seem to remember that the error type
could not be inferred because 'compute_fov' calls a function called 'scan' which itself calls 'mark_visible', and Zig could not infer that
'scan's error would be a union of its own errors and mark_visible, and then use this to infer 'compute_fov's error set.


I ended up having about 20 lines of type level assertions trying to specify the shape of all of these any types in some detail. I realize that I could
have just left them abtract, but I felt like a user was pretty likely to run into weird problems if their types where not just so, and I didn't want to
put that on them.


## An Explicit, Generic Function Signature

I then tried to expand out my types as explicitly as I could:

```zig
pub fn compute_fov(origin: Pos, map: anytype, visible: anytype, is_blocking: fn (Pos, @TypeOf(map)) bool, comptime ErrorSet: type, mark_visible: fn (Pos, @TypeOf(map), @TypeOf(visible)) ErrorSet!void) (error{Overflow} || ErrorSet)!void {
```

This leads to a very long type signature. I believe it could be cleaned up with some type functions like 'IsBlocking(comptime map: type)', but I didn't get that far.


Instead I ran into an unexpected aspect of type inference in Zig, which is that when I tried to call this function with a slice the inferred type was an array 

```
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

Three people helped me with this: 5-142857, pablo, and Tetralux.

It was quickly pointed out to me that the compiler had use the more general type of a pointer to an array because that type can be coerced to a slice.

This does make sense, even if it was surprising at first to see my array's size inferred there.

We talked through a series of solutions, including requiring the user to provide a slice, and then asking for its element type, or asking the user
to provide a struct type which contains functions with a certain signature that I would call within 'compute_fov'

I did not want to restrict the genericness of the function in this particular way, and I felt bad asking the user to do all of this extra work in order
to call my function- it felt like a rather complex contract between caller and callee.


After some discussion I felt like I was better off making the function less generic. One solution that did solve the original problem was to 
make a struct type holding the slice, and then pass a pointer to this type to 'compute_fov' and the other functions. That way the type was already
resolved by explicitly stating it in the struct definition, and the compiler could not infer a different type.


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

I thought that this was a pretty nice solution in a way- I did not even think to do this kind of explicit type level programming.
I feel like I'm used to trying to convince a compiler to infer the right types itself, instead of explicitly acting on the
type to get what I want. This seems like a good concept to have in the tool belt.


However, as Tetralux stated at the time, and I agreed, the level of abstraction here was not really warrented, and makes the code
harder to reason able. This is especially true if I were to use this NormalSlice function. Its possible that I could clean up
my type signature, split out the logic into separate type level functions, and end up with something that looks clean. However,
it would still require a good bit of thought to understand what it was doing.


# Zig Thoughts

One thing that is a little concerning here is that if I had not happened to use a slice type for my map, I would not have
seen the type inference problem. I would have probably written up and used this code without ever knowing that a small
difference in types would trigger this mismatch between inferred and given types.
This would cause a compilation error, not a runtime error, but it would prefer to not leave a time bomb in my libraries
just waiting for someone to go slightly off the beaten path.

