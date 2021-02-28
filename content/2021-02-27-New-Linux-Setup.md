+++
title = "New Linux Setup"
[taxonomies]
categories = ["linux", "vim", "tmux", "programming", "dwm"]
+++
I finally installed Linux on my new laptop, and now its even better then my old one!


I have used Ubunutu for a long time, but I wanted to try something new this
time. I did some experiments trying to install Arch, I tried Puppy Linux (too
strange for me), and considered NixOS. I eventually found Manjaro, which seems
to be Arch, but more friendly, and I think it was a good choice.


The installation went fine for the most part, with a handful of complications.
Windows was not able to resize its partition on the SDD drive, so I had to
install Linux on the second drive (spinning disk). There also appears to be a
new "Secure Boot" thing in my laptop's BIOS, and a Linux partition does not
pass whatever check its doing (perhaps it is just not Data-At-Rest encrypted or
something). I did have a few issues with Linux after the install, like sound
didn't work until I did an update.


Once all this was worked out the fun part begins. My setup isn't hugely
customized, but I've been playing around with trying to make something nice for
myself.


## Suckless Tools

I have been using the dwm window manager, dwmstatus for my status bar, and the
st terminal from suckless.org.  I've enjoyed their simplicity, and the feeling
of ownership in modifying and compiling ones own environment (at least these
pieces).  I was inspired by [this
article](https://christine.website/blog/why-i-use-suckless-tools-2020-06-05) by
Christine Dodrill on their own Linux setup, although theirs sounds much nicer
and more customized then mine.


The suckless philosophy appeals to me somewhat- using simple tools that can be
understood and modified. I am going through something of a minimal phase in my
computing (and home) life, so I'm enjoying trying out a desktop setup that
re-enforces that feeling.


In general I have found that I'm not currently willing to give up on useful
features for the sake of minimalism- I want the minimalism for practical
reasons, not for its own sake.  I may change my mind over time, but so far dwm
and st have not been an impediance. In fact, the keyboard driven workflow with
dwm is very easy and fast (and I like fast tools).

### Setup
The setup was not particularly difficult besides a few git clones, make, make install.
The main new thing was adding an option to load dwm on login by making a DWM.desktop
file in /usr/share/xsessions.

### dwm
I have only a few changes to dwm to use a nicer colorscheme, to get sound
and brightness to work on my keyboard, to use monospace font, etc.
The compile, run cycle is not quite as convienent as it might be
for a dynamic language, although I think there is a way to restart
dwm when it closes to quickly iterate on configuration. I haven't gotten
this to work yet.


I use the default dwm keybindings, as they are perfectly useful and easy enough
to memorize. I like dmenu for starting programs, and the tile management.


### dwmstatus
The dwmstatus program is an interesting example of separating concerns
and of making customizable programs.


Its a short program, easy to understand and modify, and it can interact with
dwm to set the status bar (top right of the screen for me) by simply setting
a property in dwm. This is a nice composition of tools where the task of
displaying useful information to the user doesn't have to live inside of the
window manager.


I've modified dwmstatus a bit, removing a lot of the informaiton it displays.
The current result is a simplfiied version which does not include information like
processor or memory use (information that changes frequently), but does include
brightness and volume.

### st
I haven't looked into st as much as the other programs. Its mostly just worked,
and I've setting the colorscheme outside of the configuration file.


## Workflow
I tend to live in the terminal (st) for the most part, with tmux always open. I
have dwm for window management, tmux for terminal panes, sessions, and windows,
and Vim for editing, with its own splits and buffers.


### Vim
My vim setup is somewhat customized, with a medium number of plugins and keybindings.

I have a custom statusbar of my own design which follows a few personal preferences:
I like its information to be static (nothing that changes without me doing something).
This prevents the status bar from drawing my focus.
I like the status bar to be simple- I don't use the pretty ones like airline.
I like the information to be in a fixed location so it is easy for me to find.
The file name is always displayed without the path right after the buffer number, and
with the path in the middle of the bar, such that I can see the file name itself without
having to find it within the path.

There are a few other niceities I took from tutorials or other peoples configurations like
a flag for whether a file is modified, the file type, and the progress of the cursor through
the file in lines and as a percent.


A person's vim setup desires its own post, or series of posts, but just to mention some plugins,
my main ones are nerdtree, startify, gruvbox colorscheme, ctrl-p and recently vimwiki to try to
organize my notes. I also have a ToggleHex function I copied from somewhere a long time ago
which translates a file into hex using xxd, or back to text. I use this fairly often
for binary files or checking text for unexpected characters (carriage returns or tabs, or
corruption like 0s within an ascii text file).

### Tmux
My main tmux change is a few bindings to make it so that new splits open in the same
directory as the previous terminal. This is a real pain normally, but I find
some discussion on how to rebind keys to use 'pane_current_path' when new
splits are created.


One note is that I use the -2 flag when opening tmux- this seems to help some
terminals with colorschemes. I source a colorscheme for the terminal from
a bash script on startup, so my tmux looks like the gruvbox theme (the same
one that I use for vim), which is nice.

I also have some bindings to help integrate with Vim, but they don't see to work
on my current setup.


### Setup Script
I have a thing where I really dislike modifying an environment if its not repeatable-
I don't want to make a bunch of small changes to my OS, and then have to reinstall one
day and lose everyhing. To combat this I've been trying to include changes in a setup
bash script that establishs my environment. There are probably much fancier ways to do
this, but I'm keeping it simple for now.


My little setup script tries to install a few useful programs, deploys a few
configuration files, and installs my vim plugins. Its far from perfect, but
it does work and helps me track changes I make both in MSYS2 on Windows and
in Linux.

### Session Script
One little experiment I've been doing recently is trying to make my
environment more scriptable. To this end, I wrote a little TCL script
that can switch between programming projects, setting up the tmux
window and splits quickly. Scripting tmux was not trivial, but you can
send it keys with "tmux send-keys" to a particular session, list sessions
with "tmux ls", and create new sessions ("tmux new-session") and new
windows ("tmux new-window"), so its definitely doable.


The program is called se (for sessions). I would not have taken a two
letter name except that I don't intend anyone else to use this- its a personal
tool that does what I want it to do.


One note on using TCL- I tried to use bash scripts at first, but it almost
immediately became to painful for me to bear, and it was absolutely much faster
and easier to depend on TCL. I expect TCL to be available on the systems I
actually use, so this isn't a problem for me.


If this turns out to be useful I may write a post on it. I know that there
are many other tools, likely much better ones, out there, but for right now
I'm just enjoying the simple tools and the personal programs approach.


## Conclusion
So far this Manjaro Linux setup is the best I've ever had. The terminal heavy and
keyboard driven workflow suits my preferences, and I feel a bit more
ownership over a few more of the tools I use.


Overall, my Linux programming setup is far better then any Window development
environment I've ever had.  It is like having a weigh lifted from my shoulders.
This is clearly personal preference, but for programming tasks, a simple terminal
window that takes up the entire screen, fast tools, and the option for customization
far, far outweighs any advantages I've even gotten from my Windows environments.


