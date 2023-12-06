---
layout: default
title: Sparse GRM
nav_order: 1
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
parent: Step 0 Calculate GRM and IBD files
has_children: false
---

# Make a Sparse GRM file

## Prepare PLINK binary files

PLINK binary files with high-quality genotyped variants are required to make a sparse GRM. For example, we used 227,437 LD pruned, high-quality genetic variants (MAF > 0.05 and missing rate < 0.05) to calculate sparse GRM and IBD probibalities in SPA<sub>GRM</sub> paper. Use the command below to extract high-quality genetic variants in PLINK:

```
--maf 0.05
--geno 0.05
--indep-pairwise 500 50 0.2
```

Before next step, you should have below PLINK binary files:
```
XXX.bed,   XXX.bim;   XXX.fam (XXX should be the same prefix of the PLINK binary files)
```

## Run `getTempFilesFullGRM` to get temporary files

```
getTempFilesFullGRM(PlinkFile,
                    nPartsGRM,
                    partParallel,
                    tempDir = NULL,
                    subjData = NULL,
                    minMafGRM = 0.01,
                    maxMissingGRM = 0.1,
                    threadNum = 8)
```

- PlinkFile: required. A path to PLINK files (without file extensions of bed/bim/fam).

- nPartsGRM: required. A numeric value (e.g. 250) to split subjects to multiple parts. For UK Biobank data analysis with ~500K samples, it is recommended to set nPartsGRM=250.

- partParallel: required. A numeric value (from 1 to nPartsGRM) to split all jobs for parallel computation.

- tempDir: optional. A path to store temp files to be passed to `getSparseGRM`. Default is system.file("SparseGRM", "temp", package = "GRAB"). If the sample size > 100K, then the temporary files might need a large amount of space. This should be consistent to the input of `getSparseGRM`!

- subjData: optional. A character vector to specify subject IDs to retain (i.e. IID). Default is NULL, i.e. all subjects are retained in sparse GRM.

- minMafGRM: optional. Minimal value of MAF cutoff to select markers (from PLINK files) to make sparse GRM. (default=0.01)

- maxMissingGRM: optional. Maximal value of missing rate to select markers (from PLINK files) to make sparse GRM. (default=0.1)

- threadNum: optional. Number of threads (CPUs) to use.

**Example:**

```
GenoFile = system.file("extdata", "simuPLINK.bed", package = "GRAB")
PlinkPrefix = tools::file_path_sans_ext(GenoFile)   # remove file extension
nPartsGRM = 2
for(partParallel in 1:nPartsGRM)
{
   getTempFilesFullGRM(PlinkPrefix, 
                       nPartsGRM = nPartsGRM, 
                       partParallel = partParallel)
}
```

## Run `getSparseGRM` to combine the temporary files

```
getSparseGRM(PlinkFile,
             nPartsGRM,
             SparseGRMFile,
             tempDir = NULL,
             relatednessCutoff = 0.05,
             minMafGRM = 0.01,
             maxMissingGRM = 0.1,
             rm.tempFiles = FALSE)
```

- PlinkFile: required. A path to PLINK files (without file extensions of bed/bim/fam). It should be the same as used in `getTempFilesFullGRM` function.

- nPartsGRM: required. A numeric value (e.g. 250) to split subjects to multiple parts. For UK Biobank data analysis with ~500K samples, it is recommended to set nPartsGRM=250. It should be the same as used in `getTempFilesFullGRM` function.

- SparseGRMFile: required. A file path to store sparse GRM. (e.g. XXX.txt)

- tempDir: optional. A path to store temp files from `getTempFilesFullGRM`. Default is system.file("SparseGRM", "temp", package = "GRAB"). This should be consistent to the input of `getTempFilesFullGRM`!

- relatednessCutoff: optional. A cutoff for sparse GRM, only kinship coefficient greater than this cutoff will be retained in sparse GRM. (default=0.05)

- minMafGRM: optional. Minimal value of MAF cutoff to select markers (from PLINK files) to make sparse GRM. (default=0.01)

- maxMissingGRM: optional. Maximal value of missing rate to select markers (from PLINK files) to make sparse GRM. (default=0.1)

- rm.tempFiles: optional. A logical value indicating if the temp files generated in `getTempFilesFullGRM` will be deleted. (default=FALSE)

**Example:**

```
GenoFile = system.file("extdata", "simuPLINK.bed", package = "GRAB")
PlinkPrefix = tools::file_path_sans_ext(GenoFile)   # remove file extension
nPartsGRM = 2
SparseGRMFile = system.file("SparseGRM", "SparseGRM.txt", package = "GRAB")
getSparseGRM(PlinkPrefix, 
             nPartsGRM = nPartsGRM, 
             SparseGRMFile = SparseGRMFile,
             relatednessCutoff = 0.05)
```

## About the `SparseGRMFile`

The below gives more details about the `SparseGRMFile`:

```
SparseGRMFile = system.file("SparseGRM", "SparseGRM.txt", package = "GRAB")
SparseGRM = data.table::fread(SparseGRMFile)
print(SparseGRM)
#            ID1      ID2     Value
#    1:     f1_1     f1_1 0.9550625
#    2:     f1_2     f1_2 1.0272297
#    3:     f1_3     f1_3 1.0192574
#    4:     f1_4     f1_4 1.0053836
#    5:     f1_5     f1_1 0.4648096
#   ---
# 2547: Subj-496 Subj-496 1.0027448
# 2548: Subj-497 Subj-497 0.9913247
# 2549: Subj-498 Subj-498 0.9785985
# 2550: Subj-499 Subj-499 1.0109795
# 2551: Subj-500 Subj-500 0.9783296
```

`SparseGRMFile` will latter be used for calculating IBD probabilities. 

> **Note**  
> - `GRAB` package implicitly uses `GCTA` software to make the sparse GRM. As required by `GCTA` software, the function `getSparseGRM` is only supported in Linux operation system and PLINK binary files with the same prefix are required.  
> - If users use other software (e.g. `GCTA`) to calculate a sparse GRM file, please convert it to the form above. The column names of sparseGRM file should be exactly `ID1`, `ID2` and `Value`!  
> - User can also refer to [GRAB Sparse GRM](https://wenjianbi.github.io/grab.github.io/docs/GRM_sparse.html) to make a sparse GRM file.
