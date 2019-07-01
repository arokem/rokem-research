---
layout: post
title:  "Thinking about regression"
date:   2019-07-01 09:22:47 -0700
categories: regression
---

Last week, [Kendrick Kay](http://cvnlab.net/home.shtml) was on a visit in
Seattle. Kendrick has been collecting a very large dataset of fMRI data (more
about that maybe some other day). In this context, he has been thinking about
ridge regression. We spent a couple of hours next to a white board working
through his thinking. As always with KK, I learned a ton, and it inspired me to
go back and read some of the literature on ridge regression. One of the things
that strikes me the most is the relationship between PCA and RR and how other
decompositions might correspond to other regression methods.

[This](http://www.uwe-mortensen.de/FrankFriedmanStatViewChemometRegressionTools1993.pdf)
paper lays things out nicely and is a good start for thinking about something
useful to do.

Meanwhile, Nitime 0.8 is out. In September, this project will celebrate a decade
of development. This release dropped Python 2 support. But it turns out that
you can't just drop support for something, [without consideration for the
downstream projects](https://github.com/nipy/nitime/issues/174). So Nitime 0.8.1
is also out.
