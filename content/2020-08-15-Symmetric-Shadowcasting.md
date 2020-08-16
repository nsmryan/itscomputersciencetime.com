+++
title = "Symmetric Shadowcasting"
[taxonomies]
categories = ["shadowcasting", "fov", "rust"]
+++
I recently posted an implementation of 
[symmetric shadowcasting](https://crates.io/crates/symmetric-shadowcasting)
which I simply translated from a [great tutorial](https://www.albertford.com/shadowcasting/).


This was a single day project that I undertook because the tutorial is really nice,
with interactive components and a full implementation at the bottom. I figured
it shouldn't be too hard to translate, and it would be nice to have it
in case I could use it in the [Rust Roguelike](https://github.com/nsmryan/RustRoguelike)
that I've been working on with my brother.


## Translation
The Rust translation was mostly straightforward. Besides some small syntactic
things, using 'let' and 'let mut', etc, there are some signifcant differences
just from Rust's type system.


### Dependencies
The Rust version is almost the same, using [num-rational](https://crates.io/crates/num-rational)
as its only dependency  as Rust does not have rationals as part of its standard library.

### Types
The Python version does not have to specify types for positions.
I considered making the positions generic, using
[num-traits](https://docs.rs/num-traits/0.2.12/num_traits/),
but I figured left it as a pair of isize instead, at least for now.
This makes the code a little simpler, if less generic, and makes
the user cast to a from this type. However, at least it
does not require a dependency.


The closures have fairly simple types, although I did struggle
with getting the types correct for a while and ended up not
being able to express the API I was hoping for.


### Local Functions
The largest change was that
while in Python can capture within its local environment without
issue, Rust is quite a bit more strict about this. Local functions are
not closures- they cannot capture. I could have written the functions as
closures, but decided to use separate functions instead.


This required adding arguments to some functions that otherwise
were passed by capture. I decided to not pass all the closures given 
to the main function (compute\_fov) to all the local functions,
as the type signatures would have been as long as the implemenation.


Instead I inlined several functions, such as 'reveal'.

### Closure Inputs
While the Python version can take two functions, knowing that
they may capture the same data, Rust will not allow that,
as it means having a mutable borrow along with another mutable
or an immutable borrow.

The is\_blocking function is likely to take a closure that captures
a grid or map of some kind so it can check the given position against
it.


The mark\_visible function is the problem here- it would be
nice if it could capture the same map structure, but to be
able to modify it. I was not able to get the lifetimes to
work out, so I left it the way it is.


The unit tests show how to do this- they create a map as a
vector of vectors Vec<Vec<isize>>, and a list of visible
positions Vec<Pos>. The mark\_visible function simply takes
the 'visible' vector and pushes positions to it.


### Testing
I added tests cases for the examples given in the tutorial. This
works quite well, and it gave me a great deal of confidence being
able to run the tests frequently.

