+++
title = "The Cliff and the Sea"
[taxonomies]
categories = ["Software", "Software Complexity"]
+++

This post describes a pattern I have observed in software when there is a high cost for a small thing on one hand (the cliff),
and a small cost to a larger number of things on the other (the sea).


## The Cliff
A software cliff occurs when making a small change requires a great deal of work.


A common example is a library or framework where a certain way of doing things, or solving a certain problem,
is very easy (perhaps just a single line of code), but solving a slightly different or bigger problem suddenly
explodes into a huge amount of work. 


This may be the result of something fundemental- sometimes slight variations in a problem simple make it more difficult
such as integer contraints versus real-valued constraints in optimization.
It can also be a result of an unevenness in a design- perhaps the library makes the common case easy, but as soon as you
need more from it you have to break into the "advanced" API, or even the internal API, to get what you need.


This can also occur in code that attempts to 'compress' itself by factoring out any possible redundancy.
While this can be helpful, redundant code can be like having a form of slack in an design- if there is no slack,
you have to redo a lot of work when you find you needed just a little more leway.

This is especially bad when the deduplication is done based on incidental duplication- when multiple places in the code happen to be similar.
In this case, the deduplication can be complex, and often requires sidestepping simply because it didn't capture any fundemental
concept but rather the incidental relationships between peices of code.

This is a bit abstract, but I think the concept of reducing redundancy only to find that you have created many
dependancies throughout a codebase to a function, say, that no longer serves the changing needs of all of
these locations is fairly straightforward.
Suddenly that piece of common code either becomes complex enough to handle all the different uses,
or the code reduplicates to avoid this tieing togheter of areas that should have been kept separate.


I find I have this problem most often in the most highly wrapped, highly abstracted code.
Inevitably I want to step just a little to the side of what the code is designed for because
of some particular aspect of my application, and suddenly my small step turns out to be a step
off a cliff into a world of additional work. Sometimes, this step lands me into the sea.

## The Sea
The sea, on the other hand, is when you have no highly abstracted, highly specialized code.


Rather you might have many options on what to do, a large API, a large number of operations possible at any time
(common in dynamic languages in my experience), and you have to figure out what the right combination is.


One way you might find yourself in the sea is by falling off the cliff- once the highly architectured,
highly designed solution no longer works, you go down a level of abstraction and find a less structured
world (like going from a typed language to assembly where the additional freedom includes additional complexity and manual labor).


Once in the sea, you have many small decisions to make which together may amount to a great deal of work.
You might have a highly specialized, bespoke solution that fits your needs exactly, with the performance
you need and the data structures specific to you problem, but at the cost of mental effort,
and trading the high semantic overhead inside of the library/framework with the semantic overhead in your own code.


The cliff is one way to end up in the sea, but I have also created a sea-situation for myself from scratch.
Some code devolves into a bag of functions and structures with no overall design, and becomes difficult to
work with because is not clear how to find the right combinations.
Types can help here by reducing the number of valid combinations, but this is no guarentee.

## Conclusion
Watch your step and stay dry.
