---
layout: default
title: Longitudinal trajectory
nav_order: 3
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
parent: Step 1 Fit the null model
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

# **Longitudinal trajectory**

We propose SAGELD, a method for gene-environment interaction analysis of longitudinal traits while controlling for sample relatedness. Consider a linear mixed model:

$$ y_{ij} = X_{ij}^T \beta + G_i \beta_g + (G_i \circ E_{ij}) \beta_{g\times e} + Z_{ij}^T \gamma_i + \varepsilon_{ij} $$ 

where $E$ is a time-varying variable (e.g., age or BMI) that changes within subjects. We primarily focus on testing $\beta_{g\times e}=0$.

SAGELD consists of two steps:

1) fit an LMM without any genetic variant (i.e., $H_c:\beta_g=\beta_{g\times e}=0$), and estimate model parameters and calculate residuals.

2) use a matrix projection strategy to construct test statistics and then apply SPA<sub>GRM</sub> to conduct GWAS while controlling for sample relatedness.

## Quick start for lme4

Please run the following code in R

### Display the example data set

```
frq = sample(1:10, 500, replace = TRUE)

LongPheno = data.frame(IID = rep(paste0("Subj-", 1:500), frq), AGE = rnorm(sum(frq)), GENDER = rep(rbinom(500, 1, 0.5), frq))

LongPheno$LongPheno = LongPheno$AGE + LongPheno$GENDER + 0.5 * rep(rnorm(500), frq) + 0.5 * rep(rnorm(500), frq) * LongPheno$AGE + rnorm(sum(frq))
print(LongPheno)
#            IID         AGE GENDER  LongPheno
#    1:   Subj-1  0.36854165      1  1.2758600
#    2:   Subj-2  0.66326627      0 -0.3935064
#    3:   Subj-2  0.39415976      0 -1.6903284
#    4:   Subj-2 -0.17028342      0  0.1731918
#    5:   Subj-2  0.74694481      0 -1.3294894
#   ---                                       
# 2759: Subj-500  0.68823610      1 -1.0359893
# 2760: Subj-500 -0.86605339      1  0.2060031
# 2761: Subj-500 -0.28382077      1  0.1007282
# 2762: Subj-500 -0.12656440      1  1.1798696
# 2763: Subj-500 -0.06535694      1  1.0822086
```

### Fit the null model

```
library(dplyr)
library(tidyr)
library(lme4)

nullmodel = lmer(LongPheno ~ AGE + GENDER + (AGE|IID), data = LongPheno)

summary(nullmodel)$coefficients
#               Estimate Std. Error    t value
# (Intercept) 0.05424788 0.07516685  0.7216995
# AGE         0.98810759 0.05436085 18.1768254
# GENDER      1.06136528 0.10528724 10.0806638
```

## **Pre-calculate joint distributions of genotypes**

We can use <code style="color : darkorange">SAGELD.NullModel</code> function in [**GRAB**](https://wenjianbi.github.io/grab.github.io/) package to pre-calculate the joint distribution of genotypes.

### Prepare input files

<code style="color : darkorange">SAGELD.NullModel</code> needs the <code style="color : fuchsia">SparseGRMFile</code> and <code style="color : fuchsia">PairwiseIBDFile</code> from step 0, and <code style="color : fuchsia">ResidMatFile</code> from step 1. <code style="color : fuchsia">SparseGRMFile</code> and <code style="color : fuchsia">PairwiseIBDFile</code> only need to be calculated once for a certain cohort. 

### Run <code style="color : darkorange">SAGELD.NullModel</code> to pre-calculate genotype distributions

```
SAGELD.NullModel(NullModel,
                 UsedMethod,
                 PlinkFile,
                 SparseGRMFile,
                 PairwiseIBDFile,
                 PvalueCutoff,
                 control = list())
```

- <code style="color : darkred">NullModel</code>: required. A fitted null model without any genetic variants.
- <code style="color : darkred">UsedMethod</code>: optional. "SAGELD" (default) or "GALLOP", decide which method to use.
- <code style="color : darkred">PlinkFile</code>: required. A path to the plink file containing 1,000-2,000 genetic variants to calculate the mean $\lambda$.
- <code style="color : darkred">SparseGRMFile</code>: required. A path to the sparse GRM file.
- <code style="color : darkred">PairwiseIBDFile</code>: required. A path to the pairwise IBD file.
- <code style="color : darkred">PvalueCutoff</code>: optional. A cutoff to decide use the mean $\lambda$ or marker level $\lambda$ (default is 0.001).
- <code style="color : darkred">control</code>: optional. <code style="color : darkred">control</code> is to specify a list of parameters for controlling the association testing process. See [here](https://hexupku.github.io/SPAGRM.github.io/docs/Step%202a%20Preprocessing.html) for more details.

**Example:**

```
# Load in the filepath
library(GRAB)
SparseGRMFile = system.file("SparseGRM", "SparseGRM.txt", package = "GRAB")
PairwiseIBDFile = system.file("PairwiseIBD", "PairwiseIBD.txt", package = "GRAB")
PlinkFile = system.file("extdata", "simuPLINK.bed", package = "GRAB")
PlinkFile = strsplit(PlinkFile,".bed")[[1]]
```

```
obj.SAGELD = SAGELD.NullModel(NullModel = nullmodel,
                              PlinkFile = PlinkFile, 
                              SparseGRMFile = SparseGRMFile, 
                              PairwiseIBDFile = PairwiseIBDFile)
# Process the null model product...
# Too many SNPs in the PLINK file! We randomly select 2000 SNPs.
# Reading bim file:	 C:/Users/XH/AppData/Local/R/win-library/4.2/GRAB/extdata/simuPLINK.bim 
# Reading fam file:	 C:/Users/XH/AppData/Local/R/win-library/4.2/GRAB/extdata/simuPLINK.fam 
# setting PLINK object in CPP....
# Reading bim file....
# Reading fam file:	C:/Users/XH/AppData/Local/R/win-library/4.2/GRAB/extdata/simuPLINK.fam
# m_N:	1000
# Setting position of samples in PLINK files....
# posSampleInPlink:	501 510 600
# SampleInModel:	Subj-1 Subj-10 Subj-100
# SampleInPlink:	f1_1 f1_2 f1_3
# Number of samples:	500
# Based on the 'GenoFile' and 'GenoFileIndex', PLINK format is used for genotype data.
# Number of Samples:	 500 
# Number of Markers:	 2000 
# Complete the genotype reading.
# Mean lambda:	 0.001080904 
# cutoffVec:	 -2.902171 2.960981 
# ControlOutlier = TRUE (default) to keep the outliers < 5%;
# Set ControlOutlier = FALSE for higher accuracy.
# Outliers information is as below
# A tibble: 24 x 3
#    SubjID   Resid Outlier
# 1 Subj-304 -4.91 TRUE   
#  2 Subj-474 -4.10 TRUE   
#  3 Subj-367 -4.05 TRUE   
#  4 Subj-242 -4.01 TRUE   
#  5 Subj-109 -3.69 TRUE   
#  6 Subj-253 -3.67 TRUE   
#  7 Subj-458 -3.62 TRUE   
#  8 Subj-399 -3.50 TRUE   
#  9 Subj-227 -3.41 TRUE   
# 10 Subj-300 -3.38 TRUE   
# i 14 more rows
# i Use `print(n = ...)` to see more rows
```

## **Conduct genome-wide association studies**

We can use <code style="color : darkorange">GRAB.marker</code> function in [**GRAB**](https://wenjianbi.github.io/grab.github.io/) package to conduct marker-level genome-wide association studies. See [here](https://hexupku.github.io/SPAGRM.github.io/docs/Step%202b%20Testing.html) for more details.

### Run <code style="color : darkorange">GRAB.marker</code> to conduct genome-wide association studies

```
GRAB.Marker(objNull,
            GenoFile,
            GenoFileIndex = NULL,
            OutputFile,
            OutputFileIndex = NULL,
            control = NULL)
```

```
# run the GRAB.Marker function
GenoFile = system.file("extdata", "simuPLINK.bed", package = "GRAB")
OutputDir = system.file("results", package = "GRAB")
OutputFile = paste0(OutputDir, "/simuOutput.txt")

GRAB.Marker(objNull = obj.SAGELD,
            GenoFile = GenoFile,
            OutputFile = OutputFile)
```

**Value**

The analysis results are written in a file of <code style="color : fuchsia">OutputFile</code>. Most columns are common as in [SPA<sub>GRM</sub>](https://hexupku.github.io/SPAGRM.github.io/docs/Step%202b%20Testing.html), with minor difference：

- <code style="color : fuchsia">zScore_G</code>: standardized score statistics for SNP main effects, usually follows a standard normal distribution.
- <code style="color : fuchsia">zScore_GxE</code>: standardized score statistics for SNP GxE effects, usually follows a standard normal distribution.
- <code style="color : fuchsia">Pvalue_G</code>: association test p-value for each marker's main effect.
- <code style="color : fuchsia">Pvalue_GxE</code>: association test p-value for each marker's GxE effect.

```
results = data.table::fread(OutputFile)
print(results)
#           Marker        Info    AltFreq AltCounts MissingRate Method   zScore_G  zScore_GxE    Pvalue_G
#     1:     SNP_1     1:1:G:A 0.39501040       380       0.038 SAGELD -1.1243540 -0.51412937 0.260862875
#     2:     SNP_2     1:2:G:A 0.46234310       442       0.044 SAGELD -0.3090360  0.26472862 0.757294124
#     3:     SNP_3     1:3:G:A 0.40400844       383       0.052 SAGELD -1.9107842 -0.03805962 0.056032323
#     4:     SNP_4     1:4:G:A 0.46788009       437       0.066 SAGELD -0.1615199  0.08725930 0.871683968
#     5:     SNP_5     1:5:G:A 0.25630252       244       0.048 SAGELD  0.1972576 -1.89378144 0.843625952
#    ---                                                                                                 
#  9996:  SNP_9996  1:9996:G:A 0.33954451       328       0.034 SAGELD  0.5786508 -0.16980515 0.562824815
#  9997:  SNP_9997  1:9997:G:A 0.26415094       252       0.046 SAGELD  3.0584812 -0.80338128 0.002224621
#  9998:  SNP_9998  1:9998:G:A 0.06526316        62       0.050 SAGELD  1.2286993  0.79542958 0.219184557
#  9999:  SNP_9999  1:9999:G:A 0.24838013       230       0.074 SAGELD  0.5652946  1.08282372 0.571873424
# 10000: SNP_10000 1:10000:G:A 0.16315789       155       0.050 SAGELD  0.2971961  0.28412809 0.766316834
#        Pvalue_GxE   hwepval
#     1: 0.60716154 0.5603599
#     2: 0.79121853 0.5118404
#     3: 0.96964014 0.9440939
#     4: 0.93046540 0.9213428
#     5: 0.05825403 0.9484430
#    ---                     
#  9996: 0.86516338 0.3813232
#  9997: 0.42175439 0.4393182
#  9998: 0.42636366 0.4625118
#  9999: 0.27888668 0.8883984
# 10000: 0.77631222 0.1433092
```

> **Note**  
> - Adding a random slope related to the environmental exposure in the LMM is required to control for heterogeneity introduced by the environmental variable.
> - If [lme4](https://cran.r-project.org/web/packages/lme4/index.html) fails due to sparse longitudinal data, you can use glmmTMB. For example,
```
nullmodel = glmmTMB::glmmTMB(LongPheno ~ AGE + GENDER + (AGE|IID), data = LongPheno)
```
> - When using <code style="color : darkorange">UsedMethod = "GALLOP"</code> in <code style="color : darkorange">SAGELD.NullModel</code> function, e.g.,
```
obj.GALLOP = SAGELD.NullModel(NullModel = nullmodel,
                              UsedMethod = "GALLOP")
```
>> other parameters will be out of use, the method will perform GALLOP algorithm, and the GWAS results will have two additional columns: 
>> - <code style="color : fuchsia">Beta_G</code>: effect size estimates for SNP main effects.
>> - <code style="color : fuchsia">zScore_GxE</code>: effect size estimates for SNP GxE effects.