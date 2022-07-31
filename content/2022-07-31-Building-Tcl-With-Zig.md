+++
title = "Zig TCL Build"
[taxonomies]
categories = ["tcl", "zig", "Programming"]
+++

This post is just a quick note on building TCL with the Zig toolchain.

I was hoping that Zig would build TCL out of the box on Windows and Linux, making it a 
self contained way to build TCL and TCL extensions (in Zig, or in C with 'zig cc').


This has not yet worked out on Windows. I don't know why, but the build fails.

However, on Linux, the following works to build a local TCL:

```
# TCL
wget -O tcl-release.tar.gz https://github.com/tcltk/tcl/archive/release.tar.gz
tar xvf tcl-release.tar.gz
cd tcl-release/win/
./configure --prefix=c:/tcltk/ --enable-threads --enable-64bit CC="zig cc" RANLIB="zig ranlib" AR="zig ar"
make
make install
 
# TK
cd ../..
wget -O tk-release.tar.gz https://github.com/tcltk/tk/archive/release.tar.gz
tar xvf tk-release.tar.gz
cd tk-release/win/
./configure --prefix=/c/tcltk/ --with-tcl=../../tcl-release/win/  CC="zig cc" RANLIB="zig ranlib" AR="zig ar"
make
make install

cd ../..
wget -O tcllib-release.tar.gz https://github.com/tcltk/tcllib/archive/release.tar.gz
tar xvf tcllib-release.tar.gz
cd tcllib-release/
./configure --prefix=/c/tcltk/
make install
```

I will have to try to get this working on Windows again some time. I've been having some build troubles
with Zig on Windows, especially with static libraries, but it is worth trying again.
