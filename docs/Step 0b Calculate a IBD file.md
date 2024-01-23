---
layout: default
title: Pairwise IBD
nav_order: 2
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
parent: Step 0 Calculate GRM and IBD files
has_children: false
---

# **Make a Pairwise IBD Probability file**

We can use  <code style="color : darkorange">getPairwiseIBD</code> function in [**GRAB**](https://wenjianbi.github.io/grab.github.io/) package to construct a pairwise IBD probability file. Pairwise IBD Probability file is only required for SPA<sub>GRM</sub>, so we introduce it here.

## Prepare PLINK binary files

Similar to calculating the sparse GRM, PLINK binary files with high-quality genotyped variants are required to make a pairwise IBD probability file. We recommend using the same PLINK binary files to calculate the sparse GRM file and then the pairwise IBD file.

In addition to the <code style="color : fuchsia">XXX.bed</code>, <code style="color : fuchsia">XXX.bim</code>, and <code style="color : fuchsia">XXX.fam</code> files, <code style="color : fuchsia">XXX.frq</code> file is also required for <code style="color : darkorange">getPairwiseIBD</code> function! Users can use PLINK command to generate the corresponding <code style="color : fuchsia">XXX.frq</code> file for the existing PLINK binary files:

```
--freq --out XXX
```

Before next step, you should have below PLINK binary files:
```
XXX.bed,   XXX.bim,   XXX.fam (XXX should be the same prefix of the PLINK binary files)
XXX.frq (frq file doesn't necessarily need to have the same prefix as bed, bim, and fam files)
```

## Run <code style="color : darkorange">getPairwiseIBD</code> to make a Pairwise IBD Probability file

```
getPairwiseIBD(PlinkFile,
               SparseGRMFile,
               PairwiseIBDFile,
               frqFile = NULL,
               tempDir = NULL,
               maxSampleNums = 2500,
               minMafIBD = 0.01,
               rm.tempFile = FALSE)
```

- <code style="color : darkred">PlinkFile</code>: required. A path to PLINK files (without file extensions of bed/bim/fam). It suggested be the same as used in <code style="color : darkorange">getSparseGRM</code> function.

- <code style="color : darkred">SparseGRMFile</code>: required. A file path to the sparse GRM file.

- <code style="color : darkred">PairwiseIBDFile</code>: required. A file path to store pairwise IBD-sharing probabilities.

- <code style="color : darkred">frqFile</code>: A path to the frq file corresponding to PLINK binary files (default=NULL, means the same prefix as PLINK binary files)

- <code style="color : darkred">tempDir</code>: optional. A path to store temp files generated in <code style="color : darkorange">getPairwiseIBD</code>. Default is `system.file("PairwiseIBD", "temp", package = "GRAB")`.

- <code style="color : darkred">maxSampleNums</code>: optional. Maximal number of samples in each run to calculate their pairwise IBD-sharing probabilities. If number of genetic variants > 100K, <code style="color : darkred">maxSampleNums</code> should < 2,500 to save momory usage. (default=2500)

- <code style="color : darkred">minMafIBD</code>: optional. Minimal value of MAF cutoff to select markers (from PLINK files) to make pairwise IBD-sharing probabilities. (default=0.01)

- <code style="color : darkred">rm.tempFiles</code>: optional. A logical value indicating if the temp files generated in <code style="color : darkorange">getPairwiseIBD</code> will be deleted. (default=FALSE)

**Example:**

```
GenoFile = system.file("extdata", "simuPLINK.bed", package = "GRAB")
PlinkPrefix = tools::file_path_sans_ext(GenoFile)   # remove file extension
SparseGRMFile = system.file("SparseGRM", "SparseGRM.txt", package = "GRAB")
PairwiseIBDFile = system.file("PairwiseIBD", "PairwiseIBD.txt", package = "GRAB")
getPairwiseIBD(PlinkPrefix, 
               SparseGRMFile = SparseGRMFile,
               PairwiseIBDFile = PairwiseIBDFile,
               frqFile = PlinkPrefix)
```

## About the <code style="color : fuchsia">PairwiseIBDFile</code>

The below gives more details about the <code style="color : fuchsia">PairwiseIBDFile</code>:

```
PairwiseIBDFile = system.file("PairwiseIBD", "PairwiseIBD.txt", package = "GRAB")
PairwiseIBD = data.table::fread(PairwiseIBDFile)
print(PairwiseIBD)
#            ID1   ID2          pa        pb         pc
#    1:     f1_5  f1_1 0.000000000 0.9296191 0.07038087
#    2:     f1_5  f1_2 0.075526555 0.8916187 0.03285475
#    3:     f1_6  f1_1 0.000000000 0.9466187 0.05338126
#    4:     f1_6  f1_2 0.052087250 0.9223602 0.02555257
#    5:     f1_6  f1_5 0.234476107 0.4995024 0.26602145
#   ---                                                
# 1547:   f50_10 f50_6 0.008918206 0.9413302 0.04975162
# 1548:   f50_10 f50_7 0.000000000 0.2184961 0.78150387
# 1549:   f50_10 f50_8 0.000000000 0.2533571 0.74664295
# 1550:   f50_10 f50_9 0.253726918 0.4999724 0.24630066
# 1551: Subj-293 f34_3 0.000000000 0.1040877 0.89591230
```

<code style="color : fuchsia">pa</code>, <code style="color : fuchsia">pb</code>, <code style="color : fuchsia">pc</code> are the pairwise IBD-sharing probabilities that two subjects share 2, 1, and 0 alleles in the same locus.

> **Note**  
> - <code style="color : fuchsia">PairwiseIBDFile</code> is only required for SPA<sub>GRM</sub> analysis. We do not recommend using it for other purposes.
> - If users use other software (e.g. [**PLINK**](https://www.cog-genomics.org/plink/1.9/ibd)) to calculate a pairwise IBD probability file, please convert it to the form above. The column names of PairwiseIBDFile should be exactly <code style="color : fuchsia">ID1</code>, <code style="color : fuchsia">ID2</code>, <code style="color : fuchsia">pa</code>, <code style="color : fuchsia">pb</code>, <code style="color : fuchsia">pc</code>, separated by a tabulation (`\t`)!  
> - To save computation time, we only calculate and store pairwise IBD probabilities of related subjects.  
> - The samples in <code style="color : fuchsia">PairwiseIBDFile</code> should be a subset of the samples in <code style="color : fuchsia">SparseGRMFile</code>. An error will occur if <code style="color : fuchsia">PairwiseIBDFile</code> contains samples that is not in the corresponding <code style="color : fuchsia">SparseGRMFile</code> in latter analysis!  
> - If you restrict SPA<sub>GRM</sub> to analyze unrelated subjects, then <code style="color : fuchsia">SparseGRMFile</code> and <code style="color : fuchsia">PairwiseIBDFile</code> are also required. <code style="color : fuchsia">SparseGRMFile</code> can consist of a table where <code style="color : fuchsia">ID1</code> is equal to <code style="color : fuchsia">ID2</code>, and <code style="color : fuchsia">Value</code> is 1 in each line. <code style="color : fuchsia">PairwiseIBDFile</code> can consist of an empty table with exactly column names of <code style="color : fuchsia">ID1</code>, <code style="color : fuchsia">ID2</code>, <code style="color : fuchsia">pa</code>, <code style="color : fuchsia">pb</code>, <code style="color : fuchsia">pc</code>, separated by a tabulation (`\t`).
