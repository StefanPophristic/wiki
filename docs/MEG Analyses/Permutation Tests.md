---
layout: default
title: "Spatiotemporal clustering test"
parent: "MEG Analyses"
nav_order: 1
has_children: false
section: "MEG"
---

# 8. Spatiotemporal Cluster-Based Permutation Test on averaged MEG Source Data

## 1. Overview

This documentation describes the workflow for performing spatiotemporal cluster-based permutation tests on MEG source-localized data. The analysis pipeline consists of two stages:

1. **Source reconstruction:** Transform sensor-level epochs into source estimates on the cortical surface
2. **Group-level statistical testing:** Group level statistical testing (compare conditions across subjects)

The pipeline is implemented using **MNE-Python (version 1.9.0)**.

---

## 2. Part 1: Source Reconstruction

### 2.1 Directory Structure and Setup

The following documentation describes how we generally set up the directory. Each subject will have data with multiple file types. The analysis requires the following directory organization (the following tasks are meant to be run on the HPC):

| Directory | Contents |
|-----------|----------|
| `main_dir` | Root directory for the project (`/scratch/bl3136/AdjOrder`) |
| `mri_dir` | FreeSurfer reconstructions for each subject |
| `meg_dir` | MEG data, epochs, forward models, covariance matrices |
| `stc_dir` | Source time course (STC) files organized by condition |
| `stats_dir` | Statistical results (cluster test outputs) |
| `fig_dir` | Figures and visualizations |

```python
# Define directories
main_dir = '/scratch/bl3136/AdjOrder'
mri_dir  = os.path.join(main_dir, 'mri')
meg_dir  = os.path.join(main_dir, 'meg')
stc_dir  = os.path.join(main_dir, 'stc')
```

---

### 2.2 Epoch Parameters

**Purpose:** MEG records continuous brain activity, but we're interested in brain responses to specific events (stimuli). "Epoching" cuts the continuous data into short segments time-locked to each stimulus. The **baseline period** (-0.1 to 0s here as an example) captures brain activity *before* the stimulus appears—this is assumed to be "noise" or background activity, and we subtract it from the post-stimulus period to isolate the actual neural response to the stimulus.

Epochs are extracted with the following parameters:

| Parameter | Value (Example) | Description | n 
|-----------|-------|-------------|
| `epoch_tmin` | -0.1 s | Start of epoch |
| `epoch_tmax` | 0.8 s | End of epoch |
| `epoch_baseline` | (-0.1, 0) | Baseline correction window |

```python
# Define epoch parameters
epoch_tmin     = -0.1
epoch_tmax     = 0.8
epoch_baseline = (-0.1, 0)

# Load epochs
epochs = mne.read_epochs(epochs_fname)

# Equalize trial counts across conditions
epochs.equalize_event_counts()
```

**Note:** `equalize_event_counts()` ensures balanced conditions by randomly dropping trials from conditions with more trials, preventing bias in condition comparisons.

---

### 2.3 Experimental Conditions

We assign different conditions with numeral labels to mark when each type of the stimulus occured. The experiment might include event codes as follows:

| Condition Name | Event Code |
|----------------|------------|
| Condition 1 (replace this with your own condition name) | 1600 |
| Condition 2 | 1610 |
| Condition 3 | 1620 |
| ... | ... | 

```python
# Define events
event_id = dict(
    Condi_1  = 1600, 
    Condi_2  = 1610,
    Condi_3  = 1620,
)

# Compute evoked response for each condition
evks = []
for cond in cond_names:
    evk = epochs[cond].average()
    evks.append(evk)
```

---

### 2.4 Source Space Setup

The source space defines all the possible locations where neural activity could come from. We place thousands of candidate source points (vertices) on the cortical surface. The `ico4` setting creates ~5,000 evenly-spaced points across both hemispheres.

```python
src = mne.setup_source_space(
    subject=subj, 
    spacing='ico4', 
    subjects_dir=mri_dir, 
    n_jobs=-1
)
src.save(src_fname, overwrite=True)
```

---

### 2.5 Forward Model Computation

The forward model answers: "If a source at location X is active, what would the MEG sensors measure?" This requires knowing (1) where the sensors are relative to the brain (coregistration), (2) where the candidate sources are (source space), and (3) how electrical currents spread through head tissues (BEM model). The forward model is the bridge between brain activity and sensor measurements.

The forward solution requires three components:

1. **Coregistration transform** (`-trans.fif`): Aligns MRI and MEG coordinate systems
2. **Source space** (`-ico-4-src.fif`): Defines candidate dipole locations
3. **BEM solution** (`-inner_skull-bem-sol.fif`): Single-shell conductor model for MEG

```python
# Define file paths
trans_fname = os.path.join(subj_dir, f'{subj}-trans.fif')
src_fname   = os.path.join(mri_dir, subj, 'bem', f'{subj}-ico-4-src.fif')
bem_fname   = os.path.join(mri_dir, subj, 'bem', f'{subj}-inner_skull-bem-sol.fif')

# Compute forward model
fwd = mne.make_forward_solution(
    info=info, 
    trans=trans_fname, 
    src=src, 
    bem=bem_fname, 
    ignore_ref=True
)
```

**Note:** For MEG, a single-shell BEM (inner skull only) is sufficient because magnetic fields are minimally affected by tissue conductivity differences.

---

### 2.6 Noise Covariance Estimation

MEG signals contain both brain activity and noise (environmental interference, sensor noise, physiological artifacts). The noise covariance matrix characterizes the statistical properties of this noise.

```python
cov = mne.compute_covariance(
    epochs, 
    tmin=epoch_baseline[0],  # -0.1
    tmax=epoch_baseline[1],  # 0
    method=['shrunk', 'diagonal_fixed', 'empirical']
)
```

---

### 2.7 Inverse Operator and Source Estimation

The inverse solution answers the opposite question: "Given what the sensors measured, where in the brain did the activity originate?"

```python
# Inverse parameters
fixed = False  # False = unsigned data, True = signed data
SNR = 3        # 3 for averaged data, 1 for single trials
lambda2 = 1.0 / SNR ** 2.0

# Convert forward solution for fixed orientation (if needed)
if fixed:
    fwd = mne.convert_forward_solution(fwd, surf_ori=True)

# Create inverse operator
inv = mne.minimum_norm.make_inverse_operator(
    info, fwd, cov, 
    fixed=fixed, 
    depth=0.8, 
    loose='auto'
)

# Apply inverse to evoked data
stc = mne.minimum_norm.apply_inverse(evk, inv, lambda2=lambda2, method='dSPM')
```

**Orientation options:**

| Setting | `fixed` | Output | Use case |
|---------|---------|--------|----------|
| Free | False | Unsigned (absolute) | When polarity is uncertain |
| Fixed | True | Signed | When comparing activation vs. deactivation |

---

### 2.8 Morphing to Common Space

To compare or average brain activity across subjects, we need to align everyone's data to a common template brain (fsaverage). 

```python
morph = mne.compute_source_morph(
    stc, 
    subject_from=subj, 
    subject_to='fsaverage', 
    subjects_dir=mri_dir, 
    spacing=4  # ico4 resolution
)
stc_fsavg = morph.apply(stc)
```

---

### 2.9 Output Files

```python
# Output path structure
stc_fname = os.path.join(
    stc_dir, 'stc_evoked', cond,
    f'{subj}_{cond}_dSPM_unsigned'
)
stc_fsavg.save(stc_fname, overwrite=True)
```

**File naming:** `{subject}_{condition}_dSPM_{signed|unsigned}`

---

## 3. Part 2: Group-Level Statistical Testing

### 3.1 Loading and Organizing Source Data

For group statistics, we need all subjects' data in a single, organized structure. The 4D array (conditions × subjects × vertices × time) allows us to efficiently compute statistics across subjects at every point in space and time simultaneously.

Source estimates are loaded for all subjects and conditions into a 4D array:

```python
# Parameters
n_subj     = 33      # Number of subjects
n_cond     = 2       # Number of conditions
n_vert     = 5124    # Total vertices (2562 per hemisphere)
n_times    = 901     # Time points (-100 to 800 ms)

# Initialize data matrix
stcs_mtx = np.empty((n_cond, n_subj, n_vert, n_times))

# Load all STCs
for c, cond in enumerate(cond_list):
    for s, subj in enumerate(subjects):
        stc_fname = os.path.join(stc_dir, 'stc_evoked', cond, 
                                 f'{subj}_{cond}_dSPM_unsigned')
        stc = mne.read_source_estimate(stc_fname, subject='fsaverage')
        stcs_mtx[c, s, :, :] = stc.data

# Transpose for MNE format: (n_cond, n_subj, n_times, n_vertices)
stcs_mtx = np.transpose(stcs_mtx, [0, 1, 3, 2])
```

---

### 3.2 Data Normalization

Different subjects may have different overall activation levels due to factors unrelated to the experiment (head position, skull thickness, attention levels). Z-scoring each subject's data removes these overall magnitude differences while preserving the *pattern* of activity across space and time. This ensures that one subject with unusually strong signals doesn't dominate the group results.

Each subject's data is z-scored across all vertices and time points:

```python
# Z-score normalization for each subject
stc_norm = stats.zscore(stc.data, axis=None)  # Compute over entire array
```

**Why normalize?**
- Removes inter-subject variability in overall magnitude
- Preserves spatial and temporal patterns within each subject
- Must be done *before* permutation testing (preserves subject-specific variance structure)
- Makes data comparable across subjects with different signal strengths

---

### 3.3 Region of Interest (ROI) Definition

Testing every vertex on the brain increases computational time and the multiple comparisons burden. If you have a hypothesis about *where* effects should occur (e.g., "language processing involves temporal cortex"), you can restrict the analysis to that region. Conversely, you might exclude regions known to be uninformative (like the medial wall, which contains no cortex).

The analysis supports both excluding and including specific brain regions:

#### Option A: Excluding Regions (e.g., Medial Wall)

```python
# Load atlas labels
labels = mne.read_labels_from_annot(
    'fsaverage', 'PALS_B12_Lobes', 'both', 
    subjects_dir=mri_dir
)

# Get medial wall label
roi_name = 'MEDIAL.WALL'
roi_lh = [l for l in labels if l.name == f'{roi_name}-lh'][0]
roi_rh = [l for l in labels if l.name == f'{roi_name}-rh'][0]

# Get vertex indices to exclude
hemi_idx = np.arange(0, n_hemivert, 1)
roi_lh_idx = roi_lh.get_vertices_used(vertices=hemi_idx)
roi_rh_idx = roi_rh.get_vertices_used(vertices=hemi_idx)
```

#### Option B: Including Regions (e.g., Temporal Lobe)

```python
# Get temporal lobe label
roi_name = 'LOBE.TEMPORAL'
roi_lh = [l for l in labels if l.name == f'{roi_name}-lh'][0]

# Get vertices to INCLUDE
temporal_lobe_idx = roi_lh.get_vertices_used(vertices=hemi_idx)

# Exclude everything NOT in temporal lobe
exclude_idx = np.setdiff1d(np.arange(n_hemivert), temporal_lobe_idx)
```

---

### 3.4 Time Window and Hemisphere Selection

Similar to spatial ROIs, you can restrict the analysis to specific time windows based on your hypotheses (e.g., "semantic processing occurs 300-500ms post-stimulus"). This reduces computation time and increases statistical power by excluding irrelevant time periods where you don't expect effects.

```python
# Define hemisphere
hemi = 'lh'  # Options: 'lh', 'rh', or 'both'

# Define time window of interest (in ms)
toi = (500, 800)
toi_times = np.arange(toi[0], toi[1] + 1)

# Get time indices
tmin_idx = np.where(times == toi[0])[0][0]
tmax_idx = np.where(times == toi[1])[0][0]

# Extract data for selected hemisphere and time window
if hemi == 'lh':
    X = [stcs_norm_mtx[c][:, tmin_idx:tmax_idx+1, :n_hemivert] 
         for c in range(len(cond_list))]
elif hemi == 'rh':
    X = [stcs_norm_mtx[c][:, tmin_idx:tmax_idx+1, n_hemivert:] 
         for c in range(len(cond_list))]
else:  # both hemispheres
    X = [stcs_norm_mtx[c][:, tmin_idx:tmax_idx+1, :] 
         for c in range(len(cond_list))]

# Result shape: (n_subj, n_times, n_vertices)
```

---

### 3.5 Spatiotemporal Cluster Test: One-Sample t-test (Paired Comparison)

When comparing two conditions within the same subjects (e.g., grammatical vs. ungrammatical sentences), we compute each subject's difference (Condition A minus Condition B) and test whether these differences are significantly different from zero across subjects. This is a "paired" or "within-subject" design, which is powerful because each subject serves as their own control.

#### Why Cluster-Based Permutation Testing?

With thousands of vertices and hundreds of time points, you're performing millions of statistical tests. Traditional correction methods (like Bonferroni) would be far too conservative—you'd never find anything.

Cluster-based permutation testing solves this by:

1. **Clustering:** Real brain effects tend to be spatially and temporally contiguous. Random noise is scattered. So we group neighboring significant points into "clusters."
2. **Permutation:** We randomly shuffle condition labels thousands of times to build a "null distribution" of what cluster sizes occur by chance.
3. **Correction:** A cluster is significant only if it's larger than 95% of clusters in the null distribution.

#### Step 1: Compute Difference Array

```python
# Contrast: Condition 1 > Condition 2
Xdiff = X[0] - X[1]  # Shape: (n_subj, n_times, n_vertices)
```

#### Step 2: Set Statistical Threshold

```python
# Parameters
tail       = 0      # 0: two-tailed, 1: upper, -1: lower
p_thresh   = 0.05   # Cluster-forming threshold
df         = n_subj - 1  # Degrees of freedom

# Compute t-threshold
if tail == 0:
    t_thresh = stats.distributions.t.ppf(1 - p_thresh/2, df=df)
elif tail == -1:
    t_thresh = -stats.distributions.t.ppf(1 - p_thresh, df=df)
else:
    t_thresh = stats.distributions.t.ppf(1 - p_thresh, df=df)
```

#### Step 3: Compute Spatial Adjacency

```python
# Load fsaverage source space
src_fname = os.path.join(mri_dir, 'fsaverage', 'bem', 'fsaverage-ico-4-src.fif')
src = mne.read_source_spaces(src_fname)

# Compute adjacency matrix for selected hemisphere
if hemi == 'lh':
    adjacency = mne.spatial_src_adjacency(src[:1])  # Left only
elif hemi == 'rh':
    adjacency = mne.spatial_src_adjacency(src[1:])  # Right only
else:
    adjacency = mne.spatial_src_adjacency(src)      # Both
```

#### Step 4: Run Cluster Test

```python
from mne.stats import spatio_temporal_cluster_1samp_test

T_obs, clusters, clusters_pvals, h0 = clu = \
    spatio_temporal_cluster_1samp_test(
        Xdiff,
        n_permutations=10000,    # Number of permutations
        threshold=t_thresh,       # Cluster-forming threshold
        tail=tail,                # Two-tailed test
        n_jobs=-1,                # Use all CPU cores
        adjacency=adjacency,      # Spatial connectivity
        spatial_exclude=exclude_idx,  # Vertices to exclude
        seed=1119                 # For reproducibility
    )
```

---

### 3.6 Spatiotemporal Cluster Test: One-Way Repeated-Measures ANOVA

When you have three or more conditions to compare simultaneously, a t-test isn't appropriate. ANOVA tests whether *any* of the conditions differ from each other. The "repeated-measures" part means the same subjects experienced all conditions, which (like paired t-tests) controls for individual differences.

#### Define Custom F-statistic Function

```python
from mne.stats import f_mway_rm, f_threshold_mway_rm, spatio_temporal_cluster_test

# ANOVA parameters
factor_levels = [n_cond]  # One factor with n_cond levels
effects = "A"             # Main effect of factor A
return_pvals = False      # Don't need p-values for clustering

def stat_fun(*args):
    """Custom function to compute F-values."""
    return f_mway_rm(
        np.swapaxes(args, 1, 0),
        factor_levels=factor_levels,
        effects=effects,
        return_pvals=return_pvals
    )[0]
```

#### Compute F-threshold and Run Test

```python
# Compute F-threshold for cluster formation
f_thresh = f_threshold_mway_rm(n_subj, factor_levels, effects, p_thresh=0.05)

# Run cluster test
F_obs, clusters, clusters_pvals, h0 = clu = \
    spatio_temporal_cluster_test(
        X,                        # List of condition arrays
        stat_fun=stat_fun,        # Custom F-statistic function
        n_permutations=1000,
        threshold=f_thresh,
        tail=1,                   # F-tests are always upper-tailed
        n_jobs=-1,
        adjacency=adjacency,
        spatial_exclude=exclude_idx,
        seed=1119
    )
```

**Note:** F-tests are always one-tailed (`tail=1`) because F-values are always positive and we're testing for any difference between conditions.

---

### 3.7 Interpreting Results

#### Output Variables

| Variable | Shape | Description |
|----------|-------|-------------|
| `T_obs` / `F_obs` | (n_times, n_vertices) | Observed test statistic at each spatiotemporal point |
| `clusters` | list of tuples | Each tuple contains (time_indices, space_indices) |
| `clusters_pvals` | (n_clusters,) | P-value for each cluster |
| `h0` | (n_permutations,) | Null distribution of maximum cluster masses |

#### Extracting Significant Clusters

```python
# Find significant clusters
clu_p_thresh = 0.05
sig_cluster_idx = np.where(clusters_pvals < clu_p_thresh)[0]
print(f"Found {len(sig_cluster_idx)} significant cluster(s)")

# For a specific cluster (e.g., cluster index 3)
c_idx = 3
print(f"Cluster p-value: {clusters_pvals[c_idx]}")

# Get time and vertex indices
clu_tidx = np.unique(clusters[c_idx][0])  # Time indices
clu_vidx = np.unique(clusters[c_idx][1])  # Vertex indices

# Convert to actual time values
sig_times = toi_times[clu_tidx]
print(f"Significant time window: {sig_times[0]} - {sig_times[-1]} ms")
print(f"Number of vertices in cluster: {len(clu_vidx)}")
```

---

### 3.8 Saving Results

Cluster results are saved using pickle for later analysis and visualization:

```python
import pickle
import datetime

# Generate filename with metadata
current_date = datetime.date.today()
contrast_name = 'WOtest_ROfloc'
search_area = 'left_hemi'

pickle_fname = os.path.join(
    stats_dir, 
    f'clu_{contrast_name}_{hemi}_{search_area}_{toi[0]}-{toi[1]}_{n_subj}subjs_{current_date}.pickled'
)

# Save results
with open(pickle_fname, 'wb') as f:
    pickle.dump(clu, f)
```

**Filename convention:** `clu_{contrast}_{hemisphere}_{region}_{timewindow}_{nsubjects}_{date}.pickled`

---

## 4. Visualization

### 4.1 Time Course Plots

After finding a significant cluster, you want to understand *what* the effect looks like. Time course plots show how activation in the cluster region evolves over time for each condition. This reveals whether one condition is consistently higher, whether effects emerge at specific latencies, and the temporal dynamics of the difference.

```python
fig, axes = plt.subplots(1, 1, figsize=(6, 3))
colors = ['#ec7744', '#8bd450', 'hotpink']

for c, cond in enumerate(cond_list):
    # Average within cluster vertices
    cond_evk = stcs_mtx[c][:, :, clu_vidx].mean(axis=2)
    
    # Compute mean and standard error across subjects
    cond_avg = cond_evk.mean(axis=0)
    cond_se  = cond_evk.std(axis=0) / np.sqrt(n_subj)
    
    # Plot with shaded error region
    axes.plot(times, cond_avg, color=colors[c], lw=1.5, label=cond)
    axes.fill_between(times, cond_avg - cond_se, cond_avg + cond_se, 
                      color=colors[c], alpha=0.15)

# Mark stimulus onset
axes.axvline(0, ls='dashed', lw=1.5, color='lightslategrey', alpha=0.5)

# Highlight significant time window
ymin, ymax = axes.get_ylim()
axes.fill_betweenx((ymin, ymax), sig_times[0], sig_times[-1], 
                   color='grey', alpha=0.25)

axes.set_xlim(-100, 800)
axes.set_xlabel('Time (ms)')
axes.set_ylabel('Activation (z-score)')
axes.legend(loc='upper right')
axes.spines[['top', 'right']].set_visible(False)
plt.tight_layout()
plt.show()
```

---

### 4.2 Brain Surface Plots

Showing where on the brain the significant cluster is located. This is essential for interpreting your results in terms of known functional anatomy (e.g., "the effect is in superior temporal cortex, consistent with auditory language processing").

```python
# Create STC with cluster statistics
stc_tmp = stc.copy().mean()
stc_tmp._data[:, 0] = np.zeros(n_vert)

# Fill cluster vertices with mean T-values
if hemi == 'rh':
    stc_tmp._data[clu_vidx + n_hemivert, 0] = T_obs[clu_tidx][:, clu_vidx].mean(axis=0)
else:
    stc_tmp._data[clu_vidx, 0] = T_obs[clu_tidx][:, clu_vidx].mean(axis=0)

# Plot on brain surface
fig, axes = plt.subplots(1, 1, figsize=(3, 3))
stc_tmp.plot(
    subject='fsaverage',
    subjects_dir=mri_dir,
    surface='inflated',      # Options: 'pial', 'inflated', 'white'
    views='lat',             # Options: 'lat', 'med', 'ven', 'dor'
    hemi='lh',
    figure=fig,
    backend='matplotlib',
    clim={'pos_lims': [0, 1, 3]},
    background='white'
)
plt.show()
```

---

## 5. Key Methodological Considerations

### 5.1 Multiple Comparisons Correction

The permutation test ensures that your false positive rate (saying there's an effect when there isn't) is controlled at 5% for the *whole brain/time analysis*, not per-vertex.

- Controls family-wise error rate (FWER) at the cluster level
- P-values reflect probability of observing a cluster of equal or greater mass under the null hypothesis
- More sensitive than Bonferroni because it exploits the spatial/temporal structure of real effects

---


Helpful Tutorials:
- [Intro to Permutation Based Stats](https://www.youtube.com/watch?v=5Z7pIWMYi64)
- [Cluster Based Permutations in EEG](https://www.youtube.com/watch?v=DakPCBLY2mE)
- [Correcting for Multiple Comparisons](https://www.youtube.com/watch?v=Dx143jsZDIs)
