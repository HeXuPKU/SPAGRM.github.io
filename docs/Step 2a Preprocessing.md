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

# Pre-calculation of the joint distribution of genotypes

We can use `SPAGRM.NullModel` function in `GRAB` package to pre-calculate the joint distribution of genotypes. This function requires products of step 0 and 1.

## Prepare input files

`SPAGRM.NullModel` needs the `SparseGRMFile` and `PairwiseIBDFile` from step 0, and `ResidMatFile` from step 1. `SparseGRMFile` and `PairwiseIBDFile` only need to be calculated once for a certain cohort. `ResidMatFile` has the same format regardless of various phenotypes and statistical models.

```
# Load in the filepath
library(GRAB)
SparseGRMFile = system.file("SparseGRM", "SparseGRM.txt", package = "GRAB")
PairwiseIBDFile = system.file("PairwiseIBD", "PairwiseIBD.txt", package = "GRAB")
ResidMatFile = system.file("extdata", "ResidMat.txt", package = "GRAB")
```

## Run `SPAGRM.NullModel` to pre-calculate genotype distributions

```
SPAGRM.NullModel(ResidMatFile,
                 SparseGRMFile,
                 PairwiseIBDFile，
                 control = list(MaxQuantile = 0.75,
                                MinQuantile = 0.25,
                                OutlierRatio = 1.5,
                                ControlOutlier = TRUE,
                                MaxNuminFam = 5,
                                MAF_interval = c(0.0001, 0.0005, 0.001, 0.005, 0.01, 0.05, 0.1, 0.2, 0.3, 0.4, 0.5)))
```

- ResidMatFile: required. A path to the ResidMat file.
- SparseGRMFile: required. A path to the sparse GRM file.
- PairwiseIBDFile: required. A path to the pairwise IBD file.
- control: optional. `control` is to specify a list of parameters for controlling the association testing process.
  - MaxQuantile/MinQuantile/OutlierRatio: we use these three parameters to divide model residuals into outliers and non-outliers. The interquartile range (IQR) is defined as $MaxQuantile - MinQuantile$; $[MinQuantile - OutlierRatio \times IQR, MaxQuantile + OutlierRatio \times IQR]$ divides model residuals into non-outliers and outliers.
    - MaxQuantile: optional. Upper quantile, ranging from 0 to 1. (default=0.75)
    - MinQuantile: optional. Lower quantile, ranging from 0 to 1. (default=0.25)
    - OutlierRatio: optional. Multiples of IQR, more than 0. (default=1.5)
  - ControlOutlier: optional. If TRUE, we will control the proportion of outliers $\leq$ 10% to remain computational efficiency. XXXXXXXXXX (default=TRUE)
  - MaxNuminFam： optional. Control the family size $\leq$ `MaxNuminFam` to remain computational efficiency. (default=5)
  - MAF_interval: optional. we divide the minor allele frequency (MAF) region into several intervals and pre-calculate joint distributions of genotypes at each MAF cutoff.