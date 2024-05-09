---
layout: default
title: Generalized estimation equations
nav_order: 2
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
parent: Longitudinal traits
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

We use local real data to display how to fit generalized estimation equations with different working correlation structures, because simulated longitudinal data are sensitive to working correlation structures.

```
LongPheno = data.table::fread("../XXX.txt")
print(LongPheno)
#           IID        AGE GENDER   LongPheno
#    1: 1004781 -0.1664630      1 -0.07250354
#    2: 1004781  0.2348185      1  1.02946310
#    3: 1004781  0.3574603      1  1.01024275
#    4: 1004781  0.4724219      1  1.02946310
#    5: 1004781  0.5535525      1  0.78600535
#   ---
# 8649: 6016004 -1.2958463      1 -0.40565625
# 8650: 6016004 -1.2257652      1 -0.77084287
# 8651: 6016004 -1.0657243      1 -0.57223260
# 8652: 6016004 -0.9316269      1 -0.55301225
# 8653: 6016004 -0.7729337      1 -0.78365643
```

## Fit the null model

```
library(dplyr)
library(tidyr)
library(geepack)

# we must keep the order of subjects in LongPheno and frq the same!
LongPheno = LongPheno %>% arrange(IID)
frq = LongPheno %>% group_by(IID) %>% summarize(n = n())

nullmodel_exc = geeglm(LongPheno ~ AGE + GENDER, data = LongPheno, id = IID, corstr = "exc") # for exchangeable working correlation structure

nullmodel_ar1 = geeglm(LongPheno ~ AGE + GENDER, data = LongPheno, id = IID, corstr = "ar1") # for autoregressive working correlation structure
```

### Calculate working correlation matrix

```
working.matrix_exc = matrix(nullmodel_exc$geese$alpha, nrow = max(frq$n), ncol = max(frq$n)); diag(working.matrix_exc) = 1 # for exchangeable working correlation structure
print(working.matrix_exc[1:4,1:4])
#          [,1]     [,2]     [,3]     [,4]
# [1,] 1.000000 1.034182 1.034182 1.034182
# [2,] 1.034182 1.000000 1.034182 1.034182
# [3,] 1.034182 1.034182 1.000000 1.034182
# [4,] 1.034182 1.034182 1.034182 1.000000

working.matrix_ar1 = toeplitz(1 * nullmodel_ar1$geese$alpha^(0:(max(frq$n)-1))) # for autoregressive working correlation structure
print(working.matrix_ar1[1:4,1:4])
#          [,1]     [,2]     [,3]     [,4]
# [1,] 1.000000 1.006522 1.013086 1.019693
# [2,] 1.006522 1.000000 1.006522 1.013086
# [3,] 1.013086 1.006522 1.000000 1.006522
# [4,] 1.019693 1.013086 1.006522 1.000000
```

## Obtain model residuals

```
# for exchangeable working correlation structure
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
# -3.3556 -0.3262  0.1587  0.1336  0.6741  2.0067

ResidMat.exc = ResidMat.exc %>% mutate(Resid = scale(Resid)) # if sum(Resid) != 0, we should rescale it!
```

```
# for autoregressive working correlation structure
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
# -3.4018 -0.3660  0.1135  0.1100  0.6058  1.9233

ResidMat.ar1 = ResidMat.ar1 %>% mutate(Resid = scale(Resid)) # if sum(Resid) != 0, we should rescale it!
```

> **Note**  
> - If sum of model residuals is not zero, please rescale 
> - The column names of <code style="color : fuchsia">ResidMat</code> must be exactly <code style="color : fuchsia">SubjID</code> in the first column and <code style="color : fuchsia">Resid</code> in the second column.
> - Each subject should match its corresponding residual.
