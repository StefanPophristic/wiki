---
layout: default
title: MEG Analyses
nav_order: 8
has_children: true
---

In this section we will describe and document some of the most commonly used analysis methods in MEG. Be aware that the exact analysis method you will use for your experiment is highly dependent on the exact questions your experiment is trying to answer, the type of data you have collected, and what your goal of the project is. So rather than tell you exactly how to do something, this section is meant to give you ideas on what types of analyses exist, how to begin implementing them in MNE python, and what considerations need to be taken into account when conducting these analyses.

# Time Domain

## ANOVA Models
## Linear Regression Models
## fROI and the Tarkiainen Localizer

# Decoding

Put very simply, decoding refers to the process of predicting the stimuli presented to participants. This is done by training a classifier (more on this below) to discriminate between the different categories using one set of neural data and testing its performance on a (typically different) set of neural data.

Why is decoding an interesting tool? The ability of a classifier to correctly classify an input as, e.g., A or B tells us something about how a given stimulus is represented neurally in time and/or space. If decoding is successful, this allows us to say something about how a given variable is encoded in neural activity. Finally, decoding takes into account fine-grained patterns in the data, thus setting is apart from traditional univariate analyses which typically averages out such fine-grained patterns.

# Time-Frequency Analyses

This is a set of analysis methods that our lab doesn't use as often. A time-frequency analysis is an analysis that focuses on the power (amplitude squared) of your signal across different frequencies binned into snippets of time, rather than looking at how your overall amplitude develops over time. Different frequencies have been associated with various processing affects, and decomposing signals in this way can illuminate differences in neural processing even when a time-domain analysis shows no effects.

Since these methods haven't been used to as great of an extent in the lab, this section is currently just an overview. Feel free to split it up into multiple sub-sections if you have more to add!
