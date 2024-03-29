+++
title = "My Programming Journey"
[taxonomies]
categories = ["Language", "Programming", "C", "Lua", "forth", "tcl", "lisp", "haskell", "c++", "embedded"]
+++
This post is an attempt to explain my person journey through the world of
programming, from high school up until the present day, about 10 years into my
career and maybe 16-17 years since I started learning about computing.


I've wanted to write this up for a long time, but it is so complex that I have
a very difficult time grappling with the entirety of the subject. Talking about
programming is immensely difficult, seemingly due to how large, complex, and
varied a subject it is. I don't intend to tackle the entire concept of
programming, but I will at least try to convey what I've learned over the
years.



## Prehistory

In high school I took some classes on computers, and some classes where I took
time to learn basics of computing.  I was drawn to the mystery of computers,
that they seemed so complex and difficult to understand.  This ended up being a
recurring theme for me- uncovering mysteries and wanting to understand the
basis for things. I was determined to understand these things, and wanted to
dedicate myself to understanding computers as deeply as I could.


In the specifically computer focused classes I learned basic concepts about
computer components and maintanence, operating systems, that kind of thing-
enough to get a CompTIA A+ certification to work as a computer technicion. I
did well, and was one of I think two people in the class that passed the test
at the end of the semester. 


I also took two classes with a teacher who was very influencial for me. I don't
recall the class names, except that one was about technical drawing, but in
these classes I learned a lot about logic gates, turning machines, programmmed
a milling machine in G-code to cut letters in boards, and generally started to
learn about computers.


Finally, I took a class on networking and learned about basic concepts of
network topology, routers, different technologies that have been used over the
years, and the basics of modern networking with IP and TCP/UDP.


At this time the attitude of people around me was that being a computer admin
was possible, but that programming was very difficult and may not be the right
path to go. I didn't have all that much confidence that I would be able to
program, but I was draw to the idea that it was difficult, just as I had been
drawn to the mystery and difficulty of computers in the first place.


## It Begins

My first experiences with programming were sporatic and without much real
understanding or instruction. After the g-code programming of a lathe in high
school, the next thing I recall was that someone that I worked with at On The
Border (a restaurant I worked at in high school) gave me a book on javascript
with some examples of simple website, and that I took some kind of introduction
to Visual BASIC class where I recall the enlightment of understanding how to
use a loop and other simple concepts.

I remember trying to learn C, but not understanding how to really start with
compilers and what was out there. I knew how to use a command line, but I
didn't really understand how to enter into something this complex by myself.

At this point I was basically able to program, enough that I knew simple
concepts and could make a webpage with Javascript embedded. This did prepare me
somewhat for the next phase of my education, at least more then many of my
fellow students.


## College

I went for a Computer Science major in college, with a minor in Math.
Christopher Newport University teaches Java as the main language, except for
some specific classes where you learned a variety of languages, a class focused
on C++, and some other tools here and there such as a language for describing
optimization problems.


### Java

I programmed a lot of Java, did assignments, games, etc, as one does in a CS
degree.  I did programming competitions with a team, and later lead the team as
a graduate student.


I wrote some genetic algorithms, a toy distributed "island" genetic algorithm
program to solve a simplified version of the game Lemmings described in
"Scripting the Game of Lemmings with a Genetic Algorithm". I'm sure I wrote
many other programs that I no longer remember.


What I recall from Java was that I liked learning to program and learning all
of its ideas, but that eventually I started to struggle to express increasingly
complex concepts in terms of inheritence heirarchies as I had been taught. No
arrangement of classes ever seemed to work out, and I tried to arrange Java's
Generics and classes to express very general concepts, and could not get it to
work. This experience colored my view of OOP, for better or worse, as something
that can be useful as a technique, but in the forms that I was familiar with it
wasn't something I wanted to use.

I found that not only could I not express concepts that I wanted to express,
but that I couldn't tell if my concepts were correct- there was no way to
really reason about Java programs in the way that I wanted to.

I have never worked in a place where the JVM was used, and I never had a desire
to go back to Java, so I effectively abandoned it after college and haven't
thought about it since.


### Python

Some time in college I discovered Python, perhaps in a class. I found Python freeing-
you could just get things done! It was so easy to start up a program, see something
working, and play around. There was a REPL, and I was hooked for a time. I made GUIs,
solved puzzles like that one with pegs in a triangle, and I believe solved Sudoku
with a simple constraint solving algorithm, including that extended Sudoku with
five squares.


Ultimately my opinion on Python has become that it is a good choice for science
code with its ecosystem and speed of development, and its often the fastest way
to get something put together like a tool or GUI. I think of Python
as programming made easy. Sometimes easy is good, but sometimes not.


However, I generally find that my Python programs are hard to refactor, and
after a certain size, I am less interested in trying to keep them together. I
expect someone that masters Python would have ways of dealing with this, but
its a problem I run into every time, and it makes me yearn for static types
every time.

I have other opinions here, and clearly my little Python section is too
short to really deal with the language. My opinion is mixed and changes
over time.


### Forth

I played around with many languages at this point. I tried Lisp, learned some
PHP, did a project in Perl, Prolog, probably others. One language that really
resonated with me was Forth. I loved the idea of low level programming (getting
to the basis of things), and the idea that you could understand the entirety of
a language, from assembly upwards (again, the desire to understand the basis of
things).


Some resources I used were:

[Moving Forth](https://www.bradrodriguez.com/papers/),
which is a fantastic series of articles on the concerns of implementing a Forth
interpreter in assembly language on different processors.

A [jonesforth](https://rwmj.wordpress.com/2010/08/07/jonesforth-git-repository/)
interpreter, which is an amazing example of bootstrapping a Forth
in assembly language just enough to get an interpreter going, and then
writing the rest of the language within itself. One moment that stood out
to me while reading its very well commented source was that the start of the
Forth code doesn't have comments, until just after the line of code that
defines comments. Before that line of code, the language didn't have comments,
to the author didn't write any, and just afterwards the language did have comments-
maybe this seems amazing, maybe it doesn't but at the time it was a kind of
revelation to me.


The [over-the-shoulder-forth](https://www.youtube.com/watch?v=mvrE2ZGe-rs)
tutorial was influencial for me in understanding the way you can manipulate the
Forth system, and in the way that Falvo organized his source code. Its really
a very good video, and I've seen it many times.


During this time I wrote a Forth interpreter for my Ti-83 calculator in Z80
assembly.  This was, and remains, one of my favorite projects that I've done.
It is an example of learning something deeply by doing- I feel like I
understood Forth more then the usual tutorial explaining how stacks work.
I learned a certain style of Forth from [Samuel Falvo](https://github.com/sam-falvo),
and from other Forth code, which keeps things simple, mostly one line definitions.
I learned about return stack manipulation, parsing words, and the "pearl of forth",
the "create does>" word. Ultimately, while I like Forth and do 
[some](https://github.com/nsmryan/advent_2019_forth)
[hobby](https://github.com/nsmryan/zzforth)
[projects](https://github.com/nsmryan/TAF) with
it, I've never used it professionally except for some small utilities here and
there.


Forth is one of those languages that can evoke strong feelings, good or bad.
I believe that the interactive development model, the simplicity/leanness, and the
ability to use Forth on basically any hardware really speak to its users.
I can't help but wonder what my embedded code would be like at work if we had
this kind of interactivity.

### Haskell

I don't recall exactly how I came across Haskell the first time either, but it
became perhaps the most influencial language for me. I felt like I had
discovered the true underlying nature of computation. It was a whole world of
thought that never came up in my education- lambda calculus, type theory,
algebraic data types, pattern matching, the connection to logic. 


This was like taking a biology degree, and then realizing on your own that
evolution was a thing. Programming languages seemed full of empty promises-
they would talk up all their features, all the power they give you, but
no technique was without flaws and there seemed to be nothing coherent to tie
things together or to know what the techniques really 'meant'.

It was shifting sand and empty promises, and I was very ready to hear Haskell's
story that programming was backed by an actual theory that you could actually
reason about. You could tell how your techniques worked, you could reason about
monoids, tell whether your type was a functor, etc, in a way that was lacking
in other languages.


I wanted very much to uncover the mysteries of this new world of thought. It
hhhhhhhromised that it would change how I thought, that it was difficult, and that
there was something special about this language, that is was the basis
of computing, all of which hooked me.


I think of learning Haskell as climbing the tower of abstraction. You climb
and climb, and climb and climb, and then you climb some more. Some of the
promise was delivered- Haskell permits abstraction at a level that is just
unthinkable in other languages I know. It truely did teach me important
lessons in thinking that I now apply in all languages- lessons about
control of effects, structuring and destructing of data to guide
the flow of a program, and how to think about the nature of the
task at hand in case it has a mathematical underpinning that
may guide our reasoning and suggest thoughts that are otherwise
hidden in the adhoc description.


#### Some of the Resources I Used

  * [Learn You a Haskell](http://learnyouahaskell.com/) as an early introduction
  * [School of Haskell](https://www.schoolofhaskell.com/) has some real gems, like
    how to compute a CRC incrementally, 
  * [Conal Elliot](http://conal.net/)'s [papers](http://conal.net/papers/), and
  [blog](http://conal.net/blog/) have some beautiful ideas. He seems to go from
  topic to topic, distilling each one into its simple essence, and then building
  it back up in a principaled manner. He invented functional reactive programming,
  he has amazing papers and videos on topics such as automatic differentiation,
  denotational design (also his invention), and most recently this compiling
  to categories concept that is amazing. 
  * [A Neighborhood of Inifinity](http://blog.sigfpe.com/) just blows the mind.
  Dan Piponi delves into math, quantum mechanics, and programming, combines
  them, and comes up with some incredible things. I spent a lot of time
  struggling through these posts trying to understand, and learned many things.
  For a more recent update, he talked about [stable fluids](https://www.youtube.com/watch?v=766obijdpuU&index=6&list=PLGRqfvsPiRSj_p5_bVvGxTUWKBs_Wx-Y-)
  which I think it worth a watch.
  * [Edward Kmett](https://github.com/ekmett) writes a lot of Haskell. He is
  an amazingly smart person that seems to be able to take Haskell to the next
  level, who has climbed that tower of abstraction and started to build the
  next rungs himself. His vidoes on succinct data structures, cache oblivious algorithms,
  propogators, [type classes vs the world](https://www.youtube.com/watch?v=hIZxTQP1ifo&feature=youtu.be),
  [compiling on modern hardware](https://www.youtube.com/watch?v=KzqNQMpRbac),
  the period where he [twitch streamed](https://www.twitch.tv/ekmett), all of them are great.
  Some articles from School of Haskell on
  [cellular automata](https://www.schoolofhaskell.com/school/to-infinity-and-beyond/pick-of-the-week/cellular-automata)
  and the [ST Monad](https://www.schoolofhaskell.com/school/to-infinity-and-beyond/pick-of-the-week/deamortized-st)
  also stand out in my mind. There is also his [blog](http://comonad.com/reader/),
  his famous [lens library](http://lens.github.io/), the list just goes on and on.
  * [Gabriel Gonzalez's](https://www.haskellforall.com/) blog, with introductions
  to many Haskell concepts and a lot of posts on the pipes library that was
  under a lot of discussion, along with conduit, while I was learning Haskell.
  * [Begriff's](https://begriffs.com/) blog has articles on QuickCheck,
  Vim and Haskell, practical concerns with Haskell, codensity monad,
  kand actually some nice
  C articles as well.

There are probably many others I've forgotten, but I'll just mention
[What I Wish I Knew About Haskell](http://dev.stephendiehl.com/hask/),
[Stephen Diehl's Blog](https://www.stephendiehl.com/posts.html),
[Lense Over Tea](https://artyom.me/lens-over-tea-1), [derivatives of
data types](https://www.youtube.com/watch?v=K7tQsKxC2I8), and
[derivatives of regular expressions](https://www.youtube.com/watch?v=b4bb8EP_pIE),
[a very general methods for computing shortest paths](http://r6.ca/blog/20110808T035622Z.html)
is a gem, and there is some [really advanced stuff here](https://www.twanvl.nl/blog/)
if your into that kind of thing.


#### The Struggles

My Haskell programming experience was characterized by constantly redoing my
programs, trying to cover every case, make them as abstract as possible, and
learn newer techniques to use before I needed them. This is exactly the
opposite of what I should have done, and its mostly my own fault and where I
was in my life at the time, but Haskell as a language seems to encourage
climbing the tower. I never released anything on the community website, or
finished any of my ambitious projects. I never really completed reimplementing
my master's thesis. Again, I don't blame Haskell for this as much as myself,
but none-the-less this was my experience in the land of Haskell.


The other problem I had with Haskell was that it seemed hard to inspect. At
work I was using C and C++, and I had become used to knowing what my programs
were doing, knowing where control flowed, knowing the layout of memory and the
depth of the stack, etc.  In Haskell, you give up a lot of control, and get a
lot in exchange, but you can't really control the order of evaluation (lazy
evaluation) or just print things out or really understand the whole state of
the world at any point. This really rubbed me the wrong way after a while. Pure
function are great, and testable, and decomposable into small pieces and built
up into large complex programs. However when they go wrong I had a hard time
debugging them. Again, this is partially my own failing, but it is what I
experienced.


This all sounds pretty negative, but I expect I would have just had some other
problem in any other language. The effect was what seems to me a kind of
burnout- I no longer wanted to deal with the Haskell ecosystem and I was tired
of climbing the tower of abstraction. Ultimately I had become a software
engineer interested in solving problems, and I felt that I wasn't smart enough
or determined enough to use fancy techniques in my area of work and see the
fruits of that labor above the tools I already had.


#### Lessons Learned and Moving On

Haskell taught me many things. It taught me about modeling with algebraic data
types, which now are a core means of thinking for me, the importance of pure
code vs effectful code and the management of effects, program flow following
the induction reduction of data structures, and about thinking about what was
behind the program, what mathematical underpinning might exist that ties it to
other concepts or frames it in a manner that would otherwise not be clear from
its details.


These lessons still pervade my programs, where I factor my C, Rust, Forth, etc
into pure and impure parts, where I consider the algebraic structure of my
data, the variance of my types, the composability of concepts. I expect I don't
do this nearly as much as someone steeped in Haskell, but its become part of my
thinking. I don't know that I've ever come up with any amazing revelations from
this, but handling state and the control of purity vs effects is a pervasion
concept in programming and is made explicit in Haskell in sharp relief against
other languages.


At some point I went looking for other languages. Ones that would give
me control, that might apply to my embedded programming world, that
I might really finish projects with. I was ready to descend into Rust.

### Rust

I had come across Rust occasionally, usually in reference to its 
inspirations from Haskell. I had been turned off by its syntax
several times, and the investment to learning its toolchain.
At this point a system's language really appealed to me- the
idea of mixing the control and determinism of C and the
thinking of Haskell sounded perfect. This didn't exactly
pan out how I thought, but no language is perfect.

I started learning Rust just as the None Lexical Lifetimes change
was being made, so I had some experience in hardcore Rust while
not having to suffer too long. I read through the tutorials,
and did what I find to be a normal process- failed and failed and
failed, rewriting simple programs again and again until I had
a handle on things.

I really like looking over library ecosystems, so I did a lot
of searching through crates. After a short time, I started
to actually upload actual crates of my own- something I never
did for any other language.

If you are interested you can find my crates [here](https://crates.io/users/nsmryan).
My favorite is the [symmetric shadowcasting](https://crates.io/crates/symmetric-shadowcasting)
one- the others I never actually made much use of.


I have also been making a roguelike game in Rust for
several years now which can be found [here](https://github.com/nsmryan/RustRoguelike).

I have a few takeaways from my Rust years- I like a lot
of things about Rust, but the honeymoon period is over
and I have a more balanced feeling then I did at first.


I agree with many of the decisions in Rust- the lack
of exceptions, the use of enum/sum types, the interest
in error handing, the lack of OOP, etc. Rust feels
like it gives a lot of the control of C, while detecting
an amazing range of errors or potential errors.


However, it is not perfect. I sometimes yearn to control
memory layout C style, for a language with simpler types,
for simple tools. I think Rust has a really significant
section of the system's programming world and I sometimes
inhabit that area, but its not necessary the only solution
that I'm interesting in.

TODO crates, formalism, complexity, control

### C

I have programmed in C++ for a good part of my career, nearing 10 years.
Most of this time I used C++ like it was C with a couple convenient extensions.


During this time I learned about programming within bounded, known systems that
you encounter in embedded software. At least, when you do embedded for custom,
unique hardware that takes years of development as we do at NASA. I learned
about Realtime Operating Systems (RTOSs), deterministic scheduling, how to use
semaphores, queues, mutexes, interrupts, etc.  Nowadays I miss the RTOS
scheduler when I'm in Linux or Windows (even Linux with PREEMPT\_RT). The
VxWorks APIs are so much simplier then POSIX, more limited, more specific, and
more deterministic. Its actually a pretty nice environment in some ways.


#### Settling In

Eventually I was programming my own flight software, and I had some choice in
its architecture and technology choices. At this point I took the flight
software system I had, written in C++, and converted it all over to C. This was
the start of really learning C, its standards, restricting myself to C
everywhere.  I found that I liked my software much more, I trusted it more, and
I felt more at ease knowing that it was simple and consistent everywhere. I
won't spend a lot of time trying to justify this here except to say that I
entered a C period which I am, at the time of writing, still largely within.


Returning to the very beginning, I found C quite difficult, as you might expect.
You need to be constantly on guard and constantly thinking about the location
and layout of memory (at least in embedded systems), error codes and error
handling. Its manual and dangerous, and gives you only a handful of tools.


Eventually however, I learned to keep much of this in mind at all times, so
much so that it can be hard to turn off in other languages. I learned to
appreciate the (relative) simplicity of C. It is not a programmable 
programming language, it does not have fancy features. It can create a mess
of a codebase in very few lines. However, if it is written with discipline
and experience, it can produce the kind of determinism and uniformity that
I want in a flight software system- the kind of system that runs for months
or years without errors. 


This is at once a level of thinking where the abstraction of Haskell is
unimaginable, while providing a level of precision that is in turn
unthinkable in, say, Python. The limited means of abstraction means that
the number of actual combinations that occur in my programs is fairly limited,
and this leverages simplicity in a way that I haven't seen in other languages
(except in Forth, where C seems terribly complex).


C has many flaws, and does not provide means of escaping its flaws.
It has great subtly and does present a number of significant challenges.
In the context that my C code operates however, I can control almost all of
the code, I can enforce standards, do static analysis, review, documentation-
all of the great deal of manual work needed to make reliable C. The result
is that these programs use the same memory, the same queues, the same functions
again and again, and check for errors everywhere, and have relatively few defects.


I do wonder whether I should be doing something else. Should I use more formal
methods? Should I use Ada? What about Rust? Am I locked into old patterns that
require too much work and provide too little value, or are the shiny new
tools not hardened enough for serious engineering? Are the hardened
tools not the ones I'm using? Am I missing the right place in the landscape
and wasting my energy? My explorations have shown me other options out there,
but for now I'm still in the land of C, and I am staying there for flight
software for now.


### The Retreat

After all of my wanderings in the land of languages, the complexity I saw
in Haskell, C++, and Rust, I made a strategic retreat into simple tools.

I wrote mostly C, went back to Make from CMake, started doing some diagrams in
GraphViz instead of Visio, documented in Markdown text instead of Microsoft
Word, and continuing my practice of using Vim and the shell with no IDE. I
moved partially away from licensed tools, and focused on tools I knew I could
use anywhere. I was largely limiting myself to arcane tools, with all their
janky subtlies, the vestiges of their evolution, their sensibilities from
before my time. I accepted them not because they are new, shiny or beautiful,
but rather because I was ready to accept them as they were.


I started doubling down on these technologies, and focusing on stability and
software reuse. I wrote C libraries that rely on as little as possible, that I
expect to run on Windows, Linux, VxWorks, FreeRTOS, basically any OS or CPU,
that I would use in embedded systems and in ground tools. Software that I would
expect to build and run in 5 or 10 years and watch it work the same as it
did when I wrote it. This was not my experience with Python or CMake or
many other tools, and I was tried of it. As with much of my complaints- I
could likely have written better code in these other languages, but I was tired
of trying and failing, and I was ready to retreat into the tried and true.


Part of this retreat was into Software Engineering: from theory into practice.
I documented these libraries, wrote automated testing, static analysis,
including build, test, and use instructions, I used them in mulitple places.
This can be tricky in C, especially in the subset that I use, which makes very
little or no use of macros, function pointers, and dynamic memory, but it is
possible for the problems I wanted to solve.


I have gotten real reuse from this. Software in C can be used in flight and in the ground,
in class B and below if developed properly, it TODO


While this was a useful strategy for a time, C really shines when its level of
abstraction approximately matches the level of abstraction of the problem at hand.
I started to struggle against the limits of C when I was writing tools or 
prototypes or anything except flight software. I started to collect a series of
libraries to extend my C with new capabilities, keep things cross platform, keeping
things to simple source files with no dependencies or special build system.

Most of what I use at work I wrote myself, but the code is not publicly
available, so here are some libraries that I've found and used that are
available:

  * [socket99](https://github.com/silentbicycle/socket99) to make socket
  programming a little easier.
  * [ini](https://github.com/rxi/ini) for trivially adding a configuration file with
  simple associations between names and types.
  * [log.c](https://github.com/rxi/log.c) for logging (info/warn/error/trace style)
  with no setup, and a few extra features if you want them.
  * [optfetch](https://github.com/moon-chilled/OptFetch) for trivial command line
  argument parsing.
  * [unity](http://www.throwtheswitch.org/unity) unit testing because its intended
  to be useable on embedded systems, and has different levels of complexity
  that you can move between when you start to need more features.

I rarely need a growing buffer, but there are several out there, often single
header style like Skeeto's stretchy buffer (buf.h). Instead I usually use a
queue implementation that we wrote at work which is OS-independent (designed
with RTOSs in mind) which is a simple shim over the OS. I'm not looking for the
greatest throughput, I'm looking for determinism and simplicity, so there
are no lockfree queues or anything fancy, just basic concurrency primitives.


Even with all of this, writing in C is a little exhausting for some problems, and
I felt a pressure to fill a more dynamic, easier path that I could choose for
a subset of problems. This is when I started to look around at scripting languages,
wondering what I should be using. Python is a strong choice for many reasons, but
I wanted to open up the search to whatever fit my needs. This is when I started
to learn TCL.


### TCL

I find TCL to be a humble, unassuming language. It does not seem to feel like
its the ultimate language. It does not seem to have a single philosophy or 
"big idea" that guides it unless you count the "everything is a string", which
is neither true nor particularly inspiring.  The very name "Tool Command
Language" tells you that this is a language engineered for practical
use to solve problems from the very beginning.


This might seem like a negative introduction to TCL, but that is the point.
I was tired of being promised great things, and TCL's pragmatism was appealing.
I was looking to fill a hole for a certain type of work- not an intellectual
goal or a mathematical one, but a recognizion of the pressures that are put
on me when I am solving problems. C's ability to integrate is a huge appeal,
and TCL is in a somewhat similar situation- it can be easily extended,
embedded in other programs, or connected through communication (mostly
strings, but its binary parsing is perfectly fine as well if not spectacular).



The particular type of work I was thinking about was approximately this:
lab work where you are experimenting with hardware, tying together tools,
integrating C with other systems, and putting together custom tools
to solve specific problems. Rich Hickey made a distinction in a talk of his
between correct programs and effective programs, where effective programs
work in the context that they operate on the problems they were designed to
solve, whlie correct problems attempt a more universal correctness regardless
of input or context. In his mind, almost all value was in effective programs, but
I think in flight software, and in some other domains, correctness has its place.
I mention this to say that this provides a framework to understand the problem I was
having- I knew how to write C that, whlie certainly not 100% correct, had a low
defect in practice and underwent a great deal of effect to a few gain extra percent
of correctness. The problem was that this does not fit into situations calling
for purely effective programming, and the mismatch was causing friction for me.


TCL does not try to own the world, like MatLab, or really almost any other language.
It integrates and works together in a way that is honestly a little hard to explain.
I would say I'm at an beginner, towards intermediate, level with TCL, but I have
found that TCL is effective. It solves engineering problems like packaging software
for deployment (single file executables StarPacks or pacakged distributions with TclKits),
it was always intended to interface with C (loading libraries and calling C functions, with
support for binary data that, whlie not exactly complete, it simple and well documented),
and interfacing with command line programs, or programs using other interfaces.


TCL is a batteries included style, at least with the distributions I've used. It
does not go as far as Python, to my knowledge, but its clear that it was 
forged in the fires of practice. It intents for you to spawn off processes,
interact with command line programs, write servers that handling connections
with an event loop, interpret binary data, the list goes on. The feeling
that I could package up a GUI and run it anywhere is freeing, or a tool,
or whatever I needed.


TCL is not without its problems. Its a very loose language, its not particularly
fast, it does not seem to have a community structured in the more 'modern' way
that, say, Rust has. I find it difficult to represent complex data structures,
even with its structures, dictionaries and lists. I'm also not at a level where I
can debug programs particularly effectively. However, it brings a level of
dynamisism to my C, and lets me put together custom DSLs for my needs,
packaged into executables with no dependencies. Other languages would allow
this, but for some reason TCL feels like it doesn't hide things- I can just
read up on how it does something and use it- there is no magic.


## The Future

### Lisp

I've explored many areas of the programming landscape, but I've always stayed
away from Lisp. Its a whole land outside of the static type direction I went
with functional programming, away from simplicity and determinism, right into
the opposite direction of ultimate power.


What I'm afraid of with Lisp is that ultimate power comes with ultimate
responsibility- that my meta programming would only create meta problems,
and meta-meta problems, and so on. That the untypedness would lead to subtle
bugs buried deep in highly tight, macro heavy code that generates the generator
for the code that generates the bug. I'm afraid that if I build my programs
as a series of languages each language will be poorly specified, each
without proper documentation, and each with no way of understanding
how to hook it up to anything else. I'm afraid of reading a libraries
API and not having the slightest idea how to use it because the types
won't tell me what actions are valid.


I have certainly seem the claims that Lisp is different and special, and
the claims from all sorts of other languages that their language is too.
Sometimes this is true- Haskell bend my mind in one way, Forth another,
practical application of C in another, Python in yet another, etc. I
don't doubt that Lisp could bend it yet again, which is something I am
always interested in- thinking in as many ways as possible. 

What I'm afraid of in Lisp's differences is that the deep revelation
people think they get from Lisp isn't so deep, that they make these
implicit assumptions when they talk about it. There is this sense to
me that Lispers want power, and more power is better. I think this
may be encoding their programming environment, the pressures on them,
the structure of their domains. I want, or I believe that I want, restrictions,
discpline, correct software with the least power that achieves my goals.
The domain is complex enough, with its own subtly, and if I raise the
abstraction too high I'm afraid I will cover the complexity too deepy
to debug it. That, and the fact that Haskell's call Haskell the redder
red pill, and I've already swallowed that one.


With all this said, I've been looking at Janet as a Lisp implementation
that integrates well with C, that I can compile easily, and which seems
to provide some nice facilities for common problems. It likely doesn't 
quite cover the full range of things I might want for a language in this
class, but I would like to try it out as a productive language if the 
situation arose.

### Lua

I have a small amount of exposure to Lua. I've played around with Lua's LOVE
game framework at times, and found it to be a great deal of fun.

Recently I've been looking at Lua from a different perspective- as a
scripting language.

### Zig

Recently I've been very interested in Zig. It is pragmatic, deterministic, and
low level, similar to C. C is full of problems, and there seems to be a growing
mismatch between C and how computers really work, as well as growing room for
new contenders with different values and design tradeoffs. Rust already has one
potential set of values and solutions, but currently my own values match closer
to Zig's.


Zig appears to be tackling the base problems in our software ecosystem-
problems with linkers ([this article mentions some](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&ved=2ahUKEwj2npn60Kn0AhVqpnIEHS4bAgQQFnoECAQQAQ&url=https%3A%2F%2Fwww.cs.dartmouth.edu%2F~sergey%2Fcs258%2FABI%2FUlrichDrepper-How-To-Write-Shared-Libraries.pdf&usg=AOvVaw0vpDYx3_-_7cIIdmN0_fbB)),
with cross compilation, with inefficiencies that don't buy us anything.
This is a huge amount of effort, but if the situation is never challenged we will
just have to live with our current mess.

Zig is a pleasure to write. It requires relatively little googling or
documentation hunting. For the most part, it is a small, explicit language.

Zig is not without some issues of course. The comptime feature is quite powerful,
and it can lead to some fairly complex code. I'm not sure that this is necessarily
a problem, but it seems likely that production use of Zig may need to restrict
use of comptime just as we restrict use of features in other languages.

I don't have the experience with Zig to know how its use of comptime to
implement mechanisms like traits works in the large.

I think Zig is the open source project I would most like to contribute to. I am
spending my free time on other things, but perhaps one day I will submit a PR.


