---
layout: default
title: Regression Models
parent: Behavioral Analyses
nav_order: 1
has_children: False
---


#Regressions:

## Collinearity
If models have variables that are correlated, this may lead to issues in model fitting and interpretation. See papers below.

- [Baayen, Feldman, Schreuder (2006)](https://www.sciencedirect.com/science/article/pii/S0749596X06000441?casa_token=zYdOGPShiUEAAAAA:Kd7S5f0hcqvulHaQGykfNxYNKkejonV5TU5ybcYTmz6mlGriO3vPtWrffOJIwvm0wncdFdA). Solutions to collinearity: Orthogonalizing variables, PCA, PCA-Regressions
- [Tomaschek, Hendrix, Baayen (2018)](https://www.sciencedirect.com/science/article/pii/S0095447017302292): In depth descriptions on how to test for whether linear mixed effect models have correlated variables, and 3 different methods for dealing with them.

## Model Comarisons (AIC)
- [Burnham, Anderson (2002)](https://link.springer.com/book/10.1007/b97636?utm_source=chatgpt.com) Entire book on model comparison techniques and their interpretaions. Helpful section on AIC differences in chapter 2 (especially 2.6)
- [Sutherland et al. (2023)](https://royalsocietypublishing.org/doi/full/10.1098/rspb.2023.1261) Short read on variable selection for models when conducting AIC and their relationship between p-values and AIC
