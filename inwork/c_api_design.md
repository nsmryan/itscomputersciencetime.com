# C API Design
This post is some notes on C API design. Its not intended to be comprehensive, but rather its more a record of some things
I've learned while developing a number of small libraries, and used a number of libraries, and found some things that work for me.


## Error Handling
There are a number of options for error handling in C, each with tradeoffs.
I won't go into too much depth here, but the system I've been using is to define an
enum with all error codes for a particular library, and using it as the return value
for essentially all functions (except in some cases where its more natural to return
a pointer then take a pointer-to-pointer as an input).


This system has some advantages over, say, and errno global error condition.
You don't need to pass a pointer to an error code in most cases, and you get a lot of resolution on what happened. 
