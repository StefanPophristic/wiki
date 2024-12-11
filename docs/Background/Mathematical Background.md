---
layout: default
title: "Mathematical Background"
parent: "Background"
nav_order: 1
---



## Table of contents

{: .no_toc .text-delta }

1.  TOC {:toc}

------------------------------------------------------------------------

# Basic math for time-frequency analyses

## Fourier Transform

Underlying any frequency analysis of MEEG data, is the idea that the signal we are analyzing is composed of a mixture or sum of various sine waves. Below is a pure sine wave:

![sine](puresine.png)

(Small reminder: We define a frequency **f** as the number of cycles per second. The amplitude **A** of a given frequency is a measure of its magnitude or strength compared to 0, power is simply amplitude squared, and phase $\theta$ is the timing of the wave measured in radians.)

And this is its basic formula for any timepoint t:
$$
Asin(2 \pi ft + \theta)
$$

Now compare it to this signal from one MEG sensor:
 - image of EEG data from one channel here

Though the second wave seems really different from the first, it’s possible to reconstruct wave #2 by adding together sine waves of different frequencies and amplitudes. <!-- (NOTE TO EDITOR: maybe add same image as the time frequency section?)-->

In order to deconstruct or unmix a complex signal into smaller sine waves, we apply a ==Fourier Transform==. <!-- the previous text should show up as highlighted text; relace with bold if not--> The output of a Fourier Transform is a **Fourier Series**: a series of complex numbers where each number corresponds to a frequency. We can then use this series to get more information about the characteristics of each frequency in our data. The intricacies of time-frequency analysis will be covered in another chapter.

### Complex numbers, sine and cosine

Before getting to the fourier transform, we need to take a quick shortcut through the relationship between complex numbers and sine and cosine.

Think of a unit circle of radius 1 and circumference 2 $\pi$.
See an image of the unit circle below:
![unitcircle](unitcircle.png)

Thanks to [Euler’s formula] (https://mathvault.ca/euler-formula/), we can move between cosine and sine and complex numbers, and importantly express sine waves as complex numbers.

Referring back to the unit circle, *cos* represents the x axis and *sin* the y axis; thinking in terms of the formula, *cos* represents the real part of a complex number and *sin* represents the imaginary part of a complex number.

### Discreet Fourier series

Doing a discreet fourier transform of a complex sine wave boils down to creating different sine waves and computing the **dot product** of those sine waves with our data. We create these sine waves at different frequencies, modelling them as journeys along the unit circle where each sample represents $\frac{n}{N}$% of the total journey.

Each frequency is then represented as a complex number which also serves as a coordinate along the circle.

This is the formula for the DFT:

$$
X_k  = \sum_{n=0}^{N-1}x_n . e^{-i2\pi\frac{k}{N}n}
$$

Here $X_K$ is our data for a frequency *k*, at sample point *n* out of *N* samples.

For each complex number corresponding to a frequency, **phase** is the angle of the complex number, **amplitude** is its absolute value and **power** is: $real^2+imag^2$ (sum of the squares of the real and imaginary parts).

Because our data is sampled and not continuous, we need to take this into account when deconstructing and reconstructing it using its Fourier series. We can only (de/re)construct the signal up to its **Nyquist frequency** (which we determine based on the sampling frequency). Any frequency above it will be a "negative" frequency. (See [Aliasing](wiki/docs/Preprocessing/Filtering/temporalfiltering.md#Aliasing) section for more information).

--------

## Additional resources

- [Chapters 4-6 of this online textbook](https://brianmcfee.net/dstbook-site/content/ch04-complex/Intro.html)

<iframe width="560" height="315" src="https://www.youtube.com/embed/spUNpyF58BY?si=PlEF8ze3wApmArwi" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
