+++
title = "Zig Type Checked State Transitions"
[taxonomies]
categories = ["zig", "types", "invariants"]
+++
I attempted to create type-checked state transitions in Zig, and 
[wrote up my findings](https://github.com/nsmryan/zig_state_transitions).
This idea is something I've seen in Haskell and Rust, where
functions take in and return types parameterized by a state,
such that only valid sequences of functions can be called. This
is intended to ensure certain invalid sequences of called for
a particular API will be cause at compile time.


I will leave the repo's readme to explain most of the attempts, but
overall it appears that as long as each function only maps one
state to another, this works just fine in Zig. Otherwise, I can't seem
to make this work at all.


I did learn some interesting things- I will want to try out the
strategy in [fifth_attempt](https://github.com/nsmryan/zig_state_transitions/blob/master/src/fifth_attempt.zig)
again some time. It involves type level functions, which is something
I haven't thought much about since I used to use Haskell.

