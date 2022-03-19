+++
title = "Intcode in Forth"
[taxonomies]
categories = ["Software", "forth", "AdventOfCode"]
+++
Anyone who participated in Advent of Code 2019 has at least a partially complete intcode computer.
This is a simple model of a computer with a few instructions, address modes, and a concept of input and ouput, which is then used to solve
puzzles by writing programs for it to execute in the latest advent days.
This post is about a complete implementation of this [computer in the Forth programming language](https://github.com/nsmryan/advent_2019_forth).


This was a journey for me in craftsmanship in programming, growing as a Forth programming, and learning how tools influence our thinking. This post is a fairly long, somewhat unorganized group of notes on different aspects of this project.


## Background
I am quite proud of myself for completing Advent of Code 2019 with [solutions in Rust](https://github.com/nsmryan/advent_of_code_2019).
Rust has become my go-to language for hobby projects- however, I feel an occasional need to write something non-trivial in Forth
I used to write Forth a bit for hobby projects, and there is something that I find compelling about the language.


This happens once or twice a year, but usually don't get to far with it. I'm out of practice with Forth and need to catch up before making any progress,
at which point the compulsion to use Forth usually passes.


This time I wanted it to be different- I would find something small enough to get me working in Forth again, re-learning enough to be productive, but
non-trivial to make it an interesting project.
The task I set out to solve is to create a full intcode computer, requiring IO, somewhat complex but approachable logic, and a small but non-trivial amount of code.


### The Setup
This solution was written in GForth on Linux. My experience with GForth has been good, there is a lot of documentation,
conviences like struct, asserts, and more advanced features like word lists, multithreading, FFI, etc if you want them.


I used a tiny little Lenovo S10-3, a netbook computer that was inoperable for many years.
I successfully replaced the screen recently and I'm using it for small projects like this. It feels like a good computer to write Forth code with- a small
device that limits your focus to what you are doing.


## The Experience of Forth
Part of the experience of using Forth for me is that it feels unlike any other language.
I find that it narrows your focus to a very small amount of information at a time- while in C/Rust/Python/etc a single function may have a
large number of local variables in play at a time, generally in Forth I find I'm focused on the top 1, 2, or 3 items on the stack.


This is both good and bad- on one hand I find it is hard to solve some problems, on the other hand, I'm forced to keep things simple,
to think carefully about the problem at hand, to factor code into small pieces,
and to build tools for myself that I otherwise might not bother with. This is a huge influence on how I think about a problem- the stack
is both a tool for focusing and keeping things simple, as well as a mostly orthogonal concern to the actual problem I am solving.


The tool building aspect is particularly important- Forth is like playing Factorio for me.
I'm always finding subproblems, building up pieces to connect, and creating something of greater and greater capabilties.
This feels like crafting and creation to me, in a way that I don't feel when using other languages.


Its not that I don't do this in other languages, but there is something about Forth that feels like crafting that I just don't
feel in my C programs. I think it is due to factoring in Forth- the small chunks of code that are combined to solve a problem.
It feels like cutting a large, high dimensional problem into simplier subproblems again and again until they become simple enough
to solve in a single line.



## The Stack(s)
One core ability that one learns when using Forth is how to handle the stack. As strange as it might seem when starting out,
stack manipulation ends up being completely natural. I am generally able to keep the stack in my head and manipulate it without
too much effort as long as I'm careful to keep it shallow.


It is very easy to let the data stack get out of hand, and I think that keeping a shallow start is a part of growing as a Forth programmer.
It easy to rely on the return stack for storage, and to end up writing a lot of stack shuffling logic that distracts from the problem at hand.
However, with discipline and some techniques, I was able to keep the stack fairly shallow
and to avoid to much stack shuffling in this intcode project.


Forth has a number of words for acting on the top of the stack, the top two items, and a couple words for the top three.
This matches how often these items should be used- you almost always need the very top item, you frequently need the top two,
and you often the top three.
The stack may grow deeper than three, especially with nested words, but if the number of items on your mind is often more then three
then the code falls off a cliff of complexity. Three seems to be the magic number where the stack becomes a burden instead of a
conveience.


The one exception to keeping the stack this short is double width items, like a string pointer and length, or a double width integer.
These are more awkard to handle, but the double width words like 2drop, 2dup, 2swap help in most cases.
The problem comes when you need a deeper stack, such as a single cell item that is lower then 2 double width items- in other words,
the less common situations that are normally easy to handle using, say, 'rot' become problematic when the items are double width.


## Data Structures
When dealing with data structures in this project I found that I often needed a pointer to the start of the structure,
which either means keeping it on the stack (using valueable stack space) or keeping it in a variable.


Keeping data in global variables is less of an issue in Forth then, say, C for a couple of reasons.
First, I never intended this code to be thread-safe. Second, in forth you can compile the same code multiple times if you want,
creating a separate set of 'globals' for each thread- compling and running are intertwined in a way that is unimaginable in C.


The technique that I found most useful was to build up data structures in a certain way, with a series of helper words defined for each structure.
By setting up each structure with this system I was able to create nested structures and have multiple structures in play without things
becoming a mess.


### Structure Definition
First, the structure was defined using GForth structs. The fields had names prefixed with '>' to suggest that using one gets you from the start
of the structure "to" the fields location (similar to >r meaning 'to r'). 


Here is an example for my ring buffer structure:
```forth
struct
  cell% field >ring-write
  cell% field >ring-read
  cell% field >ring-depth
  cell% field >ring-data
end-struct ring%
```

Then, a global was declared with the name of the structure (so for the ring buffer, the global was called 'ring'). This global holds the "current"
instance of the structure.  I used a variable for simplicity, although note that Forth's concept of a variable is looser then other languages,
and a value would also have worked. 
```forth
variable ring
```

I also defined a word to set the current active structure. For the ring buffer, this word was called >>ring, suggesting that we are going 'into' the ring.
This is similar to a concept found in OOP languages, and in Rust (which is not OO) where there is a way to reference
the 'current' value or structure under consideration.
```forth
: >>ring           ring ! ;
```

After all of this, a word was defined for each field of the structure which reads the global pointer to the current structure,
and offsets to the fields location within that structure.
```forth
: ring-write       ring @ >ring-write ;
: ring-read        ring @ >ring-read ;
: ring-depth       ring @ >ring-depth ;
: ring-data        ring @ >ring-data ;
```

In addition to these field accessing words, some additional words can be useful such as a 'ring-read-cell' which
gets the location of the current next cell to read from in a ring buffer. This is somewhat like having field accessors for
fields that are calculated when they are accessed.
```forth
: ring-write-cell  ring-data ring-write @ 2cells + ;
: ring-read-cell   ring-data ring-read @ 2cells + ;
```


With these definitions many words can be written without keeping any additional items on the stack. Instead of keeping a pointer to a structure
on the stack, it is kept in the global state as the current ringbuffer, or the current stack, etc.


This is absolutely critical- the stack depth needs to be defined by the problem itself, and any additional stack pressure seems to create
frequent needs to spill the stack off to somewhere else like the return stack or variables (or locals, but I have never used those).


An example of a word written in this way is 'ring-incr' which takes the current location in the ring buffer and returns the next ( n -- n ).
This accounts for wrap-around, otherwise this would just be 1+;
```forth
: ring-incr    1+ ring-depth @ mod ;
```
Note that ring-depth is defined as follows:
```forth
: ring-depth    ring @ >ring-depth ;
```
Which reads the global 'ring' (the current ring buffer pointer), and then uses the field accessor to offset to the 'ring-depth' field.
This then returns the ring buffers fixed length, which is used by 'mod' to implement wrap-around.


The conclusion for me was that taking this concept from other languages (Rust was my direct inspiration) did help me keep the stack
shallow and deal with more complex structures then I have in other Forth programs. I expect other forthers might see this as twisting
forth to be like other languages by building abstractions instead of just programming directly, but I found it useful for myself.


## Style
There are some style notes that I think are relevant to Forth programming.


### Single Line Definitions
First, I strongly prefer single line definitions whenever possible.
I use multiple lines in rare cases, and I feel that it is approporiate when using a case statement or for a unit test,
but generally it creates much more confusion in Forth than in other language.
Its hard to go into a part of the definition and understand what state it is in, and without this local reasoning the whole definition
is very complex.


There is a style I see in a lot of Forth where it is written more like C or other languages in which a definition may be many lines long.  
However, in Forth a single line can do a great deal of work-
you can loop through an array, and print its contents in a single line without playing code golf. Once a definition is any longer,
keeping track of the stack can become difficult, and hard to test. Breaking even simple definitions into multiple words leaves
a trail of testable pieces that may be re-usable. Its worth noting that many of these definition will not be specific to a particular set of words,
and may not be re-used any where else without a significant amount of set up or knowledge. This is expected- the problem is broken down
very finely into specific and re-usable pieces.


### Spacing
I follow Samuel Falvo II's example in several ways when writing Forth, and one of those is putting words in groups with their definitions aligned.
This creates a pleasing consistency within a set of related words, and I find it easy to find and read definitions.


Here is an example of this style- the definitions of the intcode machine instructions:
```forth
: op-add          arg arg d+ dst! ;
: op-mult         arg arg d* dst! ;
: op-input        in-ring >>ring ring-pop? if ring-pop dst! else ip-- need-input then ;
: op-output       arg out-ring >>ring ring-push ;
: op-jeq          arg 0 0 d<> if arg assert( 0= ) ip ! else ip++ then ;
: op-jneq         arg 0 0 d= if arg assert( 0= ) ip ! else ip++ then ;
: op-lt           arg arg d< if 1 0 else 0 0 then dst! ;
: op-eq           arg arg d= if 1 0 else 0 0 then dst! ;
: op-rel          arg rel-pos 2@ d+ rel-pos 2! ;
: op-end          INT_DONE state ! ;
```

## Crafting
If there is a single thing that makes me return to Forth again and again, even though I use other languages professionally and in my hobbies,
it is the sense of crafting I feel when writing Forth.


Forth does not help you- there are no types, there are no restrictions on how you
create control flow, and the base language has no variable, loops, structures, arrays, etc. GForth provides a lot of nice structures,
but even so you have to understand how they work on a deeper level to use them then in , say C or Rust. On the other hand,
in Forth you create what you want.


If you want structures to work differently then GForth's you define structures yourself. If you want OOP (I do not, but thats not the point)
you can define your own. This is not to say that this is necessarily easy- I've tried to create functional programming concepts in forth
and found that they don't mesh that well with the language. The point is just that when you want something in forth, you craft it for yourself.
There is a more personal feel, a lack of a need to create things for other people. I feel like I can just create things that work
just well enough for myself, and just fit my situation. In many languages, Rust and Python in particular, the solution to many problems is shopping
for a good solution on the community website. This can be very productive, but it is refreshing sometimes to build everything yourself from the ground up,
even if that means defining what an array is.


I feel this way even true for the logic of programs- to implement an intcode computer I need a way to parse the input. To parse the input, 
I need to find the numbers within the comma delimited list. To find these numbers I need to find the comma. To find the comma I need to loop.
Forth programming is like a stack in this way- I find myself solving small subproblems, testing them in isolation, and piecing them together.
In a sense, all of the programming is like this, but in Forth I feel this much more deeply.

Its hard to articulate how this is different from other languages, but its this sense of splitting problems into words, and those words into words,
and those into smaller words, until you reach small, composable concepts that creates a sense of crafting. This is more free-form then in other
languages- there are very few restrictions in Forth.


## Intcode
I haven't taled much about intcode itself yet. The intcode solution includes a parser for input programs, an interpreter to run them,
and technically a disassembler and assembler (to create programs for testing). I wrote these but never really used them, and did not keep them up to date.


An intcode computer can be created with intcode-new, which allocates a new intcode computer on the stack. I considered heap allocation,
but in this program everything is on the user dictionary (not to be confused with the data stack, for those less familiar with Forth).
There is also a global called original which stores a prestine copy of a new intcode computer with the problem loaded up.
For some problems you have to try multiple inputs to the computer, so the word 'restore' copies the original intcode back to the current intcode computer so you can try again.


Forth is particularly good at solving problems that require machine-like solutions, so this was a natural fit. I defined words for each opcode,
for loading from memory according to eahch addressing mode, for operations on the instruction pointer, etc. There are words for printing out
the state of the intcode computer, and for printing out pieces of its state if I wanted to look at different sections like input or output.


The core of the system is the 'run' word, which loops, calling 'step' to move the computer one instruction forward. The intcode computer has
a field called state which indicates if it is 1) running, 2) finished, or 3) waiting for input. This is useful for problems where the computer
runs for a time, outputs, and then requires new input to run again.

Input and output were originally defined as a stack, which was a conceptual mistake on my part. I ended up removing the stack data structure code and 
writing a ring buffer instead, whhcih is the correct structure for how the input and output works in intcode.


One thing that was nice about Forth is defining problem-specific words creates a kind of DSL naturally, with no extra effort on the programmers side.
Once I had words to load and store data, and to run an instruction, get the next instruction argument, or skip forward in memory,
each operation was a simple combination of these and read like a normal program. I could have gone further and encoded concepts like Forth
load and store (@ and !), in a kind of internal forth machine or somthing, but I did not.

## Error Handling
In C, I'm very much used to passing error codes from practically every function- error handling and pessimistic programming
is part of writing solid C programs in my experience. In Rust, the situation in nicer, and I would have no problem
threading error returns through my functions using Options.

In Forth, checking error codes in every function would be a nightmare- the focus of the programmer is narrow in Forth,
and any additional work like this would clutter definitions that are already a single line!

Forth does have an exception mechanism, which like most of Forth, can be implemented in Forth. I do not generally use exceptions,
but I could see myself using them in 'panic' situations in Forth where execution can not proceed.


Samuel Falvo has talked about a style of error checking in which he establishes preconditions in words, which seems to work well for him.
I did not try this in the intcode solution, except in one place. Instead, I used occasional asserts and let the code halt when it hit them.
This worked out well, caught some bugs, and let me define most words without having to worry about errors.

I'm not sure how this would scale to a 'production' program- I know people use asserts this way, especially in debug builds,
but perhaps there is some other systematic way to handle errors that is more natural in Forth, such as Samuel's solution.


## Day 7
I think my favorite solution in the repository is day 7. This solution requires permuting elements, and trying out each combination.
This was not easy for me to do in forth, but I came up with a solution that is kind of nice.

In functional programming, when creating a random shuffling of a list, we often want to avoid visiting each element multiple times.
I beleve this is called resevoir sampling, and it simply walks through a list, swapping elements with elements further on.
It is a bit surprising, but if you work out the probability of each shuffling, each ends up being equally likely,
creating an uniform sample from the distrubution of random shufflings of the list.
I used this in my permutations by creating every swapping of items with a subsequent item. This defines all permutations as
a number in the base of the number of elements, where each number is equal to or greater then its positions.


## Problems
While this was a fun project, it was not without problems. I certainly spent time debiugging, and many of the problems I had
would not occur at all in Rust. I found that in forth, you have to have patience, build yourself nice tools, and generally
take it on yourself to do a good job. This is a powerful experience, but also punishing at times.


I wanted to point out one particular problem I had on day 9. I had written my intcode computer as a single-cell computer (32 bit words),
and on day 9 you need to account for larger numbers. I remember doing this in the my Rust implementation- it too all of a few seconds
to change a type definitions of a cell from i32 to i64. In forth, I very *very* nearly gave up entirely before even starting to make this change.


This is a problem I find when using dynamic languages. Large refactoring is difficult because the type system is not there to point out
all the code that needs to change, and to hold your hand while you make the changes. I had a lot of errors that required mental
gymnastics to solve to get the double width values to work, and this required changing my ring buffer data structure and even defining
my own word for double width integer multiplcation (I'm not sure why its not in GFroth already, it wasn't that hard to define).

## Conclusion
I accomplished what I set out to do here- I re-learned how to use forth, I grew in how I like to write Forht, and I expanded
my own ability to write complex Forth programs by coming up with my own solutions to the problems I was having.


I would like to solve some of the other advent of code problems that require little console printouts, as I think that is a good
use-case for Forth ansd sounds like fun. I would say that I did not have good tools for this in Rust (I did not make nice tools for myself),
and I would have nicer printer outs and visuals in Forth. on the other hand, I would have a much hard time solving the actual problems,
and I expect I would strugge with the language and stack in a way that I don't in Rust- i can just solve the problem in Rust, re-use other
people creations, etc, and generally solve problems faster.


I don't epxect I will actually go on to solve more problems despite the allure- I will more on to other projects.
My hope is that this exercies will help me writen practical Forth next time I am struck by the need to use Forth
for something, and I hope I will post about it here when/if that happens.

In the meantime, keep your stack shallow and your code factored!

