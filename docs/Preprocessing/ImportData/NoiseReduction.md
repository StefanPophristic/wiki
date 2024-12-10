---
layout: default
title: "1. Noise Reduction"
parent: "1. Import Data"
grand_parent: "Preprocessing"
nav_order: 1
has_children: False
---

# Noise reduction

## What do we mean by noise reduction?

Noise reduction is the very first step in the preprocessing pipeline. All MEG recordings will inevitably contain noise, i.e., signal that we are not interested in. The MEG scanner is sensitive to electric fields other than those coming from the brain, whether it be biological noise (heartbeat, blinks, etc.), instrumental noise (from the MEG scanner itself), or environmental noise (the Earth's natural magnetic field, urban noise, etc.). The magnetically shielded room (MSR) hinders some of the external noise from contaminating the signal, but it doesn't fully attenuate it. The goal of the preprocessing pipeline is therefore to ready the data for analyses, with increasing the signal-to-noise ratio (SNR) being the primary objective of this.

The first step in the preprocessing pipeline is to "noise reduce" the data. The MEG recording will consist primarily of data from sensors close to the head (head sensors) but will also contain data from so-called reference sensors. Both head sensors and reference sensors are located in the dewar, but only the head sensors are sufficiently close to the head to pick up neural signals. In contrast, head sensors and reference sensors pick up (roughly) the same amount of environmental noise. The idea behind this initial noise reduction is therefore simple: We subtract the data recorded by the reference sensors from the data recorded by the head sensors, which leaves us with our signal of interest.

``   
head sensors - reference sensors = signal of interest
```

Note that the number of reference channels may differ from system to system. The MEG scanner in New York City has three reference channels (channels 157, 158, 159) while the MEG scanner in Abu Dhabu has eight reference channels.

## Step-by-step procedure

Noise reduction is done using the MEG 160 program. It can be found on the desktop computer that we use to record the participant in the MEG Lab or on one of the computers in the lab (currently located in the NYU Morphlab open office space, login credentials are next to the computer).

1. Open MEG160.
2. Click 'File' --> 'Open' and locate your .sqd-file.
3. Click 'Edit' --> 'Noise Reduction' to open the noise reduction GUI.
4. Click 'Execute' without changing any parameters.
5. Save your file by clicking 'File' --> 'Save as' --> 'R#_experiment_NR.sqd' (NR = noise reduced).

Repeat as appropriate if you have more than one .sqd-file (i.e., if you have multiple recordings) from the same participant.

## Examples on how to report this process in lab publications

MEG lab in New York City:
```
MEG data from each participant were first cleaned of environmental electromagnetic noise using the Continuously Adjusted LeastSquares Method (Adachi, Shimogawara, Higuchi, Haruta, & Ochiai, 2001) based on data collected at three reference channels placed away from the head. [Flick et al. 2021]
```

MEG lab in Abu Dhabu:

```
The procedures used for preprocessing first involved removing noise from the raw data by exploiting eight magnetometer reference channels located away from the participants’ heads; using the Continuously Adjusted Least Squares Method (CALM; Adachi, Shimogawara, Higuchi, Haruta, & Ochiai, 2001), with MEG160 software (Yokohawa Electric Corporation and Eagle Technology Corporation, Tokyo, Japan). [Gwilliams & Marantz 2015]
```

## Additional readings/materials

Adachi, Y., Shimogawara, M., Higuchi, M., Haruta, Y., & Ochiai, M. (2001). Reduction of Non-periodic Environmental Magnetic Noise in MEG Measurement by Continuously Adjusted Least Squares Method. IEEE Transactions on Applied Superconductivity, 11, 669–672.

Ryan Law's training slides: 1. MEG Intro (located on the server: '/Volumes/server/NEUROLING/Manuals and Tutorials/RyanLaw Training Slides')
