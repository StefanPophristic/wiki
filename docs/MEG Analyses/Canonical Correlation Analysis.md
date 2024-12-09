---
layout: default
title: "Canonical Correlation Analysis"
parent: "MEG Analyses"
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

# What is Canonical Correlation Analysis?

Canonical correlation analysis (CCA) is a statistical method used to explore the relationships between two sets of multivariate data. Let's say we are interested in comparing neural activity recorded using MEG to neural activity recording using EEG. We record from 157 MEG sensors and 256 EEG electrodes. Now have two multivariate time series - but we don't know how those MEG sensors relate to the EEG electrodes. CCA offers a data-driven way to find linear combinations of MEG sensors that are maximally correlated to linear combinations of EEG electrodes. In this case, CCA would help to identify shared patterns between MEG and EEG recordings, that might not be observed çusing standard univariate methods.

# Mathematical Background

We have two sets of multivariate variables:

- **X**: Our MEG recordings (a sensor by timepoint matrix)
- **Y**: Our EEG features (a electrode by timepoint matrix)

Now the goal of CCA is to find pairs of linear combinations of the variables in **X** and **Y** that are maximally correlated. These linear combinations are called canonical variates.

We can define:

- **u = Xw** and **n = Yv**, where **u** and **n** are the canonical variates and **w** and **v** are the weight vectors for **X** and **Y**, respectively. Essentially, w and v act as spatial filters for **X** and **Y**, respectively.

The objective is to maximize the correlation between **u** and **n**. Substituting **u = Xw** and **n = Yv**, this becomes:

<<<<<<< HEAD:docs/Canonical Correlation Analysis/Canonical Correlation Analysis.md
![](/images/cca/cca_1.png)

with the constraint that the canonical variates **u** and **n** have unit variance:

![](/images/cca/cca_2.png)
=======
![](../../images/cca/cca_1.png)

with the constraint that the canonical variates **u** and **n** have unit variance:

![](../../images/cca/cca_2.png)
>>>>>>> 7987f06df070bce4383a95dde888eac8127f2a98:docs/MEG Analyses/Canonical Correlation Analysis.md

where:
- **C<sub>XX</sub>** is the covariance matrix of **X**
- **C<sub>YY</sub>** is the covariance matrix of **Y**
- **C<sub>XY</sub>** is the cross-covariance matrix between **X** and **Y**

These constraints prevent trivial solutions (e.g., setting **w** and **v** to zero) or solutions that are arbitrarily scaled.

The objective function now can be written using Lagrangian multipliers, where we maximize the covariance between **u** and **n** while keeping the variances of **u** and **n** fixed to 1. Therefore, we effectively maximizing the normalized covariance, i.e. correlation.

<<<<<<< HEAD:docs/Canonical Correlation Analysis/Canonical Correlation Analysis.md
![](/images/cca/cca_3.png)
=======
![](../../images/cca/cca_3.png)
>>>>>>> 7987f06df070bce4383a95dde888eac8127f2a98:docs/MEG Analyses/Canonical Correlation Analysis.md

where **λ<sub>1</sub>** and **λ<sub>1</sub>** are the Lagrangian multipliers enforcing the unit variance constraint for **u** and **n**, respectively.

We can now take the derivatives of the Lagrangian with respect to **w**, **v**, **λ<sub>1</sub>** and **λ<sub>1</sub>** and set them to zero:

<<<<<<< HEAD:docs/Canonical Correlation Analysis/Canonical Correlation Analysis.md
![](/images/cca/cca_4.png)

This simplifies to:

![](/images/cca/cca_5.png)

Now we can substitute for ****v**:

![](/images/cca/cca_6.png)

And rearrange:

![](/images/cca/cca_7.png)
=======
![](../../images/cca/cca_4.png)

This simplifies to:

![](../../images/cca/cca_5.png)

Now we can substitute for ****v**:

![](../../images/cca/cca_6.png)

And rearrange:

![](../../images/cca/cca_7.png)
>>>>>>> 7987f06df070bce4383a95dde888eac8127f2a98:docs/MEG Analyses/Canonical Correlation Analysis.md

We can use matrix multiplication to compute:

**A = C<sub>XX</sub><sup>-1</sup> * C<sub>XY</sub> * C<sub>YY</sub><sup>-1</sup> * C<sub>XY</sub><sup>T</sup>**

And then solve the eigenvalue problem using eigenvalue decomposition:

**Aw = 2λ<sup>2</sup>w**

Which will give us the canonical weights **w**.

We can repeat the same steps and subsitute for **w** to solve for **v**.

Now we can compute the canonical variates **u = Xw** and **n = Yv**.

# Implementation

<<<<<<< HEAD:docs/Canonical Correlation Analysis/Canonical Correlation Analysis.md
There are implementations for CCA in [Python](https://scikit-learn.org/stable/modules/generated/sklearn.cross_decomposition.CCA.html) and [MATLAB](https://www.mathworks.com/help/stats/canoncorr.html)



=======
There are implementation for CCA in [Python](https://scikit-learn.org/stable/modules/generated/sklearn.cross_decomposition.CCA.html) and [MATLAB](https://www.mathworks.com/help/stats/canoncorr.html)
>>>>>>> 7987f06df070bce4383a95dde888eac8127f2a98:docs/MEG Analyses/Canonical Correlation Analysis.md
