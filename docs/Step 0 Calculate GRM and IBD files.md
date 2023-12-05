---
layout: default
title: Step 0 Calculate GRM and IBD files
nav_order: 2
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
has_children: false
has_toc: false
---

# Make a Sparse GRM file

## About function ```getSparseGRM```

We can use  ```getSparseGRM``` function in ```GRAB``` package to construct a sparse GRM file, which implicitly uses ```GCTA``` software to make a SparseGRMFile to be passed to function ```GRAB.NullModel```. As required by ```GCTA``` software, the function ```getSparseGRM``` is only supported in Linux operation system and PLINK binary files with the same prefix are required.

## Prepare PLINK binary files

PLINK binary files with high-quality genotyped variants are required to make a sparse GRM. For example, we used 227,437 LD pruned, high-quality genetic variants (MAF > 0.05 and missing rate < 0.05) to calculate sparse GRM and IBD probibalities in SPA<sub>GRM</sub> paper. Use the command below to extract high-quality genetic variants in PLINK:

```
--maf 0.05
--geno 0.05
--indep-pairwise 500 50 0.2
```

Before next step, you should have below PLINK binary files:
```
*.bed;   *.bim;   *.fam (* is the same prefix of the PLINK binary files)
```

## Run ```getTempFilesFullGRM``` to get temporary files

```
getTempFilesFullGRM(PlinkFile,
                    nPartsGRM,
                    partParallel,
                    tempDir = NULL,
                    subjData = NULL,
                    minMafGRM = 0.01,
                    maxMissingGRM = 0.1,
                    threadNum = 8)

# PlinkFile: required. A path to PLINK files (without file extensions of bed/bim/fam).

# nPartsGRM: required. A numeric value (e.g. 250) to split subjects to multiple parts. For UK Biobank data analysis with ~500K samples, it is recommended to set nPartsGRM=250.

# partParallel: required. A numeric value (from 1 to nPartsGRM) to split all jobs for parallel computation.

# tempDir: optional. A path to store temp files to be passed to getSparseGRM. Default is system.file("SparseGRM", "temp", package = "GRAB"). If the sample size > 100K, then the temporary files might need a large amount of space. This should be consistent to the input of getSparseGRM!

# subjData: optional. A character vector to specify subject IDs to retain (i.e. IID). Default is NULL, i.e. all subjects are retained in sparse GRM.

# minMafGRM: optional. Minimal value of MAF cutoff to select markers (from PLINK files) to make sparse GRM. (default=0.01)

# maxMissingGRM: optional. Maximal value of missing rate to select markers (from PLINK files) to make sparse GRM. (default=0.1)

# threadNum: optional. Number of threads (CPUs) to use.
```

**Example:**

```
GenoFile = system.file("extdata", "simuPLINK.bed", package = "GRAB")
PlinkPrefix = tools::file_path_sans_ext(GenoFile)   # remove file extension
nPartsGRM = 2;
for(partParallel in 1:nPartsGRM)
{
   getTempFilesFullGRM(PlinkPrefix, 
                       nPartsGRM = nPartsGRM, 
                       partParallel = partParallel)
}
```


More details can be seen in [GRAB Sparse GRM](https://wenjianbi.github.io/grab.github.io/docs/GRM_sparse.html).

# Make a Pairwise IBD Probability file

## About function ```getPairwiseIBD```

We can use  ```getPairwiseIBD``` function in ```GRAB``` package to construct a pairwise IBD probability file. Pairwise IBD Probability file is only required for SPA<sub>GRM</sub>, so we introduce it here.

## Step 0: Prepare PLINK binary files
