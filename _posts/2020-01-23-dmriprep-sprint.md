---
layout: post
title:  "dmriprep development sprint"
date:   2020-01-23
---

Reliable, robust and efficient preprocessing of MRI data is hard. So many things
can go wrong. Building a general-purpose pipeline for preprocessing also faces
the challenge that even for just one type of data (e.g., dMRI) there are
multiple variations on the manner in which the data can be collected (for
example, are multiple gradient strengths collected in each scan, or in separate
scans? Are fieldmaps collected for susceptibility distortion correction, or b0
scans with reverse phase encode directions? Etc.).
[`fmriprep`](https://fmriprep.readthedocs.io/en/stable/) provides an excellent
template to follow as an example of a robust, general-purpose
pipeline for preprocessing of data collected in many different kinds of fMRI
experiments. And so, for a while now, we have been thinking about a `dmriprep`
that would emulate the success of `fmriprep` for the dMRI community. Initially,
this was a local effort (with Adam Richie-Halford and Anisha Keshavan at the
helm), but after presenting our initial work on this at OHBM, very quickly we
were able to get together other members of the community: Oscar Esteban (at
Stanford), who is the lead developer of `fmriprep`, was already interested in
expanding `fmriprep` to an ecosystem of
[`niprep` tools](https://github.com/nipreps) and wanted to make sure that we did it
right. Matt Cieslak (Penn), who has in the meanwhile created
[`qsiprep`](https://qsiprep.readthedocs.io/en/latest/), which does a lot of what
we might want a `dmriprep` to do, was also interested in contributing his
(extensive) knowledge and experience to a community-oriented effort. Over the
course of the last few months, several others joined the effort as well: Gari Lerma (Stanford), Derek
Pisner (UT Austin), Erin Dickie and Michael Joseph (both at CAMH). We were able
to pull in Jelle Veraart (NYU) into some of our discussions, to contribute from
his expertise on the physics of dMRI and particularly on the way to mitigate
noise and other artifacts in dMRI data processing. Jelle has thought a lot
about a process for generating community consensus around dMRI processing
(including a session at ISMRM devoted to starting this process), and we'd
like to be part of this process, so his contribution is crucial.
Ross Lawrence (JHU) has also more recently joined the effort, as part of the
contributions that Joshua Vogelstein and his team are making to open source
(Ross is part of Jovo's team).

Distributed software development is challenging. It is hard to figure ut who is
doing what, and what the overall architecture should look like. Even if we are
following a well-worn template laid out by `fmriprep`. We had started doing
bi-weekly telecons, but we needed an opportunity to get together in person and
hammer out our process and coordinate our expectations. Luckily, I had some
funding available from the
[Moore and Sloan Data Science Environments](http://msdse.org/) grant here at
UW eScience that I could use to support travel and accommodation for a three-day
code sprint. And so, on January 13th - 15th, we all congregated in Seattle
(with the exception of Jelle, who couldn't make it). The sprint gave us just
the opportunity we needed to lay the ground work for the library, in terms of
development infrastructure (testing, documentation, continuous integration,
etc.) and an excellent opportunity to have some in-depth discussions about the
things we would like `dmriprep` to do for us. At the end of all this, we could
even go so far as to write down a
[roadmap](https://nipreps.github.io/dmriprep/roadmap.html) for future developments
during this year. This lays the groundwork for the telecons that we will continue
to have on a bi-weekly basis (and that are open to anyone to join...).

For me personally, three things stand out as highlights. The first important thing
that I take from this sprint is how I might implement the philosophy of "release
early and often" more seriously in my own work in other projects. For example,
it took us several years to finally [release a 0.1 of
pyAFQ](https://arokem.github.io/rokem-research/2020/01/15/pyAFQ-01.html), but it
really shouldn't have. And if we adopt some of the approaches that are part of
the genetic make-up of `dmriprep`, with its origins in `fmriprep`, we will be
releasing more and hopefully leveraging this to make more rapid progress and
detect/fix problems with the software more rapidly.

The second thing that I was excited about is an approach that Matt developed to
correcting for head motion and potentially also for eddy currents. This approach
has its roots (at least in my mind) in a
[2012 paper](https://onlinelibrary.wiley.com/doi/full/10.1002/mrm.23186) by Amitay,
Jones and Assaf. The idea is that we are limited in how we can register
different volumes to each other because differences between volumes are due to
both artifactual effects that we'd like to correct for: motion and eddy
currents; but also due to systematic effects that we'd like to retain. Different
parts of the tissue lose signal because of the different gradients applied in
different scans. This is particularly pernicious for high b-values (where a lot
of the signal is lost) and for parts of the brain in which orientation changes
gradually. The approach proposed by Amitay et al. is that a model of the
diffusion in high b-values could be used to predict what the image should look
like. This prediction is then used as a target for registration. This approach
was subsequently popularized by Andersson and Sotiropolous in FSL's popular
`eddy` tool (and their approach is also described in a
[paper](https://www.sciencedirect.com/science/article/pii/S1053811915009209)).
Their model of diffusion is slightly more complex than the CHARMED model used by
Amitay et al., but it's not clear that it more accurately represents the data.
Matt has really run with this approach by using the well-motivated 3D SHORE
model to fit and predict the data using a cross-validation approach (he calls it
[SHORELine](https://github.com/mattcieslak/ohbm_shoreline/blob/master/cieslakOHBM2019.pdf)).
However, in line with the goals of `qsiprep`, this approach would be limited to
multi-shell diffusion. For `dmriprep`, we need to expand this approach slightly
to also work for single-shell data. So, we need a model that accurately predicts
the data. Matt's previous experiments suggest that DTI systematically fails in
some places (this is well-understood as a consequence of complex fiber
configurations that are not well-captured by DTI). On the other hand, CSD seems
to overfit. Luckily, during my postdoc, I developed a model that does exactly
that: [fits the data and predicts it really accurately](https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0123272), and fortunately this model
is implemented in DIPY, including both fitting and prediction. One of the next
stages in development (already prototyped by Derek [here](https://github.com/nipreps/dmriprep/pull/62)) uses this Sparse Fascicle Model as the predictive model in the heart of a SHORELine-like algorithm. To be continued!

Finally, the last take-home is my optimism about community-led projects that
pool knoweldge, talent and resources across disparate groups and institutions.
Working together towards shared goals, when possible, makes a lot of sense. The
potential to save duplicated effort and to produce outcomes that take into
account more different use-cases is tantalizing.The challenges of bridging
between different work cultures, different scientific goals and inclinations, as
well as between the incentive structure governing the contributions of
individuals are non-trivial, but learning more about the patterns of
collaboration that facilitate productive and happy collaborations is a
worthwhile endeavour in and of itself. Maybe, like families, all happy
collaborations are alike in some essential way? Hopefully, that's exactly
where `dmriprep` is headed.