---
layout: default
title: "Source Reconstruction"
nav_order: 5
has_children: true
---

# Code
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

# What is source reconstruction?

The signal picked up from MEG sensors is a mixture of signals from multiple brain regions. Source reconstruction disentangles this mixture, mapping the measured magnetic fields back to their neural origins in the brain. This allows us to localize brain activity during a given cognitive or perceptual process, examine functional connectivity between different brain areas and integrate the observed MEG data to structural imaging (e.g. MRI), for anatomically informed analyses.

# Two problems

There are two important concepts in source reconstruction:

The forward problem involves predicting the magnetic fields measured by the sensors based on known neural source locations and head anatomy. This is a well-posed problem with a unique solution, but its accuracy depends on precise modeling of the head and brain.

The inverse problem, on the other hand, aims to determine the neural sources that generated the recorded magnetic fields. This problem is ill-posed, meaning there are infinitely many possible solutions, and it requires mathematical constraints and prior assumptions to estimate the most plausible source configuration.

(insert picture here)

# Forward Modelling

The forward solution aims to answer the question: Given a specific neural activity pattern in the brain, how would the configuration of magnetic field that are picked up by the MEG sensors look like?

To construct a forward model, we need
1. Representations of neural activity - typically modeled as dipoles (mathematical representations of current flow in the brain)
2. Volume conduction models - how magnetic fields propagate through the different tissues of the brain and head.
3. Location of the sensors relative to the head.

Then, we can compute the lead field matrix - essentially mapping each potential source of neural activity in the brain to the different MEG sensors.

The accuracy of the forward model also depends on the level of detail of our volume conduction model. We could either use spherical head models which are computationally simple, but less accurate models that assume that the head is perfect sphere. Another option that is more accurate but involves some extra steps would be to use realistic head models which are more detailed representations of head anatomy based on MRI data.

## Head Models

Two widely used approaches for realistic head modeling are boundary-element models and finite-element models.

Boundary element models divide the head into discrete boundary surfaces representing the interfaces between different tissues (e.g., brain, skull, scalp). Current flow is modelled using a geometry that is defined by these boundaries. Boundary element models are a simplification because here we assume that each tissue is a homogeneous layer with uniform properties.

In contrast, finite element models represent the head as a volumetric mesh composed of small, discrete elements (such as tetrahedra or hexahedra), which can represent the entire volume of each tissue. This allows for a more complex and heterogeneous modelling of tissue properties (such as anisotropy in white matter, where there is different conductivity in different directions) and fine details of the head’s anatomy.

## The New York Head

For both boundary and finite element modelling it always is preferable to use an individual MRI scan to accurately capture the subject’s specific anatomy, which allows for a more personalized and precise representation of the head geometry. However, in cases where an individual MRI is not available, we would use an MRI of a standard subject or a template head model for creating the forward model. The New York Head is a widely used template head model that has been created using MRI data from 152 human subjects. It has been tested and shown to be a reliable and accurate approximation of individual head geometries. The New York Head provides an efficent and accesible solution that can be used as a standard head model whenever an individual MRI is not available or the costs of acquiring individual MRI data are too high.

# Inverse Modelling

The inverse solution aims to estimate the sources of neural activity inside the brain given the measured sensor data. The inverse solution is ill-posed, meaning there are often multiple possible sources that could explain the same set of sensor measurements. This is because the sensors measure a mixture of signals from various brain regions, and there’s no one-to-one correspondence between the sources in the brain and the signals on the sensors.

There are different approaches to address this problem, some of which we will discuss here.

## Mininum Norm Estimate

The goal of the minimum norm estimate is to estimate the distribution of neural sources in the brain that most likely produced the recorded sensor signals, while minimizing the overall spatial norm (the total amount of activity across all sources).

##

# File Organization
