---
layout: post
title:  "Running Recobundles on the HCP dataset at scale"
date:   2019-09-09
categories: dMRI, DIPY, AFQ, AWS, HCP, cloud
---

As a first effort on our [newly funded grant](https://projectreporter.nih.gov/project_info_description.cfm?aid=9886761&icde=46767460&ddparam=&ddvalue=&ddsub=&cr=2&csb=default&cs=ASC&pball=),
I have been working on a pilot study to scale AFQ-style segmentation to large
datasets, using Recobundles. Particularly, I have been focusing on the Human
Connectome Project dMRI dataset. Last week, I did some parameter twiddling, to
figure out the inputs to recobundles that reliably segment out 16-bundle
segmentations. Then, I wrote a pipeline that uses Cloudknot to scale this up to
multiple subjects. After multiple rounds of error-correction, I believe that I
now have a pipeline that works, and currently running this on about 20 subjects
in parrallel (!) so that I can do some QC on the results and see that they make
sense. The notebook that does this is
[here](https://github.com/uw-biomedical-ml/bundlenet/blob/master/cloudknot_recobundles.ipynb).
Debugging this kind of pipeline has turned out to be be a rather arduous
process, because whenever I want to test a change to the code, it has to be
submitted into the AWS Batch queue. This entails creating a new stack on
CloudFormation, and often involves waiting for a long time for the pending
processing steps to complete, even when I am caching intermediate results. I
feel like I understand just a bit more what it might have felt to submit code to
a shared mainframe, back in the day.
