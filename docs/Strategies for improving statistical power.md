---
layout: default
title: Strategies for improving statistical power
nav_order: 5
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
has_children: false
has_toc: false
---

# **Strategies for improving statistical power**

We propose several strategies to further improve statistical power of longitudinal trait analysis, summarized as follows:

| Strategies | Longitudinal mean | WS variability | Longitudinal trand |
|:----------:|:-----------------:|:--------------:|:------------------:|
| Rank-based inverse normal transformation | No impact | More powerful | No impact |
| Change correlation structure | Sometimes more powerful | Not evaluate | Not evaluate |
| Polygenic score (PGS) adjustment  | More powerful | No impact | No impact |

## Rank-based inverse normal transformation

Model residuals from fitted null model can be highly unbalanced, especially when testing within-subject (WS) variability. In this context, we find that using a rank-based inverse normal transformation (INT) for model residuals can greatly improve empirical power. We propose SPA<sub>GRM(INT)</sub> in which inverse normal transformed model residuals are taken as input, and SPA<sub>GRM(CCT)</sub> in which p values from SPA<sub>GRM</sub> and SPA<sub>GRM(INT)</sub> are combined via Cauchy combination test (CCT). 

### Prepare model residuals for SPA<sub>GRM(INT)</sub>

```
library(RNOmni)
ResidMatFile = system.file("extdata", "ResidMat.txt", package = "GRAB")
ResidMat = data.table::fread(ResidMatFile)
summary(ResidMat$Resid)
#     Min.  1st Qu.   Median     Mean  3rd Qu.     Max. 
# -2.36875 -0.48665 -0.01213  0.00000  0.47614  2.57954

ResidMat$Resid = RankNorm(ResidMat$Resid)
data.table::fwrite(ResidMat, file = ResidMatFile, row.names = FALSE, col.names = TRUE) # Then we can pass this file as the input of SPAGRM
summary(ResidMat$Resid)
#    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
# -3.2273 -0.6735  0.0000  0.0000  0.6735  3.2273 
```

## Change correlation structure

Repeated measurements within each subject can be correlated. Linear mixed effect models consider the correlation via the random effect (and random slope), while generalized estimation equations require pre-defined working correlation metrics (e.g, exchangeable and autoregressive working correlation structures). We find that these two methods are rubost against correlation structure missicification (i.e., well controlled type I error rates), while empirical power varies depending on the data generating mechanism. Since no methods are omnibus, indicating that different longitudinal traits could correspond to different architectures. We propose SPA<sub>GRM(CCT)</sub> that combines p values from SPA<sub>GRM</sub> using various working correlation structures. For preparing model residuals of GEE model with various correlation structures, user can refer to [Generalized estimation equations](https://hexupku.github.io/SPAGRM.github.io/docs/Step%201a2%20Generalized%20estimation%20equations.html). To combine p values via CCT, user can refer to [ACAT](https://github.com/yaowuliu/ACAT).

## Polygenic score (PGS) adjustment

Recent reports have shown that adjusting for PGSs can account for polygenic effects and increase statistical power. Similarly, SPA<sub>GRM</sub> can further gain statistical power through incorporating polygenic scores (PGSs) as covariates with fixed effects. We employ this idea to implement a two-stage strategy, SPAGRM-PGS:
 - In stage 1, we conduct the first round of GWAS via SPAGRM and then calculate the Leave One Chromosome Out (LOCO)-PGS based on the summary statistics. 
 - In stage 2, the LOCO-PGS is included as an additional covariate for a second round of GWAS via SPAGRM. 

This is a general strategy, and currently, we do not possess a function that can directly execute this functionality. In [SPA<sub>GRM</sub> reproducibility](https://github.com/HeXuPKU/SPAGRM/tree/main/real_data/5.PRS_adjustment%20(optional)), we displayed how to construct LOCO-PGSs via the clumping and thresholding (C+T) method based on summary statistics of SPA<sub>GRM</sub>. We plan to add a function to approximate marginal genetic effects to facilitate PGS construction.
