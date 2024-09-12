---
layout: default
title: Generalized estimation equations
nav_order: 2
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
parent: Longitudinal mean
grand_parent: Step 1 Fit the null model
has_children: false
---

<head>
    <script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
    <script type="text/x-mathjax-config">
        MathJax.Hub.Config({
            tex2jax: {
            skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'],
            inlineMath: [['$','$']]
            }
        });
    </script>
</head>

# **Generalized estimation equations**

We display how to use [geepack](https://cran.r-project.org/web/packages/geepack/index.html), a R package to fit the null model for longitudinal traits. In this online tutorial, our main focus is to demonstrate how to obtain model residuals from the fitted null model.

## Example data sets

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

## Exchangeable working correlation structure

```
library(dplyr)
library(tidyr)
library(geepack)

# we must keep the order of subjects in LongPheno and frq the same!
LongPheno = LongPheno %>% arrange(IID) # We highly recommend sorting by IID first.

frq = LongPheno %>% group_by(IID) %>% summarize(n = n())
LongPheno = LongPheno %>% mutate(pseudoIID = rep(1:nrow(frq), times = frq$n))

nullmodel_exc = geeglm(LongPheno ~ AGE + GENDER, data = LongPheno, id = pseudoIID, corstr = "exc") # for exchangeable working correlation structure

summary(nullmodel_exc)$coefficients
#             Estimate Std.err    Wald  Pr(>|W|)
# (Intercept)  37.1621 1.13814 1066.12 0.000e+00
# AGE          -0.3261 0.02277  205.03 0.000e+00
# GENDER        0.7055 0.09318   57.31 3.719e-14
```

### Calculate working correlation matrix

```
working.matrix_exc = matrix(nullmodel_exc$geese$alpha, nrow = max(frq$n), ncol = max(frq$n)); diag(working.matrix_exc) = 1 # for exchangeable working correlation structure

print(working.matrix_exc[1:4,1:4])
#        [,1]   [,2]   [,3]   [,4]
# [1,] 1.0000 0.2009 0.2009 0.2009
# [2,] 0.2009 1.0000 0.2009 0.2009
# [3,] 0.2009 0.2009 1.0000 0.2009
# [4,] 0.2009 0.2009 0.2009 1.0000
```

### Obtain model residuals

```
modelreisd_exc = c()
pos = 0
for(i in 1:nrow(frq))
{
  modelreisd_exc = c(modelreisd_exc, solve(working.matrix_exc[1:frq$n[i], 1:frq$n[i]]) %*% nullmodel_exc$residuals[1:frq$n[i] + pos])
  pos = pos + frq$n[i]
}

ResidMat.exc = LongPheno %>%
  mutate(Resid = modelreisd_exc) %>%
  group_by(IID) %>% 
  summarize(Resid = sum(Resid)) %>%
  rename(SubjID = IID)

summary(ResidMat.exc$Resid)
#    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
# -15.606  -3.199  -0.093  -0.010   3.008  16.631

ResidMat.exc = ResidMat.exc %>% mutate(Resid = scale(Resid)) # if sum(Resid) != 0, we should rescale it! This often happens in real data analysis.

ResidMatFile = system.file("extdata", "ResidMat.txt", package = "GRAB")
data.table::fwrite(ResidMat.exc, file = ResidMatFile, row.names = FALSE, col.names = TRUE)
```

## Autoregressive working correlation structure

```
library(dplyr)
library(tidyr)
library(geepack)

# we must keep the order of subjects in LongPheno and frq the same!
LongPheno = LongPheno %>% arrange(IID) # We highly recommend sorting by IID first.

frq = LongPheno %>% group_by(IID) %>% summarize(n = n())
LongPheno = LongPheno %>% mutate(pseudoIID = rep(1:nrow(frq), times = frq$n))

nullmodel_ar1 = geeglm(LongPheno ~ AGE + GENDER, data = LongPheno, id = pseudoIID, corstr = "ar1") # for autoregressive working correlation structure

summary(nullmodel_ar1)$coefficients
#             Estimate Std.err   Wald  Pr(>|W|)
# (Intercept)  36.2439  1.1755 950.71 0.000e+00
# AGE          -0.3079  0.0235 171.64 0.000e+00
# GENDER        0.7143  0.0883  65.44 5.551e-16
```

### Calculate working correlation matrix

```
working.matrix_ar1 = toeplitz(1 * nullmodel_ar1$geese$alpha^(0:(max(frq$n)-1))) # for autoregressive working correlation structure
print(working.matrix_ar1[1:4,1:4])
#        [,1]   [,2]   [,3]   [,4]
# [1,] 1.0000 0.4982 0.2482 0.1236
# [2,] 0.4982 1.0000 0.4982 0.2482
# [3,] 0.2482 0.4982 1.0000 0.4982
# [4,] 0.1236 0.2482 0.4982 1.0000
```

## Obtain model residuals

```
modelreisd_ar1 = c()
pos = 0
for(i in 1:nrow(frq))
{
  modelreisd_ar1 = c(modelreisd_ar1, solve(working.matrix_ar1[1:frq$n[i], 1:frq$n[i]]) %*% nullmodel_ar1$residuals[1:frq$n[i] + pos])
  pos = pos + frq$n[i]
}

ResidMat.ar1 = LongPheno %>%
  mutate(Resid = modelreisd_ar1) %>%
  group_by(IID) %>% 
  summarize(Resid = sum(Resid)) %>%
  rename(SubjID = IID)

summary(ResidMat.ar1$Resid)
#    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
# -18.786  -3.637  -0.085  -0.031   3.543  18.491 

ResidMat.ar1 = ResidMat.ar1 %>% mutate(Resid = scale(Resid)) # if sum(Resid) != 0, we should rescale it! This often happens in real data analysis.

ResidMatFile = system.file("extdata", "ResidMat.txt", package = "GRAB")
data.table::fwrite(ResidMat.ar1, file = ResidMatFile, row.names = FALSE, col.names = TRUE)
```

> **Note**  
> - If the sum of model residuals is not zero, please rescale them.
> - The column names of <code style="color : fuchsia">ResidMat</code> must be exactly <code style="color : fuchsia">SubjID</code> in the first column and <code style="color : fuchsia">Resid</code> in the second column.
> - Each subject should match its corresponding residual.
