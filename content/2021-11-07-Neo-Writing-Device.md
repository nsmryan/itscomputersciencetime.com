+++
title = "The Neo Writing Device"
[taxonomies]
categories = ["neo", "programming", "C", "embedded", "m68k"]
+++
I have had this awesome little writing device called a Neo from the company
Alphasmart for a while now, and I wanted to write it up. This device has
gained a following of writers who use it as a no-distraction, instant 
power on, simple writing device. Its got a great keyboard, turns on fast,
and can do almost nothing except write (although as well will see later,
it is an embedded computer and can do much more).


There is plenty of information out there about getting these (they are no
longer made by Alphasmart, but you can still get them pretty cheap), but less
information about the technical aspects of these devices as an embedded system.

I haven't dug all that deep here, but I have found some resources, and I wanted
to keep a series of posts as a sort of log about this device so there is
another record to help others find and play with this thing.


## References

A good first reference is the Wikipedia article:
https://en.wikipedia.org/wiki/AlphaSmart

There is a Neo teardown on hackaday:
https://hackaday.com/2020/11/05/alphasmart-neo-teardown-this-is-the-way-to-write-without-distractions/

As well as a few articles about the app file format, a perl script to decode
them, and another tear down here:
https://hackaday.io/project/25732-hacking-the-alphasmart-neo

Things get a bit interesting in this article:
http://www.toughdev.com/content/2020/08/tweaking-the-alphasmart-neo-a-great-portable-word-processor-with-700-hour-battery-life/

Where the author talks about actually writing applications for a Neo.

### Neo Manager

There is an official manager program called Neo Manager, which provides
a way to connect to a Neo over USB, transfer text files, and load apps, 
and perhaps other things. However, this is a GUI and not scriptable,
and it does not work in Linux under Wine.


Luckily we can just 'pip install neotools' from https://github.com/lykahb/neotools
and do all kinds of things from the command line. I haven't dug into the code
but there is a lot of information on the commands that the NEO accepts and the
protocol that it speaks. For example, [here](https://github.com/lykahb/neotools/blob/master/neotools/message.py)
is a lot of information on messages used, [here](https://github.com/lykahb/neotools/blob/master/neotools/text_file.py)
is text encoding, [here](https://github.com/lykahb/neotools/blob/master/neotools/applet/constants.py)
are constants used in the applet format, etc.


With this, we could even upload applets on Linux, if we could build them...


### Building Applets

I was surprised to find a project that allows you to build your own applets
for the Neo: https://github.com/isotherm/betawise.

All you need is this project, and binutils and gcc compiled for the m68k-elf target.
I used a command like:
```bash
./configure --prefix=/opt/m68k --target=m68k-elf
```
and got the most recent binutils and gcc to compile just fine.

At first I expected to need a target like m68K-coff or something, which is no longer supported.
However, looking deeper into the betawise toolchain, the makefile uses
```
objcopy -O binary
```
to create a flat binary file. I didn't know about this option- its quite neat in that it
essentially erases the executable file format and places the entry point at the first address
as far as I can tell.

This means that gcc can produce an elf file, but we can pull the compiled code out and get
it on the Neo! There are a number of gcc options which may be required here- look at
Makefile.common for details.


I think a separate post might be useful to discuss the betawise setup as I learn it.

### Misc

How to Boot Into Small ROM Mode
https://support.renaissance.com/techkb/techkb/5985396e.asp

Mac Neo Manager Replacement
https://github.com/tSoniq/alphasync

68K Resources
http://www.easy68k.com/paulrsm/index.html

Easy 68K Assembler Page
http://www.easy68k.com/

Article on NEO and Custom Apps
http://www.toughdev.com/content/2020/08/tweaking-the-alphasmart-neo-a-great-portable-word-processor-with-700-hour-battery-life/

SmartApplet Format, and perl script to decode
https://hackaday.io/project/25732-hacking-the-alphasmart-neo/log/62519-smartapplet-format

Python Neotools
https://github.com/lykahb/neotools
Install With:
pip3 install neotools

Neo Dev Kit for Custom Apps
https://github.com/isotherm/betawise

MSYS2 GCC M68K
https://sourceforge.net/projects/mingw-gcc-68k-elf/
I did not end up using this, but its interesting anyway.

Instead I follow https://darkdust.net/writings/megadrive/crosscompiler
for the most part, although I just got the most recent
versions of everything, built with m68k-elf instead of m68k-coff,
and I have to explicitly provide the path to the C standard library
headers for some reason -I/usr/include.

## Conclusion

Hopefully this gives a little bit of an introduction to these devices.
I think most people prefer to engauge with them as writing devices,
but I would like to try out a few more technical things. I may
never dig as deep as I might have when I had more free time, buts its
an interesting diversion in the embedded systems world. Perhaps
someone else can get started and make some cool applets, or help
out betawise, which has a lot of interesting work to be done.

