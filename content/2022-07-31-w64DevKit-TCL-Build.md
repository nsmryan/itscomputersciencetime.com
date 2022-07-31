+++
title = "w64DevKit TCL Build"
[taxonomies]
categories = ["tcl", "w64devkit", "Programming"]
+++

This post is just a quick note on building TCL on Windows using [w64devkit](https://github.com/skeeto/w64devkit).

The motivation is to be able to build a working development environment for Windows,
complete with GUI capabilities (TK), in a self contained manner with consistent builds
and low likelyhood of breaking.


I have not yet used this build extensively, but it is a native Windows build which should work
just like any another TCL such as [MagicSplat's](https://www.magicsplat.com/tcl-installer/index.html). I haven't
tried building with stubs yet, so no guarentees there.


The basic concept comes from the TCL wiki page on (building for MSYS2](https://wiki.tcl-lang.org/page/Building+Tcl+and+Tk+for+Windows+with+MSYS2),
but instead using the following commands to build TCL and TK with TclLib into 'c:/tcltk' :

```
# TCL
wget -O tcl-release.tar.gz https://github.com/tcltk/tcl/archive/release.tar.gz
tar xvf tcl-release.tar.gz
cd tcl-release/win/
./configure --prefix=c:/tcltk/ --enable-threads --enable-64bit CC=gcc RANLIB=ranlib RC=windres AR=ar
make
make install
 
# TK
cd ../..
wget -O tk-release.tar.gz https://github.com/tcltk/tk/archive/release.tar.gz
tar xvf tk-release.tar.gz
cd tk-release/win/
./configure --prefix=/c/tcltk/ --with-tcl=../../tcl-release/win/ CC=gcc RANLIB=ranlib RC=windres AR=ar
make
make install

cd ../..
wget -O tcllib-release.tar.gz https://github.com/tcltk/tcllib/archive/release.tar.gz
tar xvf tcllib-release.tar.gz
cd tcllib-release/
./configure --prefix=/c/tcltk/
make install
```


I really like the ability to build TCL and start scripting, automating, and building
interfaces for myself where I feel in control of the language and environment. With
w64devkit, the build is much more reproducable then with MSYS2, even with Mingw64, which
frequently fails to build and requires reinstalls, or the package manager is broken
that day, etc. Don't get me wrong- I use Mingw64 basically all day every day when
I work in Windows, but I want my TCL build to be more reproducable then my experience
building with MSYS2.


The biggest downside to using this environment is that I often find that I want a bit
more capability then just Tk and TclLib, so I have to get a few extensions. I haven't tried
building those extensions from source with w64devkit, but if that works then this will be a 
basically ideal setup for me. Previously I would just downlink MagicSplat and copy their compiled
packages into my TCL build so I wouldn't have to worry about building everything from source, but
this is less then ideal in a number of ways.


Anyway, I hope this is helpful to someone. At the very least I wanted to make this note to myself for future builds.
