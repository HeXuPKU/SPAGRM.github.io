---
layout: default
title: b). Screen for marker-level association study
nav_order: 2
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
parent: Step 2 Conduct genome-wide association studies
has_children: false
---

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
  - `SPA_Cutoff`: a numeric value (default=2). 