---
layout: default
title: Step 2 Conduct genome-wide association studies
nav_order: 4
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
has_children: true
has_toc: true
---

# Genome-wide association studies

We have demonstrated in step 1 that model residuals share the same format for a wide range of phenotypes and statistical models. Therefore, SPA<sub>GRM</sub> is particularly suitable for the analysis of complex traits with complicated structure, and the application of complex statistical models.

SPA<sub>GRM</sub> utilizes precise estimation of joint genotype distributions to approximate the distribution of score statistics and then calibrate p values. To avoid redundant computations in step 2, SPA<sub>GRM</sub>  approximates the discrete joint distribution of genotype vectors in advance. Then SPA<sub>GRM</sub> screen whole genome for marker-level association analysis. 

To remain scalable for large-scale biobank-based GWAS, SPA<sub>GRM</sub> employs several strategies to enhance computational efficiency, including:

- **Pre-calculation of the joint distribution of genotypes**
  - We use a grid idea to divide the minor allele frequency (MAF) region into several intervals. We first calculate and store the discrete joint distribution of genotypes at each MAF cutoff. Then, we use a linear interpolation to approximate the discrete joint distribution of genotypes if the MAF falls within any of the specified intervals in step 2 when needed.

- **Family size reduction**
  - The discrete joint distribution of genotypes increases exponentially with the family size. We restrict the family size below a pre-given cutoff (e.g. 5). We use a greedy strategy by including both model residuals and kingship coefficients to reduce the family size while remaining the largest variance of the original pedigree.

- **Fast version of saddlepoint approximation**
  - We adopt the basic idea of fastSPA to employ a partially normal distribution approximation. We use the interquartile range (IQR) to divide model residuals into outliers and non-outliers. For outliers, family size reduction is applied and more precise estimation of joint genotype distributions is required; for non-outlier, normal distribution approximation is applied.
  - A hybrid strategy of normal distribution approximation and saddlepoint approximation (SPA) is also applied in SPA<sub>GRM</sub>. If observed score statistic is far away from its estimated standard error, SPA is applied to calibrate the p value; otherwise, the normal distribution approximation is used. The default cutoff is 2 in SPA<sub>GRM</sub>.

- **Variance ratio adjustment for SPA**
  - Pre-calculation of the joint distribution of genotypes and family size reduction strategies may slightly compromise the accuracy of SPA. Thus, we apply a variance ratio adjustment for SPA to ensure the variance from SPA is accurate.