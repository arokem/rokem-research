---
layout: post
title:  "GitHub Actions: first impressions"
date:   2019-12-2
---

In the last couple of weeks, I have been doing some more small experiments with
GitHub Actions. We
[previously](https://arokem.github.io/rokem-research/2019/10/07/docs-for-afq-projects.html)
used GA to build [the documentation for the AFQ projects](http://autofq.org/)
with GA, but have since moved that to netlify -- as story for a different day.
More recently, we decided to move the continuous integration of the pyAFQ
code-base to GA as well. One of the reasons for that was that we were running
out of memory on Travis CI on one of the pending PRs that we had, and we wanted
to see whether we can get past this bottleneck with GA. Another reason is that
GA supports several operating systems (linux, Mac and Windows), so we can now
test on systems other than linux. It seems to also provide avenues for
automating documentation builds, creation of wheels for different platforms and
automated deploying of releases, all things that I am
[currently experimenting with](https://github.com/yeatmanlab/pyAFQ/pulls). So far, the experience is
quite good. The eco-system of actions provides an interesting lego-like feel to
using GA. The integration into GitHub itself also makes some of UI quirks of
Travis a bit less painful. Let's see where this goes.

