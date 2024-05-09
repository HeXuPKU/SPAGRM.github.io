---
layout: default
title: a). Pre-estimate joint distributions of genotypes
nav_order: 1
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
parent: Step 2 Conduct genome-wide association studies
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

# **Pre-calculate joint distributions of genotypes**

We can use <code style="color : darkorange">SPAGRM.NullModel</code> function in [**GRAB**](https://wenjianbi.github.io/grab.github.io/) package to pre-calculate the joint distribution of genotypes. This function requires products of step 0 and 1.

## Prepare input files

<code style="color : darkorange">SPAGRM.NullModel</code> needs the <code style="color : fuchsia">SparseGRMFile</code> and <code style="color : fuchsia">PairwiseIBDFile</code> from step 0, and <code style="color : fuchsia">ResidMatFile</code> from step 1. <code style="color : fuchsia">SparseGRMFile</code> and <code style="color : fuchsia">PairwiseIBDFile</code> only need to be calculated once for a certain cohort. <code style="color : fuchsia">ResidMatFile</code> has the same format regardless of various phenotypes and statistical models.

## Run <code style="color : darkorange">SPAGRM.NullModel</code> to pre-calculate genotype distributions

```
SPAGRM.NullModel(ResidMatFile,
                 SparseGRMFile,
                 PairwiseIBDFile,
                 control = list(MaxQuantile = 0.75,
                                MinQuantile = 0.25,
                                OutlierRatio = 1.5,
                                ControlOutlier = TRUE,
                                MaxNuminFam = 5,
                                MAF_interval = c(0.0001, 0.0005, 0.001, 0.005, 0.01, 0.05, 0.1, 0.2, 0.3, 0.4, 0.5)))
```

- <code style="color : darkred">ResidMatFile</code>: required. A path to the ResidMat file.
- <code style="color : darkred">SparseGRMFile</code>: required. A path to the sparse GRM file.
- <code style="color : darkred">PairwiseIBDFile</code>: required. A path to the pairwise IBD file.
- <code style="color : darkred">control</code>: optional. <code style="color : darkred">control</code> is to specify a list of parameters for controlling the association testing process.
  - <code style="color : darkred">MaxQuantile</code>/<code style="color : darkred">MinQuantile</code>/<code style="color : darkred">OutlierRatio</code>: we use these three parameters to divide model residuals into outliers and non-outliers. The interquartile range (IQR) is defined as $MaxQuantile - MinQuantile$; The interval of outlier detection is defined as $[MinQuantile - OutlierRatio \times IQR, MaxQuantile + OutlierRatio \times IQR]$ which divides model residuals into non-outliers and outliers.
    - <code style="color : darkred">MaxQuantile</code>: optional. Upper quantile, ranging from 0 to 1. (default=0.75)
    - <code style="color : darkred">MinQuantile</code>: optional. Lower quantile, ranging from 0 to 1. (default=0.25)
    - <code style="color : darkred">OutlierRatio</code>: optional. Multiples of IQR, more than 0. (default=1.5)
  - <code style="color : darkred">ControlOutlier</code>: optional. If TRUE, we will control the proportion of outliers $\leq$ 10% to remain computational efficiency. (default=TRUE)
  - <code style="color : darkred">MaxNuminFam</code>： optional. Control the family size $\leq$ <code style="color : darkred">MaxQuantile</code> to remain computational efficiency. (default=5)
  - <code style="color : darkred">MAF_interval</code>: optional. we divide the minor allele frequency (MAF) region into several intervals and pre-calculate joint distributions of genotypes at each MAF cutoff.

**Example:**

```
# Load in the filepath
library(GRAB)
SparseGRMFile = system.file("SparseGRM", "SparseGRM.txt", package = "GRAB")
PairwiseIBDFile = system.file("PairwiseIBD", "PairwiseIBD.txt", package = "GRAB")
ResidMatFile = system.file("extdata", "ResidMat.txt", package = "GRAB")
```

```
obj.SPAGRM = SPAGRM.NullModel(ResidMatFile = ResidMatFile, 
                              SparseGRMFile = SparseGRMFile, 
                              PairwiseIBDFile = PairwiseIBDFile,
                              control = list(ControlOutlier = FALSE))
# cutoffVec:	 -1.930831 1.920325 
# Outliers information is as below
#       SubjID     Resid Outlier
#  1: Subj-217 -2.368746    TRUE
#  2:    f11_6 -2.315659    TRUE
#  3: Subj-133 -2.179490    TRUE
#  4:    f28_9 -2.135892    TRUE
#  5:    f24_1  1.948334    TRUE
#  6:  Subj-45  1.953527    TRUE
#  7:    f15_7  2.015207    TRUE
#  8: Subj-280  2.044026    TRUE
#  9: Subj-487  2.131917    TRUE
# 10:   Subj-8  2.300450    TRUE
# 11: Subj-263  2.521091    TRUE
# 12:     f5_3  2.579543    TRUE
# Start process the related residual information.
# Start process the Chow-Liu tree.
# Completed processing the CLT for families with outliers:	 0 ,  5 / 50 
```

```
# Save the objSPAGRM product
objSPAGRMFile = system.file("results", "objSPAGRMFile.RData", package = "GRAB")
save(obj.SPAGRM, file = objSPAGRMFile)
```

> **Note**  
> - Users should check model residuals in <code style="color : fuchsia">ResidMatFile</code> before running <code style="color : darkorange">SPAGRM.NullModel</code>, including: 
>   1. If model residuals are all zeros or a constant number, please check the null model fitting;
>   2. If sum of model residuals is **not** equal to zero, please rescale them to make sure the sum of model residuals is equal to zero.
> - If <code style="color : darkred">ControlOutlier</code> is set TRUE, we will automatically alter <code style="color : darkred">OutlierRatio</code> to make sure that the proportion of outliers $\leq$ 10%. But we suggest setting <code style="color : darkred">ControlOutlier</code> to FALSE when analyzing ultra-rare variants and/or phenotypic distribution is extremely unbalanced.
> - <code style="color : darkred">MaxNuminFam</code> is set to 5 by default, in order to improve computing efficiency while maintaining accuracy. Our real data analysis indicates that increasing <code style="color : darkred">MaxNuminFam</code> has **no** impact on the association results, but the run time will increase exponentially.
