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

My first few experiments with the data have focused on tractography. The
processing pipeline resembles the one that I previously