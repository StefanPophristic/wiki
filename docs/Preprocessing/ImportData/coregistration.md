---
layout: default
title: "3. Coregistration"
parent: "1. Import Data"
grand_parent: "Preprocessing"
nav_order: 3
has_children: true
---

# Coregistration

## What do we mean by coregistration?

Coregistration refers to the process of aligning the points and markers (obtained using the FastScan laser) with a brain. It is always preferable to use the participant's own MRI if available; alternatively, the fsaverage brain is used.

Fsaverage and information about how to download it can be found here: https://surfer.nmr.mgh.harvard.edu/fswiki/FsAverage

Important note: If you already have fsaverage, either in one of your own project folders or wish to copy it from someone else's project folder on the server, you CANNOT just copy-paste the files as you'd do with other files. This will likely corrupt the files. Instead, click the 'fsaverage=>SUBJECTS_DIR' button in the coregistration GUI (see below).

## Step-by-step procedure

Activate a conda environment with the relevant packages. Note that the coreg command doesn't work with all versions of MNE; if this is the case in your environment, you can create a separate environment with MNE version 0.15.2, which has functioning GUIs.

Note that your GUI might look slightly different than what is illustrated below. Most notably, some versions of MNE use big discs rather than points to visualize the headshape—see "Additional resources" for a tutorial how to do coregistration with these discs.

1. Execute the following command to open the coreg GUI:

```
mne coreg
```

![coreg_initial_gui](../../../images/coreg_initial_gui.png)

2. In the 'MRI Subject' field, input the path to your folder with MRIs. If you do not have a structural MRI for this subject, choose 'fsaverage'.

3. Input fif-file in the 'Digitization Source' field. Now you should see that points and markers  associated with the subject. They will typically not be aligned with the head (not even if you have a structural scan).

![coreg_wrong_placement](../../../images/coreg_wrong_placement.png)

4. Now the goal is to align the headshape with the head to the extent possible.

#### If you have an MRI for your participant

If you have a structural scan, this is best done using the fiducials and other anatomical information, and you should be able to get a pretty good fit. The most important part of using an MRI is **DO NOT TOUCH THE SCALING PARAMETERS**. Your MRI should already match the headshape in terms of dimensions.

Instead, you can directly work on translating and rotating your head scan points to match your participant's MRI.

#### If you do not have an MRI for your participant
You will have to use fsaverage, a premade MRI meaned across many subjects to best represent the structure of the head and the brain.  See above for instructions on how to get fsaverage into your subjects folder. Because the goal of coregistration is to reach a good match between head shape and where the brain is located, you have to modify this template to fit your subject as best possible.

The following points will describe your options for fitting the head beginning with the options on the left side of the screen followed by the options on the right side of the screen.

Regardless of whether you're using an MRI or fsaverage, you can first indicate if participants had lot of hair, i.e., if the cap used when digitization the headshape with FastScan was far away from the head or not. You can (and should) also omit points that are a certain distance away from the head, e.g., if the cap was 'lumpy'.

The 'View' options simply turn the head. You can also do this using your mouse.

Moving to the right column, you have a number of options for modifying the headshape itself.

'Scaling parameters' lets you increase/decrease the size of the head along the X, Y, and Z axes. Choose '3-axis' as your scaling mode if you want to adjust the axes independently of each other. You have the option of clicking 'Fit (ICP)' to get a first pass at this. ICP is short for Iterative Closest Point algorithm, which minimizes the distance between the digitized headshape and the MRI/fsaverage surface.

'Translation and Rotation' allows you to move and—yes, you've guessed it—rotate the headshape along the X, Y, and Z axes. Again, you can hit 'Fit (ICP)' or, alternatively, 'Fit Fid.' (short for 'fiducials') to align the headshape to the MRI/fsaverage surface.

#### Fitting MRI scanned participants
If you are coregistering using an MRI for your participant, your goal is to get the fiducials of your head scan to match the fiducials of the MRI as best as possible. 

If your MRI does not contain fiducial information, you will have to manually place them on your MRI in the MNE coregistration GUI. This is done by unclicking "lock fiducials" seen in the image below, and either manipulating the X, Y, and Z coordinates, or clicking on the anatomical points on the MRI through the GUI. These fiducials coordinates can then be saved and you can continue coregistering.

![coreg_fiducials](../../../images/ImportData/coregistration/fiducials_mne_coreg.png)

#### Fitting non-MRI scanned participants
If you are using the fsaverage brain, it's often not sufficient to simply hit one or more of the 'Fit' buttons—you'll typically have to do some manual adjusting yourself. 
Keep note of the following points:
- The goal is to make the points fit as closely as possible to the surface without burying points.
- Every circle or disc is a marker measurement. Therefore they need to be visible, outside of the head.
- The "grow hair" tool on the left side of the coregistration GUI can be used to change then size of the MRI scan to match the head shape scan. This is particularly useful for participants with a lot of hair.
- As a rule of thumb, the amount of error between the MRI and head scan fiducials should be less than 8 millimeters, and the average points error should be less than 10 millimeters. These measurements can be found in the bottom right of the coregistration GUI.

In the end, you should have something that looks along the lines of this:

![coreg_done](../../../images/coreg_done.png)

5. Click 'Save As ...' and then 'OK' to save the file as 'R#-trans'. Saving will take several minutes.

A piece of advice: Document your coregistration process somehow, e.g., by taking screenshots of the GUI so you can see how well the headshape and surface fit and have the metrics available in case you need to go back to this preprocessing step.

## Additional resources

From MNE Python's website, illustrates the coregistration process with headshape "discs" instead of points:
https://mne.tools/stable/generated/mne.gui.coregistration.html
