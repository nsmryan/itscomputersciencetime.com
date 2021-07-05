+++
title = "Zig Type Level Programming"
[taxonomies]
categories = ["Language", "Programming", "zig"]
+++

I did some playing around with type level Zig recently in the
https://github.com/nsmryan/tint repository. Along the way I learned a bit about
the capabilities of comptime type information in
Zig.


The original idea was to see if I could create an integer type with a max value
without storing both the integer and its cap in memory. This would allow, for
example, array indices that cannot be larger then the array size that they
index. Keeping the maximum value in the type system has a few advantages-
the range of indices can be restricted without having to always keep it
in range, or check every single array access. It also reduces memory use if,
for example, many of these indicates were required with the same maximum
value. The disadvantages are that it only works when you know the array size
at compile time, and it will require a bit more complexity and typing.


I believe this does work out, although I am not planning on fleshing this
library out into something useful. I'm curious whether such a system could be
used for more complex "compile time proof" style programming.


The second thing I looked at with "type tagging", meaning adding a tag type to
another type. This tag does not effect the size of memory layout of the
original data, it is just to add type information. In my Haskell days I played
around with phantom types and Data.Tagged. I am not completely sure whether
Zig supports similar use cases, such as passing configuration data to a function
through the type system, or adding extra type checking to an embedded language:
this might be too much to ask of comptime.


## Type Level Integers

To reiterate, I wanted to see if I could define a type of integer that would
only contain values below a certain maximum value. Zig already allows
integers of different bit widths, but there is no builtin way to indicate a
maximum value that is not a power of 2.


This would allow, for example, array accesses of a fixed size type to be
checked at compile time.  To construct a value of such a type, one would have
to handle the error case that a give value was above a certain cap, so that
having a value of the type would constitute a proof that the integer it
contains is within the required limit.


I did not consider a minimum value- I was really more interested in creating
type-level natural with values from 0 up to the natural's value.


### Comptime Enum Construction

My first attempt was a little roundabout- I was going to construct, at compile
time, an enumeration with the number of branches corresponding to a given
integer.  This seems like it would be complex and bad for compile times, and in
fact it seems that it is deliberately disallowed by the Zig language.

```
pub fn Capped(comptime max: usize) type {
    var fields: [max]EnumField = undefined;

    // try to build enum fields at compile time?
    var index: usize = 0;
    while (index < max) : (index += 1) {
        fields[index] = EnumField{ .name = "", .value = index };
    }
    const enum_fields = fields[0..];

    const decls: [0]Declaration = undefined;

    var info: TypeInfo = TypeInfo{
        .Enum = Enum{
            .layout = .Auto,
            .tag_type = i32,
            .fields = enum_fields,
            .decls = decls[0..],
            .is_exhaustive = true,
        },
    };
    return @Type(info);
}
```

Once I had an implementation sketched out, I found that Zig provides an error
message linking to issue 2907. The discussion in this issue indicates that ZIg
forbids constructing complex types from TypeInfos. In other words, you can't
construct a struct or enum at compile type and then reflect it into a type.


This is probably a good decision- clearly this would increase the flexibility
of the language and likely allow many interesting libraries to exist, but would
also increase the language complexity and reduce the ability to statically
understand a program.


I left my first attempt commented out just in case it is interesting to review.
The type is called Capped and it takes a comptime usize.


A similar approach would be to define an integer type with a bit width of the
desired maximum value (say for a cap of 156, a 156 bit integer), but to be
careful to not actually store a value of this integer (it would exist only in
the type system track the desired maximum value). This second approach
might actually be viable, but it is more tricky then necessary.

### Associated Types

My second attempt was much better- just add an associated field to your
structure with the given type. In other words, the type itself is given a
constant field, which contains the upper limit of the integer type you define.
I called this a Capped, meaning something that carries with it a maximum
allowed value. A value of this type does not carry the maximum value, but
inspection of the value's type allows access to this data.


Just the relevant portion of the definition:
```
pub fn Capped(comptime T: type, comptime max: T) type {
    return struct {
        const cap: T = max;
        value: T,
```

The fact that types can be used as namespaces in Zig seems to be the enabling
factor. This is not restricted to member functions, as you can also defined
functions like 'new' or 'create' that might return an instance of a type without
taking one in as a parameter. Furthermore, this is not restricted to functions,
as you can define a field of a type. However, as far as I can tell, these
fields must be 'const'- they are not namespaced variables, but rather
static information held within a type.


Some tests showed that you can construct such a value, it takes no more space
then the underlying value, and that you can add, subtract, multiple, and divide
(using custom functions, not the builtin operators which are not overloadable),
and the value contained in the resulting Capped structure remains within limit
as expected.


## Tagged Types

As I defined the Capped type, I realized that it should also be possible to
generalize to tagging a value with any type level data you want. I did a proof
of concept called Tagged, which requires the user to indicate the underlying
data type, the tag type, and the tag value, and provides a structure containing
only the underlying data and an associated value of the tag type.

```
pub fn Tagged(comptime T: type, comptime Tag: type, tag: Tag) type {
    return struct {
        const Self = This();

        const tag: Tag = tag;

        value: T,
```

While this technically works, and you can construct values of such a type, I
could not figure out how to actually pass such a value into a function. The
problem is that the type of the value contains a value of the tag type
(confusing, I know), and there is no way to specify a type level variable as
far as I know.


My second attempt was to store only the tag type, with no associated value.
This could still be used to pass information around, especially if the type can
be turned into a TypeInfo and inspected.  The trick here was to use an
associated field of type [0]Tag. In other words, a 0 sized array that would
take no memory but would still keep track of the tag type.

```
pub fn TypeTagged(comptime T: type, comptime Tag: type) type {
    return struct {
        const Self = @This();

        tag: [0]Tag = undefined,

        value: T,
```

I still ran into problems here trying to extract the tag type. I'm not really
clear on why, but it seemed as if by using the tag type the original variable
was assumed to be comptime. No matter what variation I tried, I got an "unable
to evaluate constant expression".

### Partial Application of Types
One difficulty with type level programming is tracking and passing around all the required types.
In Zig, this is quite explicit where you must pass in all types you want to make generic as parameters to
generic functions, unlike Rust where type parameters are specified separately, or Haskell where they
are specified implicitly by using lower case type names (as well as in constraints).


To avoid duplication and clear up long type names it it nice to have some kind of typedef
for renaming complex types. In Zig this appears to be as simple as assigning a name to a type:
```
    const TypedefTag = TypeTagged(u32, i8);
```
TypedefTag is now the fully specified type of a u32 tagged with a type-level i8.


I also looked at partial type application. In Zig this simply requires a function that
takes in the remaining parameters and applies them:
```
fn Tagi8(comptime T: type) type {
    return TypeTagged(T, i8);
}
```
This Tagi8 function takes a type, and tags it with an i8. This may not be the most elegant
solution possible, but as Zig is not intended to be closely tied to theory the way Haskell is,
I actually think that this is a fine way to define such a type function- as a regular function
acting at compile time.

## Conclusion
Overall it seems that Zig comptime type information can be used for keeping type information
around and using it to guide computation.

However, it is clearly not intended for complex compile type manipulation like type tagging,
even if there may be some way to do this that I did not discover. This seems
like it might be the right choice. Comptime is perhaps the most complex part of Zig, and
it has the potential to make Zig code hard to understand statically, which might
subvert Zig greatest strengths. Seeing that comptime type manipulation was given
some limitations indicates that it may not be as big of an issue as I intially assumed.

