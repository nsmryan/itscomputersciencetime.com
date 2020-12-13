+++
title = "TCL Parser for COSMOS Telemetry"
[taxonomies]
categories = ["TCL", "Programming", "COSMOS", "Ground Software", "Ruby"]
+++
As a short side project, I recently wrote [a parser](https://github.com/nsmryan/cosmostcl)
for the [BALL COSMOS](https://cosmosrb.com/docs/v4/telemetry) telemetry format using
[TCL](https://www.tcl.tk/).


The hope was that by parsing this format outside of the COSMOS ground system, I might be able to
write tools that use the definitions but are not built into the ground system. For example,
a tool that puts telemetry into a database, a simulator that generates telemetry packets, or
a way to construct commands and script systems in TCL.


The parser is a little janky in my opinion- in retrospect I think it would be done in a simpler way.
However, what I learned while doing this project is that COSMOS definitions rely on the Ruby
interpreter and COSMOS library more then I realized. Derived telemetry items can use a small
ruby script, which is mostly just arthimatic (so it could in principal be done outside of COSMOS),
but conversions and other aspects of the format may rely on a Ruby file with a class that subclasses
a certain class defined in COSMOS.


I'm essentially abandoning the project- it was a test my parsing in TCL, and a way to look into the COSMOS
format, but ultimately I think these definitions are better off living in another system and getting
generated for COSMOS, rather then living in COSMOS, due to the tight tie into the COSMOS system
and the Ruby language.
