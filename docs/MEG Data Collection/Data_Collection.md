---
layout: default
title: Data Collection (MEG 160)
nav_order: 5
has_children: true
parent: "MEG Data Collection"
section: "MEG"
---

Once the room is setup and ready for a participant (see `Setup for Data Collection` page) you will use the `MEG 160` program on the data acquisition computer to collect your data. 

This page goes through how to use `MEG 160` and the steps you should take to collect data when you have a participant. We recommend reading through the entire page while you are piloting your data. 

If you are running a participant, you can jump to the [Running a participant](#running-a-participant) section to make sure you are following all of the steps. 

## Table of contents
{: .no_toc .text-delta }
1. TOC
{:toc}


When running a participant, you want to complete the following steps: 
1. [Open MEG 160](#opening-meg-160)
2. [Autotune](#autotune)
3. [Open Data Acquisition Window](#data-collection)
4. [Set Parameters using a condition library](#setting-up-your-condition-library)
5. [Type in Participant ID and Folder](#data-acquisition)
6. [Lock the machine](#data-acquisition)
7. [Take Marker Measurements](#data-acquisition)
8. [Record](#data-acquisition)
9. [Take Marker Measurements again](#data-acquisition)


# Opening MEG 160

We use the software `MEG 160` to collect all data. To open the software, click on the MEG 160 icon located on the desktop:

*Insert image*

# Autotune

Autotuning should be done before every recording session and if there is a noise-burst during the recording. 

*Insert image*

# Data Collection

The data collection button opens up a window with all the parameters and functions you need to record data. The window looks like this:

*Insert image*

The sections below describe the function of all the parameters, how to set up a condition library for your experiment, and what to do when running a participant. 

## Sections of the Data Collection Window

The window consists of three sections:
1. Real-Time Monitor
2. Sensor Control
3. Data Acquisition. 

*Insert Image* 

The parameters of each of these sections is described below. It is useful to familiarize yourself with these parameters before running participants. There are parameters that we often change, and ones we rarely touch—the ones we rarely touch are labeled as such. 

### Real Time Monitor

This portion of the window has parameters that control the display that you see on the large screen. You can tamper with these during a recording without affecting the recording itself.

- Waveform: toggles how the sensors are displayed on the viewscreen. Either as: 1) a row per sensor; 2) a butterfly plot; 3) the sensors displayed with relation to the participants head. The easiest way to detect noisebursts is option (1). When demonstrating to participants, the display (3) is most intuitive. 
- X-scale: the time-scale shown on the viewing screen. Typically set to 8s
- Y-scale: The scale of fT shown on the viewing screen (toggles how big the recorded activity appears). I recommend setting this to 8129 to easily detect noisebursts, which at this scale appear as giant black blobs. 

The less often used parameters:
- Station-O
- Station-1
- Run
- Freeze
- A/D: 

### Sensor Control

This set of parameters dictates the frequency at which activity will be recorded. This is most important set of parameters and should be carefully decided on before running any subjects.

- HPF: High Pass Filter. Filter out all data at a higher frequency than the one specified. For example, a high-pass filter of 0.1 Hz allows waves with a frequency of ≥ 0.1 Hz to pass. 
- LPF: Low Pass Filter. Filter out all data at a lower frequency than the one specified. For example, a low-pass filter of 200 Hz allows all waves with a frequency of ≤200 Hz to pass.

The HPF and LPF parameters are online filters. Read the Online Filters section for more information and considerations on how to set these. 

The less often used parameters are:
- Gain1:
- Gain2:
- Gain3: 
- Lock/Unlock:
- Sensor Check: 

### Data Acquisition

**Patient Information:**
- Patient ID: Set to the R-Number of your participant to properly label meta-data associated with your recording
- Patient Name: Set to the R-Number of your participant to properly label meta-data associated with your recording

Less used parameters are: 
- Digitizer
- MRI

**File Storage**
- Folder Name: Path to the folder where you would like your data to be saved to. Note that marker measurements are automatically saved to this specified location

**Examination Name**
- Exam. Name: Set the name of your experiment to properly label meta-data associated with your recording

**Continuous Mode**
These are the buttons associated with collected continuous data, i.e. the type of data we predominantly record. 
- Change: Change the frequency and/or maximum time of the recording. If your participant is taking longer in a task than expected, and the max time is running out—you can click and change the max time during the recording. 
- Pause: ??
- Abort: This ends the continuous recording and automatically opens a window to allow you to save it. Despite the name, this is the button you click to save your data!

**Evoked Mode**

**Marker Measurements**
These are the buttons you use to take marker measurements. You should take these at the start and end of every recording. More details about marker measurement parameters that pop-up after the marker measurement recording are given on the marker measurements page. 
- Start: Start the marker measurement recording
- Abort: Stop the marker measurement recording

**Condition Library**
- Condition Library: Automatically set the parameters of your recording. 

## Setting up your Condition Library

The condition library allows you to save all of your recording parameters so that you don't have to manually input them every time you run a new participant. *It is highly recommended to set a condition library before your first participant and use it for all of your participants.*

**To set up a condition library**, first manually input all of your desired parameters into the `Real Time Monitor` and `Sensor Control` sections. The `Data Acquisition` section has to manually be filled out every time you run a participant. Once those parameters are filled in, click on `Condition Library`. Condition Libraries are alphabetically listed. Open up the set with the first letter of your experiment name. Select an open slot (e.g. "untitled 03"). Click `load` and then `yes`. This will prompt you for a name of the new condition library. Type in the name of your experiment. 

**To load a condition library**, select `Condition Library`. Navigate to 


## Marker Measurements

Insert image explaining the "select channel" parameters

