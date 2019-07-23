---
layout: post
title:  "Streamline deformations are hard"
date:   2019-07-08
categories: python, software, dipy, pyafq, tractography
---

Not much research last week, due to vacation and grant writing. One thing to
mention is that I have been thinking about streamline deformations. Partially
because of a very specific need, and because of a more general concern.

The specific need has to do with generating examples of bundle recognition as
part of the development of pyAFQ. The main challenge with these examples was how
long it was taking to run them and particularly how long of a time we were
spending on the tractography phase of these examples. John Kruper, who is
working with us on pyAFQ as a summer student came up with the the great idea of
seeding tractography much more conservatively. The idea was that for procedures
that use waypoint ROIs (the classical AFQ approach), we would use the ROIs
themselves as seed masks. In this case, the ROI is transformed into the
subject's DWI space using a SyN registration of the subject's B0 measurments
with the MNI template. For procedures that use an atlas (e.g., Recobundles), we
would have to transform the streamlines from the atlas into the subject space.
That's where streamlin deformations come in.

The more general concern has to do with providing guidance to DIPY users about
the use of streamline deformations in any application. Fortunately, Jon Legaretta and Derek Pisner have
both pitched into a nice [example](https://github.com/nipy/dipy/pull/1900) of this, but the discussion
surrounding this pull request clarifies just how complicated this is.

In particular, for my own use of streamline deformations, I ended up giving up
on the DIPY-provided function, because it required keeping track of 4 (!) affine
transformations, and I just couldn't figure out which ones to use. The result is
[a bit of a kludge](https://github.com/yeatmanlab/pyAFQ/pull/134), but it seems
to do what I want it to do.

But it's very good to have a broader discussion of this with others, because
this stuff is hard (for me).