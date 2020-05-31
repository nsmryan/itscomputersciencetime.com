+++
title = "A Rust Gotcha"
[taxonomies]
categories = ["Software", "Rust", "Software Complexity"]
+++
I ran into a bit of a gotcha in Rust the other week, which really confused me.  
I only realized what was happening because I had run into the same problem when I was newer to Rust.

The issue is when using a 'match' statement, and matching against enum names (variants with no fields).
I believe this situation also requires the enum to be in scope, which I see in other Rust code and only
occasionally do myself.


If you ever get an enum name wrong, or if you remove an enum variant that was previously covered in a match,
then the statement that previously matched on the enum variant, then that match arm is interpreted as a
variable match instead of a variant match (aka constructor match in more Haskell terms).


An example would be the following code:
```rust
enum ExampleEnum {
    A,
    B,
}


fn main() {
    using ExampleEnum::*;

    let value = ExampleEnum::A;

    match value {
        A => println!("A"),
        B => println!("A"),
    }
}
```

when you remove the variant B, you end up still matching the second arm. This arm is now a variable called B, which matches any pattern.

You do get a warning, luckily, that the variable name does not match the expected form.
I am fairly familiar with pattern matching from my time with Haskell, so clear why this is happening, and the fact that the new match arm will alway match.


Overall, I would like some way to detect this situation more directly, but I also pay attention to warnings,
so I'm not all that worried about running into this problem commonly, but bite me twice now, and its worth being aware of.











