---
layout: default
title: "3. Coregistration"
parent: "1. Import Data"
grand_parent: "MEG"
nav_order: 3
has_children: true
---

# coregistration
anaconda environment coreg

```
mne coreg
```


Save coregistration numbers to an excel file → this isn't saved anywhere, so good practice to have it saved if you ever have to redo it or whatever



File Structure
MNE assumes the following file structure. This can be found anywhere, but must be internally consistent.

Details
MRI Subject: file path to MRI folder with subfolders for different subjects
We usually don't have MRI's of our subjects. So instead we use the FSaverage, an average headshape (XYZ more info needed). Create a copy of the FSaverage files in your mri folder for the project. The FSaverage MRI files can be found in XYZ.
Digitization Source: path to .fif file of your experimental data
