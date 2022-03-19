+++
title = "What I'm Reading/Playing"
[taxonomies]
categories = ["reading", "programming", "games", "computer science"]
+++

I wanted to record some things I've been reading and games I've been playing
recently.


## Algorithmica HPC Book
I just finished reading the last article in an absolutely fantastic book on algorithms
and the effective use of modern hardware. This book builds up concepts like cache
hierarchies, SIMD, TLB, and other concepts as well or better than any resource I've found.


In particular, I think the treatment of SIMD was especially approachable. This is a topic
I have usually bounced off of beyond the basic idea, but this book makes it seems like
something a mere mortal could apply with some practice and time.


I also really appreciated the consistent display of real performance data with explainations.
It is easy to feel like modern hardware is simply invisible to programmers as you have little
control over a lot of its internals. The graphs in this book expose some of these internals
and explain them in a way that uncovers their hidden workings and dispells at least some 
of the mystery. Effects such as multiple caches, TLB sizes, and various pipeline hazards can
be clearly detected in these plots with reasonable explainations on where and why they occur.


I normally live in a world where determinism and 
simplicity dominate over performance, but I like to know a bit about modern hardware and how
the field has changed with it, which can be hard to find when other resources are stuck in the
past.


I admit that I didn't try out the algorithms for myself, and I certainly didn't understand
every derivation or snippet of code. However, if I where in the HPC world, I might want
to read each article multiple times and pore over the code- it contains a lot of nice
nuggets of knowledge, tricks, and practical performance concepts applied in digestible
chunks ready for learning (more so then a practical implemenation with all the complexity
handled and all the concepts implemented together).


This book really is an amazing resource and worth checking out.


[Algorithmica HPC](https://en.algorithmica.org/hpc/)


## RISC-V Unprivileged Spec

I have been interested in the RISC-V ISA recently. This is partially because I am
using a RISC-V softcore processor at work and enjoying the experience very much.


I am drawn to the simplicity of the ISA, and the granularity of the specification- 
I think programming languages could use this kind of layering and combining extensions
to allow a tradeoff in simplicity and static analysis while allowing more freedom when desired.


To learn more about RISC-V I read through the unprivileged spec up until the memory ordering
spec. I find memory ordering to be difficult, almost as if we have a complexity and tricky
business even in the simple concept of memory use, sacrificing simplicity and determinism for
performance. They may well be right to do so, but I still have a hard time understanding these
sections, unlike the rest of the spec.

[RISC-V Specs](https://riscv.org/technical/specifications/)


## ATMega 328p User's Manual

A while back I read through the user's manual for the microcontroller in the Arduino Uno.
I've had Unos for a long time, but I never bothered to read through this manual. In an 
effort to be more throughout and disciplined in my interests, I decided to read through this
document cover-to-cover.


I didn't understand everything, certainly, especially not the electrical parts, but I read
through them anyway.


This was an interested experience, and I wish I had taken more notes. I've been doing better
at keeping my own knowledge database for this kind of information, but at the time I wasn't
keeping up with it.


What I rememeber is a lot of interesting hardware details, a lot of details on when certain
conditions apply and combinations of settings with different, subtle effects. I found
the boot process and the programming process especially interesting. This is the kind 
of information that makes me feel like with enough time and investment I could
do something like flash an Arduino manually, or write assembly programs for it.

[ATMega328p Manual](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&ved=2ahUKEwizzeSO6tL2AhXnrHIEHefODjkQFnoECAYQAQ&url=https%3A%2F%2Fww1.microchip.com%2Fdownloads%2Fen%2FDeviceDoc%2FAtmel-7810-Automotive-Microcontrollers-ATmega328P_Datasheet.pdf&usg=AOvVaw1saIayRRDKrz7YCcviikuY)

## Human Resource Machine
I played through Human Resource Machine. Its a great, fun programming game with a
sense of humor. I did beat all the puzzles, but I stopped trying to optimized
as much after the first few and focused on finishing them.

[Human Resource Machine](https://tomorrowcorporation.com/humanresourcemachine)

I also played Little Inferno a while back, which has a very similar style although
it is not a programming game. Given this companies track record I would consider
their game 7 Billion Humans, another programming game that seems like an extension
of Human Resource Machine.

## SpacePlan
I played through SpacePlan, which is a very fun and visually interesting idle game.
It doesn't take long to play through, and if you like idle games at all I recommend it.

[SpacePlan](http://spaceplan.click/)

I enjoy the simple visuals of the game, and the way that these kinds of games often
change as you play them. This game changes your plan and goals again and again in
fun and surprising ways.

## The Witcher Series

I'm through the first three books of the Witcher series. I enjoyed the first game so I wanted
to check out the books as well. They are good solid fantasy mixed with a little science, and
with enough mystery, characters, and plot to keep me engauged. I enjoy the slow unvealing of the
world, the plotting an machinations, and even the misinformation given to the reader. This all
contributes to a 


Overall I just like the Witcher himself.


## Others

I've been on a little bit of a programming game kick, so I have also started TIS-100. This may
be the best programming game that I've played, at least in the sense of really feeling like programming
and stretching my mind. This is a pretty pure programming game- there is no attempt to hide that you
are writing assembly language. Beyond that, the limitations of the processors and the need to distribute
any task beyond trivial ones unto multiple processors forces me to think carefully about problems and timing,
which is not present in any programming games I know of.


I am far from finished with this game, but I am intrigued by the story and I get a nice sense of accomplishment
from the puzzles.


I've also tried out [Baba is You](https://hempuli.com/baba/), which is great. I would recommend it to anyone
that likes puzzle games at all. The changing nature of the rules, and the need to break out of one's initial
mindset about a level to work with the fluid nature of its world is unique.

I got this game as part of the [Ukraine Bundle](https://www.humblebundle.com/stand-with-ukraine-bundle)

