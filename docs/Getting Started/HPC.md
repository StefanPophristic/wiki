---
layout: default
title: "HPC"
parent: "Getting Started"
nav_order: 2
---

# Code
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

Most stc calculations and analyses are quite computationally heavy and would take forever to be run locally. So we run them on the HPC, or rather High Performance Clusters. You can find the main NYU cluster website [here](https://sites.google.com/nyu.edu/nyu-hpc/home). You need to [sign up](https://sites.google.com/nyu.edu/nyu-hpc/accessing-hpc/getting-and-renewing-an-account) to be able to use it, which includes a necessary sponsorship from Alec or Liina.

# Access

You can access the HPC either through the gui via the [Open-On-Demand interface](https://sites.google.com/nyu.edu/nyu-hpc/hpc-systems/greene/software/open-ondemand-ood-with-condasingularity) or via terminal

```
ssh <NetID>@gw.hpc.nyu.edu
ssh <NetID>@greene.hpc.nyu.edu
```


# Uploading/Downloading files

You can both upload and download files via the gui system or via the command line. Uploading files via the terminal is much more efficient, as the gui system can crash when uploading files.


To upload files from terminal:

```
scp -r local_path NETID@greene.hpc.nyu.edu:hpc_path
```

To download files from Terminal:

```
scp -r NETID@greene.hpc.nyu.edu:hpc_path local_path
```

For example:
```
scp -r /Users/sp6961/Documents/preprocessing/Participant_1 sp6961@greene.hpc.nyu.edu:/scratch/sp6961/preprocessing_data/participants/
```

## Troubleshooting

If you get an error message like this one:
```
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@  WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!   @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the [...] key sent by the remote host is
[...]
```

You should run one of the following line to "load" the access to the hpc:
```
ssh-keygen -R greene.hpc.nyu.edu
```
If you are trying to transfer files from the server, and are getting a similar error message, try running this one:
```
ssh-keygen -R nellabny.psych.nyu.edu
```

If you get the following error message:
```
Host greene.hpc.nyu.edu not found in /Users/NETID/.ssh/known_hosts
```
Try running the following line in the terminal. This loads the known hosts:
```
rm ~/.ssh/known_hosts
```


# STC

This youtube video goes through the basic concepts of general linear modeling. The initial stc script creates the design matrices discussed here:  
https://www.youtube.com/watch?v=mZbK6KvMF2I
