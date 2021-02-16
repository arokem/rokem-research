---
layout: post
title:  "Life support"
date:   2020-08-24
---

Over the weekend, and inspired by a conversation we had on Friday in the weekly telecon of our data intensive research in connectomics (DIRIC) group, I started working on a new project that I call [life support](https://github.com/nrdg/life-support) (because what I really need right now are new projects...). The idea is rather simple and is not mine (it is due to my postdoc advisor, Brian Wandell): if you have a tractography and you try to create tract profiles, you often find that some features of the tract profile are more affected by the environment that the streamlines are passing through than by the characteristics of that bundle. This can muddy the interpretation of the tract profile in all kinds of ways, because the features may be due to the size of a certain narrow passage, or due to changes in other crossing pathways. To deal with this, one way is to generate a bundle-specific signal, based on a model, such as LiFE and then generate the tract profile based on this predicted signal. The advantage of this approach is that it could effectively remove confounding effects. The [notebook](https://github.com/nrdg/life-support/blob/master/2020-08-22-ls1.ipynb) I have in that repo is a first pass at doing this. For now, it's not working as well as I'd hoped, but one initial observation is how easy it is to start prototyping something like this with the new version of pyAFQ. 