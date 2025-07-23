---
layout: default
title: SAGELD step 1
nav_order: 1
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
parent: Longitudinal trajectory
grand_parent: Step 1 Fit the null model
has_children: false
---

# **SAGELD step 1**


## Quick start for lme4

Please run the following code in R

### Display the example data set

```
PhenoFile = system.file("extdata", "simuLongPHENO.txt", package = "GRAB")
print(PhenoFile)
# "../GRAB/extdata/simuLongPHENO.txt"

LongPheno = data.table::fread(PhenoFile)
print(LongPheno)
#           IID      AGE GENDER LongPheno
#     1: Subj-1 49.76668      0  21.18404
#     2: Subj-1 46.97617      0  22.63922
#     3: Subj-1 48.60388      0  22.99897
#     4: Subj-1 48.47036      0  22.88830
#     5: Subj-1 50.51208      0  23.17735
#    ---                                 
# 10511:   f9_9 50.34522      1  19.04696
# 10512:   f9_9 50.44379      1  24.20562
# 10513:   f9_9 51.52763      1  24.86237
# 10514:   f9_9 49.90129      1  23.18408
# 10515:   f9_9 50.21092      1  21.56067
```

### Fit the null model

```
library(dplyr)
library(tidyr)
library(lme4)

nullmodel = lmer(LongPheno ~ 1 + AGE + GENDER + (AGE|IID), data = LongPheno)

summary(nullmodel)$coefficients
#               Estimate Std. Error   t value
# (Intercept) 37.1712626 1.10910848  33.51454
# AGE         -0.3263161 0.02216986 -14.71891
# GENDER       0.7080723 0.06096869  11.61370
```

> **Note**  
> - Adding a random slope related to the environmental exposure in the LMM is required to control for heterogeneity introduced by the environmental variable.
> - If [lme4](https://cran.r-project.org/web/packages/lme4/index.html) fails due to sparse longitudinal data, you can use glmmTMB. For example,
```
nullmodel = glmmTMB::glmmTMB(LongPheno ~ 1 + AGE + GENDER + (AGE|IID), data = LongPheno)
```
