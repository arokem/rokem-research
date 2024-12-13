---
layout: post
title:  "Debugging workflow for R development"
date:   2024-12-12
---

Over the time that I have developed software with Python, I have grown to like
a rather specific workflow for discovering and addressing bugs. I like to think
of this workflow as fairly efficient. I might start with a few simple tests of
the code that I am writing, to make sure that I am on the right path, and that
the code runs through, producing reasonable outputs for some set of simple
outputs. I use [`pytest`](https://docs.pytest.org/en/stable/) as a test
harness, and so a typical development cycle might look like:

- Start to develop some new code.
- Pretty early on write some tests for this code (sometimes, but frankly rather rarely I might even start with the tests, aka test-driven development or TDD).
- Iterate:
  - Run the tests (usually that will fail first time through)
  - Fix up the code
  - Run the tests; stop when they pass.
- Go back to the code and hone the ideas (yes, I often don't really know what I need to do until I start doing it...)
- Iterate:
  - Run the tests.
  - Fix up the code and/or the tests (at this point, I might discover the tests that I originally wrote are not the right ones).
  - Run the tests; stop when they pass.
- Etc.

This is nice. In the Python/pytest setting. The fixing up part often requires
knowledge of the state of the program at various points. This is because at the
point where something needs fixing, I am usually facing some kind of bug in my
code, and without the ability to explore the state of the program (i.e., the values of variables within the program up and down the stack), it is sometimes
hard to know what's wrong. Pytest enables this kind of examination by using the
`--pdb` flag. This means that when tests are run with (using a [recent example](https://github.com/tractometry/AFQ-Insight/pull/13)):

```
pytest afqinsight/tests/test_parametric.py --pdb
```

When the program hits an error, it will drop me into good old
[pdb, which is the Python debugging environment](https://docs.python.org/3/library/pdb.html#debugger-commands).
If I am getting results that don't make sense, I will often need to introduce
"break points" into the program. That is, I will want to examine the program
failing at a point earlier from the point at which the error is raised. This is
because, for example, by the time an assertion error is raised in my tests, the
state of the program that produced the error is no longer inspectable. One
sneaky way to do this is to insert a 1/0 into the code at the point at which
you want to examine the state of the program. This will raise a
ZeroDivisionError, at which point the program will drop me into pdb, and I can
get to work (I believe I may have picked this one up from Matthew Brett, when I
was wet behind the ears.) I have been doing this for so long that writing the
"appropriate" code for inserting a breakpoint (`import pdb; pdb.set_trace()`)
seems like that many too many characters. By the way, this approach works just
as well if I am not doing development on the command line, but I am instead
working inside of a Jupyter notebook and I hit some results that don't make
sense. This is thanks to the Jupyter `debug` magic command, which also drops me
into the debugger at the point at which an error was raised. This is all good and
simple.

However, I more recently started developing software in R ([here](https://github.com/tractometry/tractable)), and I have been hankering for a similar
workflow. Much of what I described above does work quite well. For example, R provides an excellent test harness in the [`testthat`](https://testthat.r-lib.org/) library, so I can replicate my quasi-TDD development cycle quite nicely
(adding in frequent visits to R documentation, stack overflow and google to figure out how to do the most basic things in this language, because I am quite
clueless about it for now.)

but when it comes to debugging, testthat seems to completely resists the kind
of facilities that I have been accustomed to in my Python development. For
example, there is no way (that I could figure out) to run testthat in a way
that would drop you into a debugging session when an error is raised(either in
the test code, or in the program that is exercised by the test), which would be
akin to the `--pdb` flag . So, for example, if I am working in the zsh shell
(because, yes, I am a cliche) and execute:

```Rscript -e "devtools::test()"```[^1]

[^1]: Natch, I actually run [`make test`](https://github.com/tractometry/tractable/blob/main/Makefile) because that's too many characters to be typing into a terminal every time I want to test my code.

Errors will get recorded and reported, but as far as I can tell, there is no
input to the `test` function that will make R stop running when it hits that
error and would drop me into an interactive debugging session. If I introduce
breakpoints into the code by introducing `browser()` calls into the code,
testthat will happily report that a browser debugging session happened, but
will just keep going. So, how does one (especially one as inexperienced with R
as me) make any progress debugging their code?

## Enter Positron

So here's what I end up doing (for now?). I use the newly-launched
[Positron IDE](https://positron.posit.co/).
I would've preferred to be in VS Code (my usual text editor) for all this, but
fortunately, it's a fork of VS Code, so I am pretty close to where I want to be.
I can still use the terminal to run the tests and identify failures, repeating my
development-cycle-of-choice but in addition, I also add to the top of each of my
test files (haven't done it yet, to be completely honest, [but it might happen soon](https://github.com/tractometry/tractable/pull/12))

```
library(testthat)
```

and append to all the calls to library code the necessary `tractable::`, so
that when the test file is run independently in a Positron session, the code
can be run and all the functions that are defined within the tests can properly
be found by R. Thus, I open up the test files and ask Positron to run them for
me, and when I do that, I can now hit the calls to `browser` and Positron will
conveniently drop me into a browser session, where I can haplessly bumble
through the variable defined in my code and try to make sense of things. For
now, that seems to solve the main problem for me, and I can get on causing some
real damage. I will also add that the Makefile I have for this library also has
a `make reinstall` rule, which runs:

```
R CMD REMOVE tractable
R CMD INSTALL .
```

Because I can't figure out how to install an R package in "editable" mode (i.e.,
the equivalent to the Python `pip install -e` flag), so whenever I make any
changes to the code, I can run:

```
make reinstall
make test
```

to see what I've broken this time.