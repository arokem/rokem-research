---
layout: post
title:  "Tractography with dask"
date:   2019-10-07
---

The [AFQ collaboration](https://nrdg.github.io/afq-docs/) is focused on the
analysis of data from the Human Connectome Project. Because this data is
already stored in an openly available AWS S3 bucket, through the AWS open
datasets program, it makes sense to analyze the data in cloud-based systems.

For this purpose (and with a lot of help from Scott Henderson) I set up a pangeo
deployment on AWS that is accessible to members of the NRDG and collaborators.
This deployment allows a user to access a scalable Dask cluster and to
submit work into this cluster through the Dask API.

My first few experiments with the data have focused on tractography. The
processing pipeline resembles the one that I previously described [here](https://arokem.github.io/rokem-research/2019/09/09/hcp_aws_recobundles.html): Download some data from S3, create a white-matter
mask based on the provided Freesurfer segmentation, compute FA based on the
tensor model and fODFs based on the CSD model, track from every voxel in the
white matter until you reach a voxel with FA < 0.1. Upload the resulting
tracks to AWS. When run with Cloudknot, this pipeline takes on the order of 2 -
2.5 hours to run, but in my first experiments accelerating this on a Dask I was
able to accelerate this to just underr 8 minutes, for one seed per white matter
voxel or about 25 minutes for 8 seeds per voxel. In particular, parallel
computationo of the CSD model is a big win down from about 45 minutes done
serially in Cloudknot to just about 2 minutes with Dask. The notebook with
these experiments is [here](https://github.com/nrdg/hcp-pangeo-experiments/blob/master/dask_tractography.ipynb).

Because tractography is parallelized, and to avoid pulling the results back onto
the user node, tractography results end up in multiple `.trk` files in AWS.
This means that looking at the results as a whole will require some additional
computing for aggregation. On the other hand, this also means that the files
are small and can be ingested in parallel in the next step of computation. For
example in running a parallelized LiFE moodel on them, or in segmenting them
into bundles. Whih is probably the ext thing to do here.