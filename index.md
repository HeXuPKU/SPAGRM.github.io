---
layout: default
title: Overview
nav_order: 1
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
permalink: /
---

# **Documentation for SPA<sub>GRM</sub>**

SPA<sub>GRM</sub> is now implemented in the [GRAB package](https://wenjianbi.github.io/grab.github.io/). Please click here for download.

SPA<sub>GRM</sub> is a scalable, accurate, and universal analysis framework to control for sample relatedness in large-scale genome-wide association studies (GWAS). In the paper **A scalable, accurate, and universal analysis framework to control for sample relatedness in large-scale genome-wide association studies and its application to 79 longitudinal traits in UK Biobank (to be updated)**, we applied SPA<sub>GRM</sub> to analyze 79 longitudinal traits extracted from UK Biobank primary care data. As a universal analysis framework, we also evaluated SPA<sub>GRM</sub>'s performance in quantitative and binary trait analysis. 

Like many other popular methods, such as [BOLT-LMM](https://alkesgroup.broadinstitute.org/BOLT-LMM/BOLT-LMM_manual.html), [SAIGE](https://saigegit.github.io/SAIGE-doc/), [fastGWA](https://yanglab.westlake.edu.cn/software/gcta/#Overview), [REGENIE](https://rgcgithub.github.io/regenie/), [GATE](https://github.com/weizhou0/GATE), and [POLMM](https://github.com/WenjianBI/POLMM), SPA<sub>GRM</sub> is also a two-step method to control for sample relatedness in large-scale cohort. It consists of:

- In step 1, SPA<sub>GRM</sub> fits a null model to adjust for the effect of covariates on phenotypes and calculate model residuals. It is optional, rather than required, to incorporate the random effect into null model fitting to characterize the sample relatedness. 

- In step 2, SPA<sub>GRM</sub> associates the trait of interest to a single genetic variant and obtain GWAS results by a retrospective strategy: that is treating the genotypes as random variables. Unlike regular retrospective methods which solely rely on the genetic relationship matrix (GRM), SPA<sub>GRM</sub> calculates identity by descent (IBD)-sharing probabilities and then employs the Chow-Liu algorithm to approximate the joint distribution of genotypes. Saddlepoint approximation is applied in SPA<sub>GRM</sub> that can greatly increase the accuracy to analyze low-frequency and rare variants, especially if the phenotypic distribution is unbalanced. 

- To avoid redundant computations in step 2, SPA<sub>GRM</sub> uses the IBD-sharing probabilities and Chow-Liu algorithm to approximate the discrete joint distribution of genotype vectors in advance (illustrated as step 0). 

![plot](https://raw.githubusercontent.com/Fantasy-XuHe/SPAGRM.github.io/main/docs/assets/images/workfolw%20of%20SPAGRM.png)

See **A scalable, accurate, and universal analysis framework to control for sample relatedness in large-scale genome-wide association studies and its application to 79 longitudinal traits in UK Biobank (to be updated)** for more details about the workflow of SPA<sub>GRM</sub>.
