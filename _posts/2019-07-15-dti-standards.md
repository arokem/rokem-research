---
layout: post
title:  "Should the default be the standard, or what people usually use?"
date:   2019-07-08
---

A couple of weeks ago, we got a [bug report on
DIPY](https://github.com/nipy/dipy/issues/1890) pointing out that the default
behavior of the command-line interface that fits the DTI model is to return
the 6 independent parameters of DTI on the 4th dimension. And that the order is
also not that required by the Nifti standard. As pointed out by Emmanuel in his
report, this is what the Nifti standard says:

```
/*! To store an NxN symmetric matrix at each voxel:
       - dataset must have a 5th dimension
       - intent_code must be NIFTI_INTENT_SYMMATRIX
       - dim[5] must be N*(N+1)/2
       - intent_p1 must be N (in float format)
       - the matrix values A[i][[j] are stored in row-order:
         - A[0][0]
         - A[1][0] A[1][1]
         - A[2][0] A[2][1] A[2][2]
         - etc.: row-by-row                           */

#define NIFTI_INTENT_SYMMATRIX 1005
```

This is because the 4th dimension of a Nifti file is reserved for time-series.
For example, when we created Nifti files for analysis of MRS data when I was
working on MRS at the Stanford CNI, we created files with [time on the 4th
dimension](http://cni.github.io/MRS/doc/_build/html/data.html).

So why do we use this wonky format? As it turns out, we wanted to create files
that would interoperate well with other software, and other software (FSL and
fibernavigator, I believe) flaunted the standard and rely on tensors being
on the 4th dimension and ordered in a different way.

What is the right forward? My own opinion is that we should do the
standards-compliant thing per default and we should allow our users to do the
popular-but-standards-flaunting thing too. For now, I have created a
[PR](https://github.com/nipy/dipy/pull/1892) that retains the Nifti-compliant
order and shape per default and adds a flag, `fsl_tensor`, which would let
users easily create files that would work with FSL/fibernavigator.

The main concern raised so far is back compatibility, but I believe this (as we
approach a DIPY 1.0 release -- hopefully by the end of this month!) is the time
to break back compatibility in favor of a more standards-compliant approach.
We'll see how this shakes out in discussion.