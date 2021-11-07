+++
title = "Digging Deeper into the Neo"
[taxonomies]
categories = ["neo", "programming", "C", "embedded", "m68k"]
+++
Last post I introduced the Neo and Neo 2 a little bit. This time lets dig in
a little bit and look at the [betawise](https://github.com/isotherm/betawise) project.

Ultimately the hope would be to build an applet, and use [neotools](https://github.com/lykahb/neotools)
to upload it and try it out.


## Getting Started

The betawise tools require a GCC m68k-elf cross compiler.
I downloaded binutils and gcc, and built using the line
```bash
./configure --prefix=/opt/m68k --target=m68k-elf
make
sudo make install
```
and got the most recent binutils and gcc to compile just fine. They are now installed
in /opt/m68k with the normal gcc toolchain executables, libc, etc.

## Betawise

I looked a bit at the betawise project to figure out how they are able to compile and load programs
for the Neo. Lets start by looking around.


### Makefile

A good place to start with new code is the build system. In this case we have a good ol' Makefile.

This Makefile is only three lines. It defines some subdirectory paths, includes Makefile.common, and
has a rule

I tried the 'make toolchain' rule, and to my surprise it started to build an m68k-elf toolchain. I am
glad that I built my own however, because the build eventually failed. Maybe we can get back to that
sometime. I hope my toolchain actually works- I haven't run an applet that I compiled yet.

### Makefile.common

The real build starts in Makefile.common. This defines our toolchain file names. There are some
flags being used here.

The assembler flag '--pcrel' is used, presumably asking to use offsets relative to the program counter?

The compiler flags 
```
CFLAGS += -mpcrel -fomit-frame-pointer -ffixed-a5 -fshort-enums
CFLAGS += -Os -I"$(TOP)/os3k" -ffunction-sections -fdata-sections
```
It looks like we omit frame pointers, perhaps for size, or perhaps related to the m68k's separate data and
address registers. The '-ffixed-a5' must somehow be related to the use of address registers, although I don't
know how. The '-fshort-enums' is likley just a way to reduce size (I hope).

The separate sections (function-sections and data-sections) are likely due to the use of the linker
flag "--gc-sections" to help remove unused sections and reduce binary size.


The linker flags here are quite interesting. First, we want a static binary, as we don't want to link
at runtime- I don't think that this is available on the Neo.

Second, we pass --gc-sections as mentioned above.

Third, we link in os3k and use os3k.lds as a linker file, which controls the layout of the resulting binary file.
Lets look into that file.

### os3k.lds

This file is in the os3k directory. It starts with a note that we are using the ELF file format so that the linker
can garbage collect section before translating the result into a flat binary file with objcopy.

The layout starts with address 0, and places the os3k header. I expect that this is the one that the
perl script decodes, which is Neo specific and describes the application and its needs. After that
there is text.BwProcessMessage, which I believe is the main loop that they provide for processing
events for applications. The betawise project provides this wrapper and some conveniences, which we
will look at later. Anyway, this BwProcessMessage is then the entry to the program, coming right
after the applet header.

We then have .text and .rodata, so we are putting in the user's code and all the read only data.
This is followed by a footer, which we will look at later when we talk about sections declared
in C files.

The rest of the file declares a symbol __os3k_rom_size, presumably so a C program can tell how large
the code is, the BSS and something called the COMMON section, and then another symbol __os3k_bss_size,
again likely to help C code tell how large things are.

I don't really get the line that seems to reset the location to '.' again just before BSS and COMMON.
I'm not well versed in linker scripts, I can just read basic ones.

### version.h

Next I looked through the os3k/version.h file. This just provides some #defines for version information, and
does not even have include guards or a "pragma once".

### syscall.c

This file, os3k/syscall.c, seems to place named sections into the elf file at fixed locations starting at
0xA000 at 4 byte offsets, clearly building a table of function pointers.

These functions seem to have to do with the built in functionality of the Neo. Clearing the screen, setting the cursor,
placing a character or string.

Some interesting ones are: SYS_A028 which says that it saves the screen, SYS_A02C which says that it restores the screen, 
SYS_A03C which puts a character in 'shadow', DrawBitmap, ProgressBar, TextBox, SYS_A140, SYS_A148, and SYS_A15C, having to do with
serial, _OS3K_CallSysInt, and some C standard library functions.


I expect somewhere we will use this to implement functions that vector through our function pointer table.

### os3k.h

Now we are getting into the C code a bit more with our header file.


We start with some standard library includes, FILE typedef to void, and standard file descriptors. We seem
to be bootstrapping ourselves a C environment.

This is followed by a cursor mode, a map of key codes, and a map of modifier keys.


The Message_e enum is interesting- it appears to be messages of some kind that can be sent to applets. The applets
seem to get a chance to initialize, respond to characters, other keys, to USB events, and changes in focus.
MSG_MOD_SYNTHETIC may be some kind of user defined message, or just something to do with modifer keys?


Next is a function that appears to be very important: ProcessMessage. This is not defined anywhere, and seems to be
intended for implementation in the user's applet as a callback to handle messages. I think an 'extern' or comment
might help make this clear.
Anyway, it looks like you get the message id, a parameter as a uint32_t, and you return status by setting a status uint32_t.


This is followed by some rasterization enum, system calls mostly about fonts and display, and then function prototypes.
Most of these function prototypes appear to be the implementation of the system calls we looked at earlier, although
BwProcessMessage is in there as well.


We then get the AppletHeader_t structure that defines the header that all applets require. It looks to be naturally well
packed, not requiring any \_\_packed\_\_ or pragma(pack) to remove padding.

As predicted, the linker script's definitions are available in C, extern declared as:
```C
extern char __os3k_rom_size;
extern char __os3k_bss_size;
```

The rest of the file is a bunch of #defines for applets and the LCD display. The most interesting
to me is the applet header begin, which declares the header with a certain section name so the linker script
can find in. This is used in applets to allow you to fill out some fields while providing a convenient way to
ensure that this section exists and is filled out. One interesting this here is the magic number COFFFEAD
to start and CAFEFEED to end.

As a final note, the LCD definitions seem to include some fixed memory location, presumably registers that control
the LCD system.


### os3k.c

Now we have the first C code. There are a number of globals for font, cursor, and LCD data, and some internal prototypes.
The Cursor_t type seems to hold the state of a text cursor, with font, location, screen dimensions, visibility, and 'pause'.
This structure seems to be built in, either in the hardware or software, at location 0x5C68.


This file contains the BwProcessMessage function we heard about earlier, and some syscall functions that I haven't looked much into.

The BwProcessMessage function receives the message id, parameter, and status pointer. It was registered in the os3k app header as the
entry point, but it does not appear to contain a loop. This suggests that applets get messages from the OS, and don't maintain their
own event loops or take over (although if you never return perhaps you could just control the whole processor- I'm sure there is no
preemption going on).

BwProcessMessage seems to handle some basic messages itself: MSG_INIT, MSG_SETFOCUS, and MSG_KILLFOCUS.

The applet does get all messages- these few are handled but not intercepted by the BwProcessMessage function.
Interestingly, there is a call to AppletSendMessage with message id 0x1000002, which is 2 more then MSG_MOD_SYNTHETIC.
Perhaps this is some kind of user-defined messages, as I speculated above.



### HelloWorld.c

As the final file to look through for this post, lets look at a minimal applet. The HelloWorld/HelloWorld.c file contains a simple
applet that seems to print "hello, world" to the screen.


This file includes the os3k.h file and declares the header structure with some user provided fields.

The ProcessMessage function only handles MSG_SETFOCUS, and it simply clears the screen with ClearScreen, hides the cursor
with SetCursorMode(CURSOR_HIDE), and prints a string with PrintStringCentered(1, "hello, world"). This seems simple enough.


Its interesting that PrintStringCentered appears to be a syscall at entry 3 in syscall.c. The prototype in os3k.h shows that the
first argument is the row, so we are printing a centered string on row 1.

## Conclusion

That was fun! I didn't get through the entire program, leaving much of os3k.c unreviewed, and only looking at the simplest applet
without digging into the BASIC interpreter or debugger, both of which are quite impressive, but I had a good time.

I am very impressed with this project- there is a lot knowledge of the processor, the NEO, and the GCC toolchain required to get all
of this working. I didn't think I was ever going to know how these applets worked, but someone else did a huge amount of work
for us all and enabled a pipeline for applet development.

I was able to get the HelloWorld applet to compile, so the only thing left is whether it can actually run. If so, there may be a whole world
of Neo programming ahead! Or at least a little prototype or two as a side project!

