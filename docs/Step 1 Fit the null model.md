---
layout: default
title: Step 1 Fit the null model
nav_order: 3
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
has_children: true
has_toc: true
---

# Fit the null model

SPA<sub>GRM</sub> is designed as a universal GWAS analysis framework for various complex traits, such as longitudinal, quantitative, binary traits, and more. 

For SPA<sub>GRM</sub>, it is optional, rather than required, to incorporate a random effect to characterize sample relatedness. Therefore, SPA<sub>GRM</sub> is particularly suitable for the analysis of complex traits with complicated structure, and the application of complex statistical models.

To conduct a GWAS analysis containing related subjects, users only need to fit the null model only once with/without incorporating random effcts and obtain model residuals defined as the gradient of the likelihood function. We will latter show you how to implement it through longitudinal, quantitative, and binary trait analysis.

> **General pipeline to fit the null model**  
> 1. Prepare the data  
> - Prepare the phenotype and covariates. Genotype file is required if you fit a mixed model containing a random effect.  
> - It's recommended to do the quality control for the phenotype, espically longitudinal traits.  
> - The samples in PairwiseIBDFile should be a subset of the samples in SparseGRMFile. An error will occur if PairwiseIBDFile contains samples that is not in the corresponding SparseGRMFile in latter analysis!  
> - If you restrict SPA<sub>GRM</sub> to analyze unrelated subjects, then SparseGRMFile and PairwiseIBDFile are also required. SparseGRMFile can consist of a table where `ID1` is equal to `ID2`, and Value is 1 in each line. PairwiseIBDFile can consist of an empty table with exactly column names of `ID1`, `ID2`, `pa`, `pb`, `pc`.