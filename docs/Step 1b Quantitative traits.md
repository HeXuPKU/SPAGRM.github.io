---
layout: default
title: Quantitative traits
nav_order: 2
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
parent: Step 1 Fit the null model
has_children: false
---

# **Quantitative traits**

We can use a linear regression to fit the null model for quantitative traits. That is ignoring random effects in mixed models. Or we can use a linear mixed model to fit the null model and obtain residuals.

## Linear regression

Please run the following code in R

### Read in example data sets

```
library(GRAB)
PhenoFile = system.file("extdata", "simuPHENO.txt", package = "GRAB")
PhenoData = data.table::fread(PhenoFile)
print(PhenoData)
#            IID      AGE GENDER      eta       bVec BinaryPheno  QuantPheno 
#    1:   Subj-1 59.61118      0 29.59961 -0.2059781           0  0.27866626
#    2:  Subj-10 61.31636      0 29.32101 -1.3371678           0 -2.29732890
#    3: Subj-100 59.16625      0 29.90944  0.3263173           0 -2.48702515
#    4: Subj-101 60.18490      1 28.99876 -1.5936861           0 -3.40632756
#    5: Subj-102 58.76700      1 27.90020 -1.9832990           0 -3.44214439
#   ---                                                                     
#  996:     f9_5 59.25336      0 29.33119 -0.2954873           0 -0.91237106
#  997:     f9_6 57.90253      1 28.55622 -0.8950407           0 -2.50808150
#  998:     f9_7 62.04752      1 30.98239 -0.5413704           0 -0.38776894
#  999:     f9_8 58.79103      0 28.06333 -1.3321861           0 -0.02743997
# 1000:     f9_9 61.18766      1 30.14505 -0.9487810           0 -0.02288709
#       OrdinalPheno   SurvTime SurvEvent
#    1:            0 0.03245097         0
#    2:            0 0.19733412         0
#    3:            1 0.09451178         0
#    4:            0 0.10946053         0
#    5:            0 0.04882298         0
#   ---                                  
#  996:            2 0.20041554         0
#  997:            0 0.01220480         0
#  998:            2 0.04456741         0
#  999:            0 0.07052257         0
# 1000:            0 0.08657261         0
```

### Fit the null model and obtain model residuals

```
nullmodel = lm(QuantPheno ~ AGE + GENDER, data = PhenoData)

ResidMat = data.frame(SubjID = PhenoData$IID,
                      Resid = nullmodel$residuals)
print(ResidMat)
#         SubjID      Resid
#    1:   Subj-1  0.7302493
#    2:  Subj-10 -2.7149915
#    3: Subj-100 -1.8086329
#    4: Subj-101 -3.6774972
#    5: Subj-102 -2.9905191
#   ---                    
#  996:     f9_5 -0.2783859
#  997:     f9_6 -1.6157763
#  998:     f9_7 -1.6084382
#  999:     f9_8  0.8422290
# 1000:     f9_9 -0.8052310

ResidMatFile = system.file("extdata", "ResidMat.txt", package = "GRAB")
data.table::fwrite(ResidMat, file = ResidMatFile, row.names = FALSE, col.names = TRUE, quote = FALSE, sep = "\t")
```

## Linear mixed model

In practice， we use SAIGE to fit the null linear mixed model. Here is an example code showing how to use docker to run SAIGE.

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
  --traitType=quantitative \
  --invNormalize=TRUE \
  --phenoCol=QuantPheno \
  --covarColList=AGE,GENDER \
  --sampleIDColinphenoFile=IID \
  --isCateVarianceRatio=FALSE \
  --IsOverwriteVarianceRatioFile=TRUE
```

See [SAIGE documentation](https://saigegit.github.io/SAIGE-doc/) for more details about these commands. At the end of the page, we will show you some tips to convert our SparseGRMFile to SAIGE's required format.

### Load step 1 product and obtain model residuals

Please run the following code in R

```
load("/path/to/your/output/SAIGE_step1.rda") # please verify your filepath.

ResidMat = data.frame(SubjID = modglmm$sampleID,
                      Resid = modglmm$residuals)
print(ResidMat)
#         SubjID      Resid
#    1:     f1_1 -1.1239756
#    2:     f1_2  0.7595350
#    3:     f1_3  0.6276040
#    4:     f1_4  1.2506376
#    5:     f1_5 -0.3869877
#   ---
#  996: Subj-496 -0.7629248
#  997: Subj-497  0.2142096
#  998: Subj-498 -0.1806843
#  999: Subj-499  0.2505687
# 1000: Subj-500  0.2809179

ResidMatFile = system.file("extdata", "ResidMat.txt", package = "GRAB")
data.table::fwrite(ResidMat, file = ResidMatFile, row.names = FALSE, col.names = TRUE, quote = FALSE, sep = "\t")
```

> **Note**  
> - <code style="color : fuchsia">ResidMatFile</code> has the same format regardless of testing $\beta$<sub>g</sub> or $\tau$<sub>g</sub>.
> - The column names of <code style="color : fuchsia">ResidMatFile</code> must be exactly <code style="color : fuchsia">SubjID</code> in the first column and <code style="color : fuchsia">Resid</code> in the second column.
> - Each subject should match its corresponding residual.

### Tips to convert our <code style="color : fuchsia">SparseGRMFile</code> to [SAIGE](https://saigegit.github.io/SAIGE-doc/) required format

Here is a R code to convert our <code style="color : fuchsia">SparseGRMFile</code> to [SAIGE](https://saigegit.github.io/SAIGE-doc/) required format.

```
SparseGRMFile = system.file("SparseGRM", "SparseGRM.txt", package = "GRAB")
sparseGRM = data.table::fread(SparseGRMFile)

uniqeID = unique(c(sparseGRM$ID1, sparseGRM$ID2))
nSubjects = length(uniqeID)
nRelatedness = nrow(sparseGRM)

header0 = "%%MatrixMarket matrix coordinate real symmetric"
header = c(nSubjects, nSubjects, nRelatedness)

ID1 = sparseGRM$ID1
ID2 = sparseGRM$ID2
Value = sparseGRM$Value

posID1 = match(ID1, uniqeID)
posID2 = match(ID2, uniqeID)

SAIGE.SparseGRM = cbind(posID1, posID2, Value)
SAIGE.SparseGRM = rbind(header, SAIGE.SparseGRM)

data.table::fwrite(data.table::data.table(header0), file = "/path/to/your/sparseGRM/SAIGE_SparseGRM.txt",
                   row.names = FALSE, col.names = FALSE, quote = FALSE, sep = " ")

data.table::fwrite(SAIGE.SparseGRM, file = "/path/to/your/sparseGRM/SAIGE_SparseGRM.txt",
                   scipen = 10,
                   row.names = FALSE, col.names = FALSE, quote = FALSE, sep = " ", append = T)

data.table::fwrite(data.table::data.table(uniqeID), file = "/path/to/your/sparseGRM/SAIGE_SparseGRMSampleID.txt",
                   row.names = FALSE, col.names = FALSE, quote = FALSE, sep = " ")
```