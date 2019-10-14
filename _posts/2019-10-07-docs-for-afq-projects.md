---
layout: post
title:  "Documenting the AFQ projects"
date:   2019-10-07
---

Last Friday, we had the first team telecon for our newly-funded AFQ project. One
of the things we discussed is centralizing documentation across several
different repositories. Both as a place for us to establish and work on ideas
that cut across all of our projects, but also as a first stop for potential
future users. We want this to be: 0) version-controlled, 1) automatically
generated, 2) simple to use: preferably using markdown as a format to write in,
so that it's human-readable and -writeable, and 3) aesthetically pleasing.

To fulfill these requirements, I set up a
[GitHub repo](https://github.com/nrdg/afq-docs) for this. In doing so, I made the
following choices: 1) No to GitHub wikis (or wikis in general). While these are
relatively easy to edit and navigate, they are not usually version-controlled. I
would also like to use the pull request protocol for adding material, and wikis
do not generally support that. 2) Use [mkdocs](https://www.mkdocs.org/). This is
the first time I use it myself, but I've seen it in use in other projects, and
it seems easy to use and aesthetically appealing. 3) Set up automated builds
using [GitHub Actions](https://github.com/features/actions). This is the final
piece in the machinery. Though I realize that buying into Actions can potentially
backfire, when it kills the very valuable ecosystem of third-party services that
has grown around GitHub, I was interested to see how difficult it would be
to set up and also get a sense for what can be done with GA. So far, it seems
easy to use, and also rather fast. Though I could probably have set this up
with Travis or CircleCI, we will stick with this for now.

And now, to add some content...