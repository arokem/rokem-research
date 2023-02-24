---
layout: post
title:  "A typology of datatypes?"
date:   2023-02-24
---

We are writing up a project that explored the potential benefits of using
neural network algorithms to model relationships between white matter tract
profiles and phenotypes. One of the main questions we are asking in this work
is whether NNs would be able to capitalize on the structure that exists in the
data and discover non-linear relationships between white matter tissue
properties and chronological age of the participants. It turned out that the
benefits in model accuracy where rather small (although we also know of [use cases](https://www.biorxiv.org/content/10.1101/2023.01.17.524459v1) where the
benefits are much more dramatic).

When presenting this work at the eScience Institute core staff meeting a few
months ago, Anissa Tanweer raised the interesting question of whether we could
reason about the data in advance of the rather extensive empirical evaluation
that we did to tell us whether this data would benefit from the additional
flexibility and power of an NN algorithm. My current hunch is that this is not
possible, because it's hard to discover a specific non-linear relationship in
high dimensional data without the non-linear model. However, I think that there
could be a way to reason about data that would bring us closer to this
interesting silver bullet. This could have to do with the fact that different
types of data have different characteristics. In the paper that we are writing,
we touch on the fact that tract profiles are derived from images, but they are
no longer images. In fact, when they are naively observed, they are tabular
data. But we also know that they have group structure, and that they contain
spatially contiguous data in neighboring nodes within a tract, but not across
tracts. In addition, there could be other kinds of structure. For example, some
tissue properties that we calculate could be correlated because of physical
relationships between them (for example, diffusion FA and estimates of axonal
water fraction are related to each other). Another example is that there are
known correlations between the same tract in the two hemispheres (a fact beautifully exploited by [Lerma et al.](https://stanford.edu/~wandell/data/papers/2019-Replication-Generalization-Neuroimage.pdf)).

One way to analyze and discover this structure may be through unsupervised
learning approaches (linear and non-linear). If this analysis reveals the kind
of structure that would benefit from the strengths of the NN, then it might be
worth pursuing.

This also relates to a typology of different kinds of data that we might
encounter with more or less of this kind of structure. For example, things that
are more "image-like" vs. things that are more "table-like". It also relates to
findings about the relative disadvantage of CNNs in analysis of large tabular
data, where [other kinds of non-linear models may be better suited](https://arxiv.org/abs/2207.08815).