---
layout: post
title:  "Brainhack Global: Neuropixels"
date:   2019-10-21
---

Last week was the annual [global brainhack hackathon](http://www.brainhack.org/global2019/),
taking place in [more than 30 different locations world-wide](http://www.brainhack.org/global2019/locations/)
simultaneously over the course of a few days. In organizing the Seattle chapter for this event, as part of the activities of
the [eScience Neuroinformatics Special Interest Group](https://uwescience.github.io/neuroinformatics/),
I decided to settle on a central theme that we would all explore together. It seemed like a great opportunity to
explore a new dataset that was just recently released by the Allen Institute for
Brain Science (AIBS):
[visual coding neuropixels](https://portal.brain-map.org/explore/circuits/visual-coding-neuropixels).

This dataset includes a large amount of data recorded in the brains of awake
mice while they were viewing a range of visual stimuli. Extending
[previous work by the folks at the AIBS](https://observatory.brain-map.org/visualcoding/overview), in which
recordings were done with optical imaging techniques, the new dataset contains
electrophysiological recordings of single cell activity. The recordings were
conducted using six neuropixels probes in each experiment, targeting a number of
cortical visual areas, as well as sub-cortical areas, primarily in hippocampus
and thalamus. Because of the high throughput of the neuropixels probes, each
experiment has simultaneous recordings from hundreds of single units, with the
total of approximately 60 experiments totalling more than 100k neurons in all. In
most cases, simultaneous recordings from well-isolated units in multiple visual
areas are available and in some cases, recordings from thalamus and multiple
cortical regions that target neurons with similar spatial receptive fields. In
some cases, additional molecular characterization of the recorded neurons is
also available. The industrial-scale standardization of the experiments, that is
typical of the AIBS approach, means that the data is produced through a uniform
set of procedures and also means that the data and metadata are impeccably
documented. Taken together, this provides a very valuable resource to the
community. The range of different potential questions to ask with these data is
very broad, including basic questions about neural coding of vision in mouse
cortex and extending to questions about information transmission between
thalamus and cortex and between different parts of cortex in these animals.
Given the molecular characterization of some of these cells, questions about
biophysical mechanisms of neural coding are also accessible.

The hackathon drew a lot of potential interest from participants from many
different departments on campus, including graduate students, postdocs and
research staff from computer science and data science, biology, psychology,
bioengineering and several departments in the med school. More than 50
participants signed up and more than 20 participants eventually took part in the
event (why do so many people sign up and not show up? Perhaps a topic for
another day). We were fortunate to have a few AIBS staff join us for much of the
hackathon: Josh Siegle, who has been involved in collecting the dataset, gave a
great introduction to the dataset and created a great cheat-sheet that provides
both an overview of the data collection procedures and the (impressive) yield
from these experiment, as well as an overview of the software API provided
through the Allen SDK; Xiaoxuan Jia, who has been analyzing these data; and Nile
Graddis, who has worked on the Allen SDK access these data. Having inside
knowledge about the data was invaluable and Josh, Xiaoxuan and Nile were able to
answer participant questions at multiple different levels, ranging from access
to the data and metadata, and through the kinds of analysis approaches that have
already been taken with this data. In addition, we were fortunate to be joined
for the second day of the hacakthon by Nick Steinmetz, a member of faculty here
at UW, and one of the developers of the neuropixels probe, whose [recent work with this technique](https://www.biorxiv.org/content/10.1101/474437v1)
impressively demonstrates the kinds of questions that can be asked with this
kind of dataset. Nick gave a presentation about the technique used to sort the
wave forms captured by the probes into different functional units (spike
sorting), and the metrics used for quality control of this processing.

One of the main challenges of working with this data is the sheer volume of data
files that hold the data. Even just the sorted spikes from a single experimental
session can exceed 2 GB of data. Moving this data from the AIBS servers to
participants' laptops over a wireless connection would have taken far too long
and would have made for a rather unpleasant hackathon experience. To mitigate
this challenge, and to provide hackathon participants with a uniform
computational environment, I set up a JupyterHub for them to log into. The data
is duplicated on AWS, through their [Open Data Program](https://registry.opendata.aws/allen-brain-observatory/), so I
provisioned machines on AWS EC2 for participants to log into through the
JupyterHub interface. Initially, on the first morning of the hackathon, data
transfer was still quite slow. This was because even though the data is
available through S3, data access through the Allen SDKis wired to point to the
AIBS servers (that are not on AWS). A quick fix, using
[s3fs](https://s3fs.readthedocs.io/en/latest/), instead downloaded the data from
S3. This already caused a significant speedup. Eventually, as our own hack
project, Nile and I (mostly Nile, really), created an interface to the Allen SDK
that uses the S3 storage, rather than the AIBS servers (see
[here](https://github.com/nrdg/brainhack2019-notebooks) for the code). Nile firmly believes that mounting S3 as a FUSE filesystem on the instances would have made it go even faster, and I believe him.

Given the infrastructure and the data, the kinds of things that I saw
participants do were extending the data-munging presented in the intro notebook,
calculating power spectra and their relationship to the presented stimuli and
even calculating correlations across different areas and how these correlations
change with stimulus types. For me, this was also an opportunity to have some interesting conversations both
about the data and potential analysis (information theory! Image-computable
models!), but also about more data-science theoretical questions, such as data
standards and useful software data representations for this kind of data (Nick
pointed out the [Open Neurophysiology Environment data standard](https://ibllib.readthedocs.io/en/latest/04_reference.html),
which is a very compelling example) and to think about the kinds of cyber-infrastructure that we need to work with this data.

One of the take-home messages for me was how impactful it would be to
design a [Pangeo](http://pangeo.io/)-like structure around this dataset. In our
own research, I have already set up an instance of a dask-enabled Jupyterhub
around the Human Connectome Project dataset. Initial work with this cluster
suggests that we can not only dramatically speed up computations that otherwise
take a very long time (see [here](https://arokem.github.io/rokem-research/2019/10/07/tractography-dask.html)),
but that some aspects of collaborative analysis work is simplified through
central management (e.g., software versions and installation). The vision is that instead of duplicating this data multiple times in different scientists' lab computers, they would all come to one place to play around with the data, interact with it, and eventually analyze it for new findings. There are
of course still some challenges to acheiving this kind of vision. One of the main barriers remains
the data in its current format. For the Pangeo approach to be useful in neuropixels data and similar datasets, we would need to
support cloud-optimized file formats. As far as I understand, currently, to
read any data from an NWB file, we need to download the whole (>2 GB) thing, but
a cloud-optimized format would presumably allow us to read just a bit of metadata
upfront and then read smaller bits of data as needed. This would really capitalize on the ability of cloud-based distributed clusters to read data in parallel from cloud storage and enable rapid interactive computation at the scale of the dataset. There are already [plans](https://github.com/NeurodataWithoutBorders/pynwb/issues/230) and even [some](https://neurodatawithoutborders.github.io/nwb_hackathons/HCK06_2019_Janelia/projects/ZARR/), [initial](https://neurodatawithoutborders.github.io/nwb_hackathons/HCK06_2019_Janelia/projects/Exdir/) attempts to do that, so I am optimistic.