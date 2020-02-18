---
layout: post
title:  "Paper about ML for glaucoma"
date:   2020-02-18
---

We're almost almost ready to submit a paper about an automated algorithm for
detection of glaucoma based on data from the UK Biobank. This paper is the
result of a lot of work by Parmita Mehta, who is a PhD student in Computer
Science and a long-time collaborator, and a continuation of a long series of
collaborations with Aaron Lee. I will not say here too much about the results,
except to say that giving a talk about these results last week at a local
seminar (slides [here](https://arokem.github.io/2020-02-12-UWIN/#/)) made me
think about the value of such work. There is the (obvious?) value of
demonstrating the potential utility of such algorithms. And it's (vaguely?)
possible that some variant of this algorithm will find its way into clinical
application one day. But for now, I think that one important and potentially
quite fruitful outcome of this kind of work is in considering the interplay
between "brute force" machine learning, that aims to find the most accurate
representation of the data for predictive accuracy, and an interpretive
methodology that tries to pick apart the results of an accurate algorithm, to
derive some insights. Here, the interpretive methodology takes three different
forms: the first is pixel-by-pixel allocation of credit in deep learning
algorithms. The spatial maps provided by such an analysis can be quite
compelling. Another approach uses sub-sampling of the data, to ask what
information is provided by different parts of the data. This kind of approach is
then also further formalized in using SHAP values. One thing that became clear
in giving a talk about this work is that it would be worth coming up with a
simple and intuitive explanation of how SHAPs work. But even when these values
and maps are derived there is still often a challenge to synthesize what it is
that the algorithm is telling us. So this interplay is further complemented by
*a lot* of domain knowledge. In this case, the knowledge is derived from close
collaboration with ophthalmologists (primarily Aaron and also Christine
Petersen, who have been working with us closely on the manuscript) and from
reading the literature. Here, a long line of literature on the effects of
glaucoma on different parts of the retina (whoa, eyes are complicated...).

One cool potential conclusion of the work, reiterating previous results that
we've found in at least one more
[case](https://www.nature.com/articles/s41598-019-42042-y) is that deep learning
algorithms can be sensitive to information that is "hidden in plain sight" in
features of an image that are very subtle and would be hard to extract in a
top-down manner, just based on what we think that an image represents. This
allows the algorithm to point to parts of the retina that would not have been
considered useful for a diagnosis, and in which you might think no information
should be present based on the standard analysis of the images. This is a rather
interesting and important conclusion, as data-driven approaches to analysis of
images becomes much more central.