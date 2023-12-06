---
layout: default
title: Pairwise IBD
nav_order: 2
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
parent: Step 0 Calculate GRM and IBD files
has_children: false
---

# Make a Pairwise IBD Probability file

We can use  `getPairwiseIBD` function in `GRAB` package to construct a pairwise IBD probability file. Pairwise IBD Probability file is only required for SPA<sub>GRM</sub>, so we introduce it here.

## Prepare PLINK binary files

Similar to calculating the sparse GRM, PLINK binary files with high-quality genotyped variants are required to make a pairwise IBD probability file. We recommend using the same PLINK binary files to calculate the sparse GRM file and then the pairwise IBD file.

In addition to the `XXX.bed`, `XXX.bim`, and `XXX.fam` files, `XXX.frq` file is also required for `getPairwiseIBD` function! Users can use PLINK command to generate the corresponding `XXX.frq` file for the existing PLINK binary files:

```
--freq --out XXX
```

Before next step, you should have below PLINK binary files:
```
XXX.bed,   XXX.bim,   XXX.fam (XXX should be the same prefix of the PLINK binary files)
XXX.frq (frq file doesn't necessarily need to have the same prefix as bed, bim, and fam files)
```

## Run `getPairwiseIBD` to make a Pairwise IBD Probability file

```
getPairwiseIBD(PlinkFile,
               SparseGRMFile,
               PairwiseIBDFile,
               frqFile = NULL,
               tempDir = NULL,
               maxSampleNums = 2500,
               minMafIBD = 0.01,
               rm.tempFile = FALSE)
```

- PlinkFile: required. A path to PLINK files (without file extensions of bed/bim/fam). It suggested be the same as used in `getSparseGRM` function.

- SparseGRMFile: required. A file path to the sparse GRM file.

- PairwiseIBDFile: required. A file path to store pairwise IBD-sharing probabilities.

- frqFile: A path to the frq file corresponding to PLINK binary files (default=NULL, means the same prefix as PLINK binary files)

- tempDir: optional. A path to store temp files generated in `getPairwiseIBD`. Default is system.file("SparseGRM", "temp", package = "GRAB"). check!!!!!!!!!

- maxSampleNums: optional. Maximal number of samples in each run to calculate their pairwise IBD-sharing probabilities. If number of genetic variants > 100K, `maxSampleNums` should < 2,500 to save momory usage. (default=2500)

- minMafIBD: optional. Minimal value of MAF cutoff to select markers (from PLINK files) to make pairwise IBD-sharing probabilities. (default=0.01)

- rm.tempFiles: optional. A logical value indicating if the temp files generated in `getPairwiseIBD` will be deleted. (default=FALSE)
