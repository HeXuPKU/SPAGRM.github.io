---
layout: default
title: Longitudinal traits
nav_order: 1
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
parent: Step 1 Fit the null model
has_children: false
---

# Longitudinal traits

We can use  `WiSER`, a Julia package to fit the null model for longitudinal traits. Another Julia package `TrajGWAS` is builded upon the `WiSER` method and can also be used for model fitting. More details can be seen in [WiSER documentation](https://github.com/OpenMendel/WiSER.jl/blob/master/docs/src/model_fitting.md) or [TrajGWAS documentation](https://openmendel.github.io/TrajGWAS.jl/dev/). In this online tutorial, our main focus is to demonstrate how to obtain model residuals from the fitted null model.

## Example data sets

Please run the following code in R

```
PhenoFile = system.file("extdata", "simuLongPHENO.txt", package = "GRAB")
print(PhenoFile)
# "../GRAB/extdata/simuLongPHENO.txt"

LongPheno = data.table::fread(PhenoFile)
print(LongPheno)
#           IID      AGE GENDER LongPheno
#     1: Subj-1 49.76668      0  21.18404
#     2: Subj-1 46.97617      0  22.63922
#     3: Subj-1 48.60388      0  22.99897
#     4: Subj-1 48.47036      0  22.88830
#     5: Subj-1 50.51208      0  23.17735
#    ---                                 
# 10511:   f9_9 50.34522      1  19.04696
# 10512:   f9_9 50.44379      1  24.20562
# 10513:   f9_9 51.52763      1  24.86237
# 10514:   f9_9 49.90129      1  23.18408
# 10515:   f9_9 50.21092      1  21.56067
```

## Quick start for TrajGWAS

Please run the following code in Julia

```
using DataFrames, CSV, DelimitedFiles, Statistics, LinearAlgebra
using Ipopt, NLopt, KNITRO
using WiSER, TrajGWAS

# for TrajGWAS new version, solver settings:
solver = Ipopt.Optimizer(); solver_config = Dict("print_level"=>0, "mehrotra_algorithm"=>"yes", "warm_start_init_point"=>"yes", "max_iter"=>100)
# solver = Ipopt.Optimizer(); solver_config = Dict("print_level"=>0, "watchdog_shortened_iter_trigger"=>3, "max_iter"=>100)
# solver = KNITRO.Optimizer(); solver_config = Dict("outlev"=>3) # (Knitro is commercial software)
# solver = NLopt.Optimizer(); solver_config = Dict("algorithm"=>:LD_MMA, "maxeval"=>4000)
# solver = NLopt.Optimizer(); solver_config = Dict("algorithm"=>:LD_LBFGS, "maxeval"=>4000)

PhenoFile = "../GRAB/extdata/simuLongPHENO.txt" # replicate the filepath of simuLongPHENO.txt.

```
