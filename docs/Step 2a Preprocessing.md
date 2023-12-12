---
layout: default
title: a). Pre-estimate joint distributions of genotypes
nav_order: 1
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
parent: Step 2 Conduct genome-wide association studies
has_children: false
---

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

## Run `SPAGRM.NullModel` to pre-calculate joint distributions of genotypes