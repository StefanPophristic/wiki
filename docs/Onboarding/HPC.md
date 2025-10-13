---
layout: default
title: "HPC"
parent: "Onboarding"
nav_order: 2
section: "Lab"
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


# Conda Environments

You can set up conda environments on the HPC! I found that this is necessary in order to install and use eelbrain on the HPC. 

1. Install Miniconda in /vast

Install miniconda. Because we have limited space in our home folder, you can install this in your /vast folder. 

```bash
cd /vast/sp6961
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh -b -p /vast/sp6961/miniconda3
```

2. Set up conda access

```bash
source /vast/sp6961/miniconda3/etc/profile.d/conda.sh
source ~/.bashrc

```

3. Create and actrivate the environment 

```bash
conda create -y -n eelbrain-env python=3.9
conda activate eelbrain-env
```

4. Install required packages
```bash
pip install numpy==1.26
pip install eelbrain
pip install mne
```

You can verify that everything worked via:
```
python -c "import eelbrain; print(eelbrain.__version__)"
python -c "import mne; print(mne.__version__)"
```

5. Register Environment with Jupyter

To be able to access the environment via jupyter, run the following commands:

```bash
export JUPYTER_DATA_DIR=/vast/sp6961/.jupyter-data

python -m ipykernel install \
  --name eelbrain-env \
  --display-name "Python (eelbrain-env)" \
  --prefix=$JUPYTER_DATA_DIR

mkdir -p ~/.local/share/jupyter/kernels
ln -s /vast/sp6961/.jupyter-data/kernels/eelbrain-env ~/.local/share/jupyter/kernels/
```

Now when you activate a session via the HPC Open On Demand Gui, when you open a jupyter notebook script, at the top right where it says somehting like `Python 3 (ipykernel)` select the option `Python (name of your environment)`. 