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


