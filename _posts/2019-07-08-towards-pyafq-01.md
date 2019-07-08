---
layout: post
title:  "Towards a first release of pyAFQ"
date:   2019-07-08
categories: python, afq, software, oss, pulse2percept, xarray
---

A Python version of AFQ is something that I have been working on for almost as
long as I have been at the University of Washington. And yet, we have yet
to release even a first version of the software the we have been developing
[here](https://github.com/yeatmanlab/pyAFQ). One of the reasons for this is
the fact that neither of I nor my collaborators have not really *needed* the
software to do the things that we wanted to do over this time. PyAFQ remained
more aspiration than actual intention.

But now, with several projects planned for this fall that will require a robust
and scalable tractometry pipeline, this summer seems to be a good time to finish
this up and start using it. In a fortunate turn of events, John Kruper, who is
an undergraduate in the computer science department here at UW, and a trainee
the computational neuroscience training program, joined our group and has been
plugging away at the software over the last couple of weeks. He has already
found ways to improve it, which is great. But for this to be truly effective in
the long run, we need to start figuring out what the next few weeks should be
devoted to.

In other software developments, our software for modeling retinal prosthetics is
undergoing a massive overhaul. I spent a couple of hours early on Wedensday
morning reviewing the code that Michael Beyeler has redesigned for the spatial
aspect of the model. In the next couple of weeks, this work will shift to focus
on the temporal aspect. This will be a first opportunity to integrate XArray
into one of my neuroscience projects, which is exciting.