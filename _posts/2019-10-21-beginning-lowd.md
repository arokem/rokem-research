---
layout: post
title:  "Beginning to do dimensionality reduction of neurophysiology data"
date:   2019-10-21
---

In my work on the learning2learn collaboration, we are interested in examining
changes in multi-channel recordings across episodes of learning and the larger
establishing of learning schemata. In a (possibly criminal) simplication of the
idea (and based on revisiting
[this](http://stat.columbia.edu/~cunningham/pdf/CunninghamNN2014.pdf) Cunningham
and Yu review): one of the ways to examine this process is by looking at the
'intrinsic' dimensionality of the data and the trajectory of neural activity
through the lower-dimensional representation with behavioral changes. John
Ferre, who is a graduate student here at UW, has started computing these things
for a relatively small amount of data, on our PanNeuro deployment. So far,
we've been setting up a pipeline for spectral decomposition and for construction
of a large covariance matrix, as the first step towards a distributed PCA of
multi-channel data.