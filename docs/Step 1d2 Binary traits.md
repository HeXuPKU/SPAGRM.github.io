---
layout: default
title: Binary traits
nav_order: 2
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
parent: Other trait types
grand_parent: Step 1 Fit the null model
has_children: false
---

# **Binary traits**

We can use a logistic regression to fit the null model for binary traits. That is ignoring random effects in mixed models. Or we can use a generalized linear mixed model to fit the null model and obtain residuals.

## Logistic regression

Please run the following code in R

### Read in example data sets

```
library(GRAB)
PhenoFile = system.file("extdata", "simuPHENO.txt", package = "GRAB")
PhenoData = data.table::fread(PhenoFile)
```

### Fit the null model and obtain model residuals

```
nullmodel = glm(BinaryPheno ~ AGE + GENDER, data = PhenoData, family = binomial(link = "logit"))

ResidMat = data.frame(SubjID = PhenoData$IID,
                      Resid = nullmodel$y - nullmodel$fitted.values)
print(ResidMat)
#         SubjID       Resid
#    1:   Subj-1 -0.05951686
#    2:  Subj-10 -0.13514735
#    3: Subj-100 -0.04760708
#    4: Subj-101 -0.11724359
#    5: Subj-102 -0.05894260
#   ---                     
#  996:     f9_5 -0.04974518
#  997:     f9_6 -0.03809952
#  998:     f9_7 -0.26281321
#  999:     f9_8 -0.03935781
# 1000:     f9_9 -0.18433960

ResidMatFile = system.file("extdata", "ResidMat.txt", package = "GRAB")
data.table::fwrite(ResidMat, file = ResidMatFile, row.names = FALSE, col.names = TRUE, quote = FALSE, sep = "\t")
```

## Generalized linear mixed model

In practice， we use SAIGE to fit the null generalized linear mixed model. Here is an example code showing how to use docker to run SAIGE.

### Fit the null mixed model

```
docker run -v /path/to/your/sparseGRM/:/sparseGRMDir \
  -v /path/to/your/pheno/:/phenoDir \
  -v /path/to/your/geno/:/genoDir \
  -v /path/to/your/output/:/outputDir \
  wzhou88/saige:1.1.9 step1_fitNULLGLMM.R \
  --sparseGRMFile=/sparseGRMDir/SAIGE_SparseGRM.txt \
  --sparseGRMSampleIDFile=/sparseGRMDir/SAIGE_SparseGRMSampleID.txt \
  --useSparseGRMtoFitNULL=TRUE \
  --outputPrefix=/outputDir/SAIGE_step1 \
  --plinkFile=/genoDir/simuPLINK \
  --phenoFile=/phenoDir/simuPHENO.txt \
  --traitType=binary \
  --phenoCol=BinaryPheno \
  --covarColList=AGE,GENDER \
  --sampleIDColinphenoFile=IID \
  --isCateVarianceRatio=FALSE \
  --IsOverwriteVarianceRatioFile=TRUE
```

See [SAIGE documentation](https://saigegit.github.io/SAIGE-doc/) for more details about these commands.

### Load step 1 product and obtain model residuals

Please run the following code in R

```
load("/path/to/your/output/SAIGE_step1.rda") # please verify your filepath.

ResidMat = data.frame(SubjID = modglmm$sampleID,
                      Resid = modglmm$residuals)
print(ResidMat)
#         SubjID       Resid
#    1:     f1_1 -0.10972010
#    2:     f1_2 -0.10444114
#    3:     f1_3  0.93103287
#    4:     f1_4 -0.06519121
#    5:     f1_5 -0.07512193
#   ---
#  996: Subj-496 -0.07066911
#  997: Subj-497 -0.03410188
#  998: Subj-498 -0.08215333
#  999: Subj-499 -0.07556645
# 1000: Subj-500 -0.11477622

ResidMatFile = system.file("extdata", "ResidMat.txt", package = "GRAB")
data.table::fwrite(ResidMat, file = ResidMatFile, row.names = FALSE, col.names = TRUE, quote = FALSE, sep = "\t")
```

> **Note**  
> - <code style="color : fuchsia">ResidMatFile</code> has the same format regardless of testing $\beta$<sub>g</sub> or $\tau$<sub>g</sub>.
> - The column names of <code style="color : fuchsia">ResidMatFile</code> must be exactly <code style="color : fuchsia">SubjID</code> in the first column and <code style="color : fuchsia">Resid</code> in the second column.
> - Each subject should match its corresponding residual.