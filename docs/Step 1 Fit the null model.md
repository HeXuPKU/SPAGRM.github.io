---
layout: default
title: Step 1 Fit the null model
nav_order: 3
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
has_children: true
has_toc: true
---

# **Fit the null model**

SPA<sub>GRM</sub> is a scalable and accurate analysis framework to control for sample relatedness for various complex traits, including multiple patterns for longitudinal traits (longitudinal mean, within-subject variability, and trand).

For SPA<sub>GRM</sub>, it is optional, rather than required, to incorporate a random effect to characterize sample relatedness. Therefore, SPA<sub>GRM</sub> is particularly suitable for the analysis of complex traits with complicated structure, and the application of complex statistical models.

To conduct a GWAS analysis containing related subjects, users only need to fit the null model only once with/without incorporating random effcts and obtain model residuals defined as the gradient of the likelihood function. We will latter show you how to implement it through longitudinal, quantitative, and binary trait analysis.

> **General pipeline to fit the null model**  
> 1. **Prepare the data**  
> - Prepare the phenotype and covariates. Genotype file or GRM file is required only if you fit a mixed model containing random effects to account for sample relatedness.  
> - It's recommended to perform quality control (QC) for longitudinal traits.  
> - Covariates can include age, gender, SNP-derived top principal components (PCs), Leave One Chromosome Out Polygenic Scores (LOCO-PGSs), and so on.  
> 2. **Choose a suitable statistical model**  
> - For example, we can use linear mixed models or generalized estimation equations for longitudinal trait analysis.
> - We can use linear/logistic regression methods for quantitative/binary trait analysis.
> - Users can try other statistical models for complex traits with more complicated structure if interested.
> 3. **Fit the null model and obtain model residuals**
> - The score statistics can be derived from the gradient of the likelihood function by fitting the null model. 
> - Generally, The score statistics have the consistent format of _S_ = _G<sup>T</sup>R_, where _G_ represents the genotype to be tested and _R_ is defined as the model residuals.
