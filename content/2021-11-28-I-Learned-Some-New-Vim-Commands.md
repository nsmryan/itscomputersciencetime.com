+++
title = "I Learned Some New Vim Commands"
[taxonomies]
categories = ["vim", "programming"]
+++

I have been using Vim for over a decade now, and I have had several major changes in my
level of knowledge, use of plugins, etc.


Like many vim users, however, I only know a small portion of the entire tool. Recently
I have been investing time in myself and my knowledge, knowing that it does pay off
in the long term (partially inspired by [this talk](https://www.youtube.com/watch?v=Z8KcCU-p8QA)).


I have been reading the very good [Learn Vimscript the Hard Way](https://learnvimscriptthehardway.stevelosh.com/),
and I have been reading the Vim documentation, and I have a few little things to mention.


The first is simply '(', and ')'. These move one sentence back or forth. However, in software
this tends to move around blocks of code, which may be slightly faster then other movements.


The next is the extremely useful ctrl-], which jumps to a definition. I have been using this
with ctags, and it is a huge time saver.


The next command, which helps a great deal with ctrl-] movements, is ctrl-o and ctrl-i. These
move back and forth between jumps, so you can jump to a definition and then jump back.


One other thing I wanted to mention is that the book's description of operator mappings
asks you to read up on some useful commands like ` and ' with < and >, which help jump around
selections.


Happy Vimming!

