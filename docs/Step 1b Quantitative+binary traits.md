---
layout: default
title: Quantitative/binary traits
nav_order: 2
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
parent: Step 1 Fit the null model
has_children: false
---

# Quantitative traits

We can use a linear regression to fit the null model for quantitative traits. Or we can use a linear mixed model to fit the null model and obtain residuals.

## Linear regression

Please run the following code in R

### Read in example data sets

```
library(GRAB)
PhenoFile = system.file("extdata", "simuPHENO.txt", package = "GRAB")
PhenoData = data.table::fread(PhenoFile)
print(PhenoData)
#            IID      AGE GENDER      eta       bVec BinaryPheno  QuantPheno 
#    1:   Subj-1 59.61118      0 29.59961 -0.2059781           0  0.27866626
#    2:  Subj-10 61.31636      0 29.32101 -1.3371678           0 -2.29732890
#    3: Subj-100 59.16625      0 29.90944  0.3263173           0 -2.48702515
#    4: Subj-101 60.18490      1 28.99876 -1.5936861           0 -3.40632756
#    5: Subj-102 58.76700      1 27.90020 -1.9832990           0 -3.44214439
#   ---                                                                     
#  996:     f9_5 59.25336      0 29.33119 -0.2954873           0 -0.91237106
#  997:     f9_6 57.90253      1 28.55622 -0.8950407           0 -2.50808150
#  998:     f9_7 62.04752      1 30.98239 -0.5413704           0 -0.38776894
#  999:     f9_8 58.79103      0 28.06333 -1.3321861           0 -0.02743997
# 1000:     f9_9 61.18766      1 30.14505 -0.9487810           0 -0.02288709
#       OrdinalPheno   SurvTime SurvEvent
#    1:            0 0.03245097         0
#    2:            0 0.19733412         0
#    3:            1 0.09451178         0
#    4:            0 0.10946053         0
#    5:            0 0.04882298         0
#   ---                                  
#  996:            2 0.20041554         0
#  997:            0 0.01220480         0
#  998:            2 0.04456741         0
#  999:            0 0.07052257         0
# 1000:            0 0.08657261         0
```


## Linear mixed model






# Binary traits

We can use a logistic regression to fit the null model for binary traits. Or we can use a generalized linear mixed model to fit the null model and obtain residuals.

## Logistic regression


## Generalized linear mixed model