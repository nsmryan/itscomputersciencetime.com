+++
title = "RectCut Crate"
[taxonomies]
categories = ["GUI", "rust", "rectcut", "crate", "c"]
+++
I recently had a need for a simple GUI layout system, and I came across
[RectCut](https://halt.software/dead-simple-layouts/). I like the simplicity,
and I feel like I fit into the post's intended readership- I've tried to lay
out GUIs a few times with different mechanisms, and found that they often get
in the wat.

To try this algorithm out I implemented the functions in Rust as described in
the blog post- for details please read the linked post.


The result is [rectcut-rs](https://crates.io/crates/rectcut-rs), a Rust crate
on crates.io. I implemented the extended functions as well, and wrote some unit
tests as a sanity check. 


Ultimately I stuck with my own, similar, layout system that I already had
because it fit my specific need a little better, but I am always happy to have
a new crate so I wanted to post it here to record the accomplishment.

