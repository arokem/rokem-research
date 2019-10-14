---
layout: post
title:  "More ridge regression"
date:   2019-07-01 09:22:47 -0700
---

This week, I am finally going to be able to get back to work that started
[a few weeks ago](https://arokem.github.io/rokem-research/regression/2019/07/01/regression.html)
following a conversation I had when Kendrick visited. The main issue we are
trying to tackle is the setting of regularization parameters when conducting a
regularized regression. It turns out that at least in Ridge Regression (but
maybe also in other methods? Lasso? Elastic Net?), you can treat this
mathematically and derive something close to a closed-form solution that will
tell you the effect of a particular value on the degree of regularization that
occurs in practice. Thanks to the nice simulation methods available as part of
Scikit Learn, it is relatively easy to do some simulations that could
demonstrate this and test it in various regimes (e.g., correlated vs.
uncorrelated regressors). I am excited about this project, because it really
requires extending myself into terrain that I am not super confident in, and
would like to gain some more confidence in. There's [another statistics
paper](https://github.com/arokem/rrmse) that I still need to write, once this
one is done.