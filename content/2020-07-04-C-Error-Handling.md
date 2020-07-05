+++
title = "C Error Handling"
[taxonomies]
categories = ["C", "API"]
+++
# C API Error Handling
This post has some notes on C API error handling design.


Its not intended to be comprehensive, but rather its more a record of some
things I've learned while developing a number of small libraries, and used a
number of libraries, and found some things that work for me.


I actually intended this to be a broader discussion of C APIs, but error handling
is enough for a full post.


## Error Handling Strategies
There are a number of options for error handling in C, each with tradeoffs.
The system I've been using is to define an enum with all error codes for a
particular library, and using it as the return value for essentially all
functions (except in some cases where its more natural to return a pointer
rather than take a pointer-to-pointer as an input).

I'll go breifly over some other options, just to record some C API design notes. I
won't go into any performance implications. This has been looked into, but its not
the primary concern I usually have- I'm willing to lose a few percent of performance
if I think is makes the code more robust. If this is not true for you, YMMV.

### Global Error Codes
Some OSs use a 'global' errno for error conditions, where each function returns whether
it succeeded or failed, an on failure the error code is consulted for more information.
This is the strategy used in Linux and VxWorks (and probably other operating
systems). This has some advantages, and is probably best used for operating
systems which have many errors, and want to allow error codes to be extended by
users.


An [example](https://www-numi.fnal.gov/offline_software/srt_public_context/WebDocs/Errors/unix_system_errors.html)
shows that these are done with #define, which is slightly problematic, but also pretty conventional.


There are a number of problems here- one is that an actual global
would create race conditions where the error code may be changed by another thread between being set and being used.
Instead, errno is thread local- its actually a macro in VxWorks that accesses a field of the current tasks TCB.


This is all fine, although I wouldn't want every library to add to every tasks TCB necessarily. The other problem is
that there is no error type- errors are transferred as ints. This is probably mostly historical, but there is a problem
using enums here, and in general with error handling, which is that enums are closed- you can't add elements later
in other libraries. You could have multiple enums, and ensure that they don't overlap, but there would be no single
type for errors.


This means that error handling is inconsistent in some APIs. VxWorks uses a STATUS typedef (or #define I can't remember)
which is just an int, as its error type, which allows other values that are not error codes.
This int is sometimes used to return a number of bytes or other information, and only an
error code if it is negative. This leads to occasional bugs where if you check for the success value the check fails,
as the return value on success is not a single success value but some other information, and you have to remember
to always check for the error value instead.

One other inconsistency is that some functions actually return the errno value instead of -1, likely because
the errno could be overwritten by cleanup code and needs to be preserved some other way. This can be very confusing,
as almost all functions are consistent in error handling, but occasionally one will not, and you had better catch
this in the documention for each function you use.


The last problem is that with a single error code, its common to re-use error codes widely. This means that many
functions can return the same code, and it is not always clear what the code means when returned from a particular
function, unless the documentation is truely comprehensive (which it is for some systems, and is not for others).


### Pointer to Error Code
I've used the strategy where you take a pointer to an error code as well, which is then set to an error code and
checked after each function call. This strategy infects all functions in the API, requiring one more argument
for every function.


This is especially bad when you are writing persimistic code, where you check all inputs to all inputs, every time.
This is not common in C programming, and sometimes people use asserts for NULL checking, but I've been writing code
that needs to work, where correctness overtakes convenience, code size concerns, and most other concerns. In this
case you have additional NULL checking for this error code in every function, and you have to make sure you check the
result after every function. This is very tedious, but it works.


This strategy can be used with errno style 'int' error codes or enum, or any other error handling- its more
about how the errors are passed then their content.


### Struct Return Value
This is an interesting option, which I do not usually use, but is a contender for the best error handling strategy in C.
It is very similar to what you might do in Rust or Haskell, with Option/Maybe/Result/Either.
The idea is to define an error struct that looks something like:
```c
typedef struct XX_Result_t
{
    XX_RESULT_ENUM result;
    int errno;
    char *description;
} XX_Result_t;
```
This struct is just an example, as the details would be different for each use. In this case, the result struct
contains not just an enum indicating whether there was an error, and what the error was, but also the current errno,
and a text description. This additional information is still fairly compact, and can provide a lot of additional
context.


However, no strategy is without its problems. The main one is that in embedded systems, we can be paranoid about
stack use, and technically we are putting this structure on the stack each time. It is not likely to be large, but
as it grows, your stack use will grow depending on your call depth. Optimizations may help, but we sometimes ship
with -O0 to avoid any issues resulting from optimization, so you can't rely on this.


There may also be concerns about not just stack use, but CPU use as the code may be copying this structure around
all the time. The error data is no longer a single number that can be stored in a register, so the cost may
be higher in this case then the other error strategies. I won't comment on that actual cost- I don't have any
sense of it, and I'm sure its somewhat context dependent for different CPU and compiler combinations, and
depends on compiler flags.


The other problem I've had using this strategy is that if you use a single struct for a library or module, there
the fields that are only used in a subset of cases, and you have to know this in your error handling. 
In principal you could use a union based on the result code, but this is starting to put a lot of complexity into
error handling. I would be interested to see this done well, as it might be worth it in some cases, but I don't
have the examples or experience to know.

### Enum Return Value
This is the strategy I actually use. It requires an enum for each library or module, which I usually call something like
XX\_RESULT\_ENUM where the module name is used in place of XX:
```c
typedef enum XX_RESULT_ENUM {
    XX_RESULT_OKAY             = 1,
    XX_RESULT_NULL_POINTER     = 2,
    XX_RESULT_SOME_OTHER_ERROR = 3,
} XX_RESULT_ENUM;
```

This example uses positive numbers for error codes, and for success, which is not conventional. OKAY could just
have easily been 0, with negative numbers for error codes. I wrote it this way to avoid using 0, which can indicate
the intended meaning, but is also the most common unintended value and I usually avoid assigning meaning to it.
Using positive numbers makes things a little easier to decode- when you see a positive number in binary or hex,
you can tell what it is, but a negative number often requires decoding.


This enum is then used as the return value for almost every function of the library or module XX. This works
for self-contained systems where all errors are known. Sometimes it makes sense to break the errors out into
multiple enums, but for a small enough piece of code the convenience of a single enum is nice- you don't have
to worry about converting between them or remembering which enum is used in which cases.


The exception to returning the error enum is:
```c
XX_RESULT_ENUM xx_do_something(int **return_value);
```
which returns a pointer by taking a pointer-to-pointer, I will sometimes use:
```c
int *xx_do_something(void);
```
even though this hides the reason for a NULL pointer if one is returned- you don't get an error code to tell you what happened
unless you instead use:
```c
int *xx_do_something(XX_RESULT_ENUM *result);
```


The nice thing about this is that it is pretty uniform and low cost to define the enum and return it from each function.
This can be set up easily and it is clear what the intend is- check the result value to know if there is an error. There is
no other place to look, like with errno, no additional fields that may or may not have more information, no ambiguity about
whether the result code is being used for something else.

The low cost is not just for programmer convenience- its so easy to add error codes that you can do things like
produce a different code for every single error location in a library or module. Even if you also report an error message
to some kind of log, which can give the line number with __LINE__, its nice to have distinct error codes and to be able
to pin-point the exact path that caused the error. This is especially true if the error handling code essentially just short circuits
your functions, propogating the error code up the path without doing much additional cleanup or processing. In this case, you
can get a pretty good idea of the exact path your code went down in the case of an error.


This system does have some issues. One is that if your code calls another API, such as the operating system, then you either
end up including many error codes from other APIs in your own error enum, or you group errors into a single code like
XX\_RESULT\_OS\_ERROR which hides the exact error, reporting only that it was with the OS.


The other issue is that you usually know more about the problem when it occurs than in subsequent code. If there are other
values that you could include with the error code, such as the current ERRNO, or the arguments to the failed function, then
there is no easy way to pass that information around. There are several ways to deal with this- one is to return a struct
instead of an enum, where the struct has additional information. I do not usually use this strategy, although I think it might be
a good idea, and it is more like error handling in Rust or Haskell.


One other way to handle this problem is to log what is known about the problem somehow. In flight software, we use a simple logging
system, with with strings or just small arrays of numbers, to report this kind of information. This does get the data to the operator,
although it is not provided through the error handling path, so the code cannot make any decisions based on additional information.
This might even be a good thing, if the error handling code gets to complex it might do the wrong thing.

## Exceptions
Exceptions are clearly not even a candidate here, as they are not provided by C. I mention them only to say that they are a possible
error handling strategy, and I think I understand why they are valueable in some types of code. I have avoided them, even in C++,
as the non-local return stuff means that the number of error paths can be very high. I think this might seem crazy to some
people, but I think its just a tradeoff based on what values you have and what kind of code you are writing. I have opted
to use a very manual and explicit error handling system, where a great deal of your code is error checking and handling, instead
of one that reduces error checking code and leaves mostly the happy path.

## Conclusion
These are all the error handling strategies that I can think of. I would be interested to see other strategies that I haven't
explored, or other implications of the ones I listed. For example, if memory allocation where not a problem somehow, perhaps you could
return a pointer to an error structure, and if the pointer is NULL then the function succeeded (and somehow deal with out of memory
errors separately?). Seems like a bad idea, but its an idea.


Or perhaps you could use a variation on the enum idea, but prefix each error value with a module ID that 
makes each module's error enum variant distinct?


Or perhaps you could use a single struct that does not grow, with only a code indicating the cause of the error, and one code 
with optionally more information. This would capture many cases, where the only other information you have is an errno or an enum from
another library, keeping stack use small and the number of variations in error information fixed.


Or maybe some variation on the enum idea, but using 'int' error codes, to avoid the long list of #define style errors. This would at
least allow you to cast error codes to an enum once you had them?


Or maybe you could go off the deep end of complexity and return a function pointer which, when run, performs some kind of error
handling, or continues the computation? Probably don't actually do this.

