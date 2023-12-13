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

# Conduct genome-wide association studies

We can use `GRAB.marker` function in GRAB package to conduct marker-level genome-wide association studies. `GRAB.marker` share many parameters in [GRAB.ReadGeno](https://wenjianbi.github.io/grab.github.io/docs/read_genotype.html). Click here for more details about these parameters.

## Run `GRAB.marker` to conduct genome-wide association studies

```
GRAB.Marker(objNull,
            GenoFile,
            GenoFileIndex = NULL,
            OutputFile,
            OutputFileIndex = NULL,
            control = NULL)
```

- `objNull`: required. The output object of function `SPAGRM.NullModel` for SPA<sub>GRM</sub> method. For other methods (e.g. POLMM), it can be the output object of function `GRAB.NullModel`.
- `GenoFile`: required. A character of genotype file. Currently, two types of genotype formats are supported: PLINK and BGEN. Click [GRAB.ReadGeno](https://wenjianbi.github.io/grab.github.io/docs/read_genotype.html) for more details.
- `GenoFileIndex`: optional. Additional index files corresponding to the GenoFile. If _NULL_ (by default), the prefix is the same as `GenoFile`. Click [GRAB.ReadGeno](https://wenjianbi.github.io/grab.github.io/docs/read_genotype.html) for more details.
- `OutputFile`: required. A character of output file to save the analysis results.
- `OutputFileIndex`: optional. A character of output index file to record the end point.  If _NULL_ (by default), OutputFileIndex = paste0(OutputFile, ".index").
- `control`: optional. A list of parameters for controlling function `GRAB.Marker`.
  - `SPA_Cutoff`: a numeric value (default=2). If absolute value of observed score statistic $\geq$ SPA_Cutoff $\times$ its estimated standard error, saddlepoint approximation is applied; otherwise normal distribution approximation is applied.
  - `zeta`: a numeric value (default=0). Initial value of saddle point. We use Newton iteration to find the root. This will converge faster if initial value is closer to the root.
  - `tol`: a numeric value (default=1e-5). It decide the accuracy of Newton's iteration. Generally, tol=1e-4 is OK for a wide range of phenotypes. If model residuals are extremely unbalanced (e.g. when testing within-subject variability for longitudinal traits), `tol` should be 1e-5 or smaller.
  - `impute_method`: a character, "mean" (default), "bestguess", or "none". Click [GRAB.ReadGeno](https://wenjianbi.github.io/grab.github.io/docs/read_genotype.html) for more details.
  - `missing_cutoff`: a numeric value (default=0.15). Markers with missing rate $>$ this value will be excluded from analysis.
  - `min_maf_marker`: a numeric value (default=0.001). Markers with MAF $<$ this value will be excluded from analysis.
  - `min_mac_marker`: a numeric value (default=20). Markers with minor allele count (MAC) < this value will be excluded from analysis.
  - `nMarkersEachChunk`: number of markers (default=10000) in one chunk to output.
  - `omp_num_threads`: (To be added later) a numeric value to specify the number of threads in OpenMP for parallel computation.
  - `IDsToIncludeFile`: Click [GRAB.ReadGeno](https://wenjianbi.github.io/grab.github.io/docs/read_genotype.html) for more details.
  - `IDsToExcludeFile`: Click [GRAB.ReadGeno](https://wenjianbi.github.io/grab.github.io/docs/read_genotype.html) for more details.
  - `RangesToIncludeFile`: Click [GRAB.ReadGeno](https://wenjianbi.github.io/grab.github.io/docs/read_genotype.html) for more details.
  - `RangesToExcludeFile`: Click [GRAB.ReadGeno](https://wenjianbi.github.io/grab.github.io/docs/read_genotype.html) for more details.
  - `AlleleOrder`: for PLINK files, the default `AlleleOrder` = "alt-first"; for BGEN files, the default `AlleleOrder` = "ref-first". Click [GRAB.ReadGeno](https://wenjianbi.github.io/grab.github.io/docs/read_genotype.html) for more details.