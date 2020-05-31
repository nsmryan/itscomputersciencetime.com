# Thoughts on Complexity in Software
I've been thinking about software compexity recently, so this post is an attempt to articulate my current line of thought.

In some sense, complexity is the currency of software. If you want something out of your software-
features, performance, really anything- you pay some cost in complexity. This complexity results in bugs, time for
reviewing, time for documenting, difficulties in maintenance and updates, and all sorts of other issues that create cost
in time and money.


I often see the advice to avoid complexity, which I think is good advice, but I think the more general strategy is to manage complexity.
Be aware of where it lives, be mindful of when you are adding complexity, and consider the tradeoffs involved.


I have been applying some techniques to manage software complexity at work that have come out of this thinking that we will get into below.


## Managing Complexity through Simplicity
Perhaps the single best way to manage complexity is indeed through simplicity. Software tends to have a high cost of interaction-
each line/function/module interacts with all others modules (in the worst case) and create very much superlinear increase
in complexity as they grow. Once software has reached a certain size, you can no longer keep the whole thing in your head
(whether that is the whole program, or just a single module or even a single function), and you resort to a lower-fidelity
mental model to continue reasoning about its behavior. The great thing about simple software is that you can sometimes use
the best mental model of all- the software itself.


This model is not perfect (the machine does not execute exactly as you think it does) but its about as close as you can come to a complete model.
However, simplicity alone does not appear to be enough. Complexity is something like information in information theory- its the underlying 'size'
of the thing, a minimum bound beyond which a concept cannot be compressed. This is a Kolmogorav style information entropy,
where the actual size is not computable in general. You never know when you have reached the minimal complexity, and you probably never reach it in practice.
Its something to strive for but not something to arrive at.


This idea leads me to see software has having a hidden measure behind the lines of code (correlated to their number, but not defined by it),
and that is the complexity that we are managing. Simplicity can get you very far, and it is a tool that should be in your mind, but if you are going to manage software
consider the underlying complexity within it and apply techniques, like simplicity, to organize it.


## Managing Complexity Through Re-use
Another technique to manage complexity is to decide where it lives- if it can be moved into a library, say, and isolated from the rest of the code through
a small interface then you can cut off some of the code from its interaction with other code, forcing it to interact through the narrow pipe of its interface.

This does not truely isolate the complexity- it can still leak out- but none of these techniques are about 100% success,
they are about applying many partial solutions to difficult problems to migate their severity in real world engineering.


I want to talk about the libraryification process, and how it can help with complexity in large software, but first I want to talk a bit about software re-use.
Like all techniques in software, re-use has its tradeoffs. While this is a big topic, the part I want to focus on is the liability of re-use.
High quality software is expensive to create, requires a great deal of engineering and testing, and necessarily involves a lot of time.
Faced with this reality, its very tempting to want to reduce these costs- to use better tools, better practices, or to just pay this cost once and
then benefit in the future. This last point is never as good as it seems- software is always bound explicity or implicitly into its environment,
and builds in assumptions that end up changing over time or between environments (such as between two projects).


The software around the software changes and invalidates its assumptions.

### Frameworks
One way out is to build a framework- then the rest of the software is built inside and must adhere to its assumptions in order to function.
This does work- for example I was using NASA CFS for some time for example, fitting myself into this framework and finding that it delivers on the promise of re-use.
However, there is always a cost, and I found that for my situation (the values I have for the project I was working on) the costs were higher then the benefits.


The trap that I found myself falling into here was that the amount of software re-use was very high- higher then on other projects I've worked-
but I was paying for all the software I was re-using in many ways. First, there was a lot of it- it was re-usable, but partially because it just
had so many features that it could be used in many different scenarios.
It had high re-use numbers, but partially just because there was so much of it. When I started to plan testing, reviews, documentation, and maintanence,
I started to realize just how much I was going to pay for all of this complexity I was carrying around in the software.


With the features I wasn't going to use, the sheer number of definitions and concepts involved, I started to get concerned that I was paying more than I was getting.
Every line of code in a software system is a liability- it costs complexity when reviewing, it may hide bugs, it needs to be documented,
it needs testing- and keeping code around that I wasn't using made me aware of just how high these costs were.


### Librarifying
This experience lead me down a different attitude towards software re-use- rather then focusing on just re-use, focus on containing complexity in libraries
and designing them to be thrown away. 
To accomplish the first part, containing complexity, I started to look for a certain type of logic in my code- logic that solves a clear problem,
that mostly involves modifying data (more then managing tasks, or processing from a queue, for example), and which could be designed for use in multiple contexts.


This is not set in stone- for exmample, generic queue processing system might be useful, but I was interested in supporting many operating systems and embedded devices,
where the main thing you can rely on is basic C. The hope is that by selecting these chunks of complexity and making them libraries,
they limit their internal scope (making them simplier as they have fewer interactions), they can have a clear interface (rather then mixing their
interface with the code that uses them), they can be documented, tested, and reviewed in isolation (paying these costs once and in small chunks).
They can then by used in multiple tools and modules, both on host systems like Linux or Windows, and on embedded systems like an ARM SOC board running VxWorks.


This sounds at first like a bit of a naive dream of a software engineer, but it has worked out very well in practice over several projects, and many tools.
I rarely write a tool at work without relying on at least 2 or 3 of these libraries and sometimes 5 or 6.


This is only part of the story however- we also have to talk about throwing software away.


### Throwing Software Away
I think its worth spending some time clarifying what it means to design software to be thrown away, and why what the benefits are.

First, this does not mean that the software should be poorly engineered- I don't mean to imply that that software shouldn't be high quality
because we are just going to throw it away. Instead, what I am striving for is to create high quality chunks of software that provide real value,
but do not embed themselves deeply into the rest of the software system.  Keeping the chunk of software- the library- focused, keeping the API small,
and keeping the logic internal help to isolate the library from the rest of the system. If the library's assumptions are no longer valid,
it does not have the right features, or does not have the right performance, it can be extracted and a new library written in its place.


In practice this can mean that I split the core logic of my modules into a library, and then write a module around it
(with tasks and queues and other things that don't go in the library). If this module is not useful, I would throw it away and start again,
or replace just the library and rewrap a new one.


Hopefully this provides some idea of how to create disposable software- keep it small, focused, well designed and tested, and attempt
to avoid embedding it to deeply in your application.


Its important to reiterate something here- this does not solve the problem of having frameworks, or the liabiliy of lines of code- rather it mitigates
these problems for some of the code, making the remaining code slightly less costly, slightly less complex, and slightly more flexible. This is
engineering, where silver bullets are rare, and lead bullets are worth their weight in gold.


## Costs of Dependency on Libraries
We talked a bit about the cost of dependency on frameworks above- the cost of the code, the lack of flexibility.


In general in software design, architecture is a great and powerful thing, which comes with great responsibility.
It can reduce size and factor complexity, but can also involve large upfront costs and almost invariably seems to reduce flexibility.
Frameworks are the embodiment of architecture, which all the associated costs and benfits. My current strategy for embedded systems is
to use a framework with as few lines of code as possible, and to lay on top of this framework modules that make heavy use of libraries
written for re-use and to be disposable. This factors complexity into a small core chunk, and then places as much as possible in well tested,
isolated containers, leaving, as much as possible, the remaining complexity (much of it system level complexity like the flow of information,
interfaces between components and to hardware, and all the interactions between software modules), which is where I want to spend my time thinking and debugging,
as this is the root of the greatest, most application specific complexity.


What about libraries? Just as frameworks took certain tradeoffs, libraries must have their own set.
Splitting code off and isolating it seems like an ideal thing to do, but each dependency has a cost.
This is a big topic in itself, and varies depending on language and tooling, but there is always a cost. 


Some examples of the cost- the managment of the versioning and linking of the library (even if the 'linking' is just compiling it into you project),
the inflexibility of libraries (if they don't do what you want in some application specific way, you have to fork or rewrite them or reduce their re-usability),
and you absorb the cost of creating interfaces (splitting code out doesn't make things simpler or smaller- it usually makes them more complex and bigger, but more isolated).


This last point is fairly important. Its easy to think of libraries as reducing complexity but they are also prone to creating complexity.
Splitting code off creates an interface where none might have existed before (or the interface was previously implicit). This creates communication, which is a source
of complexity even if the communication is just functions and types in an API.


Situations that might have been handled in one place, such as errno conditions, not might be checked and handled in each library. Error handling might be inconsistent,
logging may be done differently in each library, allocation might have different requirements, etc.


A library must make choices like how to format data, how to handle errors, what to do in edgecases.
If these decisions match up to the rest of the software, they can slot in nicely, but if they don't
then you wind up with a similar situation as with frameworks- you are locked into a way of doing things,
or you are always converting between two or more ways. This isn't to say the two strategies are the same-
the great thing about libraries, as we have discussed, is that they can be thrown away.


The reality is never so simple as "just throw it away"- the code has to be extracted, implcit assumptions and leaked internals confronted,
and a replacement has to be written. However, the cost is usually much smaller per library then the cost of doing the same with a framework.


## Conclusion
The general topic of complexity in software is too large to cover in a single post,
and I expect my understanding will be continually evolving, but I hope I got across a couple of points that have been important to me recently.


Software has a hidden inherent complexity, complexity is the currency of software, complexity can be managed,
all techniques for managing complexity are partial and have tradeoffs, combine techniques to manage as much complexity as you can,
and try not to lock yourself into an architecture because the world changes around your software and invalidates it over time. 


These lessons have lead me into building libraries at work, splitting logic into testable components,
and building a small framework that provides mostly services to other software.
So far this has panned out well, and I have gotten more re-use then any other time in my career.
However, I still don't know how to create a good framework, and I expect all of this to evolve over time.

