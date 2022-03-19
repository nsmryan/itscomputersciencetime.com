+++
title = "What I'm Reading/Playing"
[taxonomies]
categories = ["reading", "programming", "games", "computer science"]
+++

I wanted to record some things I've been reading and games I've been playing
recently.


## Human Resource Machine
I played through Human Resource Machine. Its a great, fun programming game with a
sense of humor. I did beat all the puzzles, but I stopped trying to optimized
as much after the first few and focused on finishing them.

```
https://tomorrowcorporation.com/humanresourcemachine
```

I also played Little Inferno a while back, which has a very similar style although
it is not a programming game. Given this companies track record I would consider
their game 7 Billion Humans, another programming game that seems like an extension
of Human Resource Machine.


## RISC-V Unprivileged Spec

I have been interested in the RISC-V ISA recently. This is partially because I am
using a RISC-V softcore processor at work and enjoying the experience very much.


I am drawn to the simplicity of the ISA, and the granularity of the specification- 
I think programming languages could use this kind of layering and combinging extensions
to allow a tradeoff in simplicity and static analysis while allowing more freedom when desired.


To learn more about RISC-V I read through the unprivileged spec up until the memory ordering
spec. I find memory ordering to be difficult, almost as if we have a complexity and tricky
business even in the simple concept of memory use, sacrificing simplicity and determinism for
performance. They may well be right to do so, but I still have a hard time understanding these
sections, unlike the rest of the spec.

https://riscv.org/technical/specifications/

