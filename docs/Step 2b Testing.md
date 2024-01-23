---
layout: default
title: b). Screen for marker-level association study
nav_order: 2
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
parent: Step 2 Conduct genome-wide association studies
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

# **Conduct genome-wide association studies**

We can use <code style="color : darkorange">GRAB.marker</code> function in [**GRAB**](https://wenjianbi.github.io/grab.github.io/) package to conduct marker-level genome-wide association studies. <code style="color : darkorange">GRAB.marker</code> share many parameters in [GRAB.ReadGeno](https://wenjianbi.github.io/grab.github.io/docs/read_genotype.html). Click here for more details about these parameters.

## Run <code style="color : darkorange">GRAB.marker</code> to conduct genome-wide association studies

```
GRAB.Marker(objNull,
            GenoFile,
            GenoFileIndex = NULL,
            OutputFile,
            OutputFileIndex = NULL,
            control = NULL)
```

**Command**

- <code style="color : darkred">objNull</code>: required. The output object of function <code style="color : darkorange">SPAGRM.NullModel</code> for SPA<sub>GRM</sub> method. For other methods (e.g. [POLMM](https://github.com/WenjianBI/POLMM)), it can be the output object of function <code style="color : darkorange">GRAB.NullModel</code>.
- <code style="color : darkred">GenoFile</code>: required. A character of genotype file. Currently, two types of genotype formats are supported: PLINK and BGEN. Click [GRAB.ReadGeno](https://wenjianbi.github.io/grab.github.io/docs/read_genotype.html) for more details.
- <code style="color : darkred">GenoFileIndex</code>: optional. Additional index files corresponding to the GenoFile. If _NULL_ (by default), the prefix is the same as <code style="color : darkred">GenoFile</code>. Click [GRAB.ReadGeno](https://wenjianbi.github.io/grab.github.io/docs/read_genotype.html) for more details.
- <code style="color : darkred">OutputFile</code>: required. A character of output file to save the analysis results.
- <code style="color : darkred">OutputFileIndex</code>: optional. A character of output index file to record the end point.  If _NULL_ (by default), <code style="color : darkred">OutputFileIndex = paste0(OutputFile, ".index")</code>.
- <code style="color : darkred">control</code>: optional. A list of parameters for controlling function <code style="color : darkorange">GRAB.marker</code>.
  - <code style="color : darkred">SPA_Cutoff</code>: a numeric value (default=2). If absolute value of observed score statistic $\geq$ `SPA_Cutoff` $\times$ its estimated standard error, saddlepoint approximation is applied; otherwise normal distribution approximation is applied.
  - <code style="color : darkred">zeta</code>: a numeric value (default=0). Initial value of saddle point. We use Newton iteration to find the root. This will converge faster if initial value is closer to the root.
  - <code style="color : darkred">tol</code>: a numeric value (default=1e-5). It decide the accuracy of Newton's iteration. Generally, tol=1e-4 is OK for a wide range of phenotypes. If model residuals are extremely unbalanced (e.g. when testing within-subject variability for longitudinal traits), <code style="color : darkred">tol</code> should be 1e-5 or smaller.
  - <code style="color : darkred">impute_method</code>: a character, <code style="color : fuchsia">"mean"</code>(default), <code style="color : fuchsia">"bestguess"</code>, or <code style="color : fuchsia">"none"</code>. Click [GRAB.ReadGeno](https://wenjianbi.github.io/grab.github.io/docs/read_genotype.html) for more details.
  - <code style="color : darkred">missing_cutoff</code>: a numeric value (default=0.15). Markers with missing rate $>$ this value will be excluded from analysis.
  - <code style="color : darkred">min_maf_marker</code>: a numeric value (default=0.001). Markers with MAF $<$ this value will be excluded from analysis.
  - <code style="color : darkred">min_mac_marker</code>: a numeric value (default=20). Markers with minor allele count (MAC) < this value will be excluded from analysis.
  - <code style="color : darkred">nMarkersEachChunk</code>: number of markers (default=10000) in one chunk to output.
  - <code style="color : darkred">omp_num_threads</code>: (To be added later) a numeric value to specify the number of threads in OpenMP for parallel computation.
  - <code style="color : darkred">IDsToIncludeFile</code>: Click [GRAB.ReadGeno](https://wenjianbi.github.io/grab.github.io/docs/read_genotype.html) for more details.
  - <code style="color : darkred">IDsToExcludeFile</code>: Click [GRAB.ReadGeno](https://wenjianbi.github.io/grab.github.io/docs/read_genotype.html) for more details.
  - <code style="color : darkred">RangesToIncludeFile</code>: Click [GRAB.ReadGeno](https://wenjianbi.github.io/grab.github.io/docs/read_genotype.html) for more details.
  - <code style="color : darkred">RangesToExcludeFile</code>: Click [GRAB.ReadGeno](https://wenjianbi.github.io/grab.github.io/docs/read_genotype.html) for more details.
  - <code style="color : darkred">AlleleOrder</code>: for PLINK files, the default <code style="color : darkred">AlleleOrder</code> = <code style="color : fuchsia">"alt-first"</code>; for BGEN files, the default <code style="color : darkred">AlleleOrder</code> = <code style="color : fuchsia">"ref-first"</code>. Click [GRAB.ReadGeno](https://wenjianbi.github.io/grab.github.io/docs/read_genotype.html) for more details.

**Value**

The analysis results are written in a file of <code style="color : fuchsia">OutputFile</code>, which includes the following columns.

- <code style="color : fuchsia">Marker</code>: marker IDs extracted from <code style="color : fuchsia">GenoFile</code> and <code style="color : fuchsia">GenoFileIndex</code>.
- <code style="color : fuchsia">Info</code>: marker Information of <code style="color : fuchsia">"CHR:POS:REF:ALT"</code>. The order of REF/ALT depends on <code style="color : darkred">AlleleOrder</code>: <code style="color : fuchsia">"ref-first"</code> or <code style="color : fuchsia">"alt-first"</code>.
- <code style="color : fuchsia">AltFreq</code>: alternative allele frequency (before genotype imputation, might be > 0.5). If the AltFreq of most markers are > 0.5, you should consider resetting <code style="color : darkred">AlleleOrder</code>.
- <code style="color : fuchsia">AltCounts</code>: alternative allele counts (before genotype imputation).
- <code style="color : fuchsia">MissingRate</code>: missing rate for each marker.
- <code style="color : fuchsia">zScore</code>: standardized score statistics, usually follows a standard normal distribution.
- <code style="color : fuchsia">Pvalue</code>: association test p-value for each marker.
- <code style="color : fuchsia">hwepval</code>: hardy weinberg equilibrium p-value for each marker.

**Example**

```
# load in an R object of "obj.SPAGRM"
objSPAGRMFile = system.file("results", "objSPAGRMFile.RData", package = "GRAB")  
load(objSPAGRMFile)
```

```
# run the GRAB.Marker function
GenoFile = system.file("extdata", "simuPLINK.bed", package = "GRAB")
OutputDir = system.file("results", package = "GRAB")
OutputFile = paste0(OutputDir, "/simuOutput.txt")

GRAB.Marker(objNull = obj.SPAGRM,
            GenoFile = GenoFile,
            OutputFile = OutputFile)
```

```
# preview the results
results = data.table::fread(OutputFile)
print(results)
#           Marker        Info    AltFreq AltCounts MissingRate      zScore    Pvalue   hwepval
#     1:     SNP_1     1:1:G:A 0.39132706       749       0.043  0.21156824 0.8324439 0.3054106
#     2:     SNP_2     1:2:G:A 0.45886076       870       0.052  1.29168799 0.1964652 0.5650432
#     3:     SNP_3     1:3:G:A 0.39769150       758       0.047 -0.12118120 0.9035475 0.8631123
#     4:     SNP_4     1:4:G:A 0.44867725       848       0.055  0.09807512 0.9218726 0.3074466
#     5:     SNP_5     1:5:G:A 0.24293194       464       0.045 -1.63994666 0.1010163 0.2631180
#    ---                                                                                       
#  9996:  SNP_9996  1:9996:G:A 0.33889468       650       0.041 -0.06282245 0.9499079 0.2573180
#  9997:  SNP_9997  1:9997:G:A 0.26399155       500       0.053 -0.15408692 0.8775412 0.2418564
#  9998:  SNP_9998  1:9998:G:A 0.06427819       122       0.051 -0.49062723 0.6236901 0.5603254
#  9999:  SNP_9999  1:9999:G:A 0.24250535       453       0.066 -0.65968239 0.5094577 0.2910454
# 10000: SNP_10000 1:10000:G:A 0.14014752       266       0.051  1.12354821 0.2612047 0.2401637
```

> **Note**  
> - You can also see [GRAB documentation](https://wenjianbi.github.io/grab.github.io/docs/overview.html) for more details about <code style="color : darkorange">GRAB.marker</code> function.
