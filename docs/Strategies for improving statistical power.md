---
layout: default
title: Strategies for improving statistical power
nav_order: 5
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
has_children: false
has_toc: false
---

# Strategies for improving statistical power

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



## Polygenic score (PGS) adjustment



