+++
title = "TCL Count Words"
[taxonomies]
categories = ["tcl", "programming"]
+++
I came across this article the other day on Hacker News:
https://benhoyt.com/writings/count-words/#performance-results-and-learnings


The article describes a simple problem which was implememnted in multiple languages as a
basic comparision.


I took a look at the TCL solution, and found that there was a simple optimization that results in
about a 2x performance improvement. TCL is much faster if code is placed in a proc and then run- this
is described in the [TCL Wiki Performance page](https://wiki.tcl-lang.org/page/Tcl+Performance) under
"Put Everything in a proc". This would put TCL between Ruby and AWK, instead of between Common Lisp and Haskell.


I put the result as simple.tcl in [my fork](https://github.com/nsmryan/countwords). I also wrote
a simple 'optimzed.tcl' which just uses dicts instead of arrays, which happen to be slightly
faster for this problem.


I tried a few other concepts such as different amounts of buffers, with no results. I would
guess that default buffering by TCL is good enough and no further tweaking was going to help.


For fun, I also write this version using an in-memory sql database. The result was many times slower. This
is clearly not a good idea, but I had to wonder whether native data structures would win or a database.
It turns out, at least for such a simple query, the database is a huge loss.

My SQL is not good, so perhaps someone could do better. The one thing I do know how to do is to wrap things
in a transaction, which was a big performance boost, but not enough to make this a reasonable solution.

```tcl
#!/usr/bin/env tclsh

package require sqlite3

proc main {} {
    fconfigure stdin -buffering line

    sqlite3 db ":memory:"
    db eval { create table counts(word text primary key, count int default 1) }

    db eval { begin transaction }
    while {[gets stdin data] >= 0} {
        foreach word [split [string tolower $data]] {
            db eval { INSERT INTO counts(word) VALUES($word) ON CONFLICT(word) DO UPDATE SET count=count+1; }
        }
    }

    db eval { select word,count from counts } {
        puts "$word $count"
    }
    db eval { end transaction }
}

main
```


If I ever revisit this, it would be cool to try out a Forth solution. I'm surprised to see Forth so low
on the list, and I can't help but wonder if a better solution could bring it at least near Python if not
higher.
