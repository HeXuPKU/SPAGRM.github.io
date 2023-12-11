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

### Display the example data set

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

### Installation and library

```
using DataFrames, CSV, DelimitedFiles
using Ipopt, NLopt, KNITRO
using WiSER, TrajGWAS

# for TrajGWAS new version, solver settings:
solver = Ipopt.Optimizer(); solver_config = Dict("print_level"=>0, "mehrotra_algorithm"=>"yes", "warm_start_init_point"=>"yes", "max_iter"=>100)
# solver = Ipopt.Optimizer(); solver_config = Dict("print_level"=>0, "watchdog_shortened_iter_trigger"=>3, "max_iter"=>100)
# solver = KNITRO.Optimizer(); solver_config = Dict("outlev"=>3) # (Knitro is commercial software)
# solver = NLopt.Optimizer(); solver_config = Dict("algorithm"=>:LD_MMA, "maxeval"=>4000)
# solver = NLopt.Optimizer(); solver_config = Dict("algorithm"=>:LD_LBFGS, "maxeval"=>4000)

PhenoFile = "../GRAB/extdata/simuLongPHENO.txt" # copy the filepath of simuLongPHENO.txt.
```

### Display the example data set

```
LongPheno = CSV.read(PhenoFile, DataFrame)

# 10515×4 DataFrame
#    Row │ IID       AGE      GENDER  LongPheno 
#        │ String15  Float64  Int64   Float64   
# ───────┼──────────────────────────────────────
#      1 │ Subj-1    49.7667       0    21.184  
#      2 │ Subj-1    46.9762       0    22.6392 
#      3 │ Subj-1    48.6039       0    22.999  
#      4 │ Subj-1    48.4704       0    22.8883 
#      5 │ Subj-1    50.5121       0    23.1773 
#      6 │ Subj-1    47.5477       0    22.7587 
#      7 │ Subj-1    50.2266       0    21.5066 
#      8 │ Subj-1    50.2574       0    22.9993 
#      9 │ Subj-1    49.1261       0    23.6902 
#    ⋮   │    ⋮         ⋮       ⋮         ⋮     
#  10508 │ f9_8      48.8736       1    18.8061 
#  10509 │ f9_8      50.4701       1    22.4624 
#  10510 │ f9_9      51.0203       1    24.1638 
#  10511 │ f9_9      50.3452       1    19.047
#  10512 │ f9_9      50.4438       1    24.2056
#  10513 │ f9_9      51.5276       1    24.8624
#  10514 │ f9_9      49.9013       1    23.1841
#  10515 │ f9_9      50.2109       1    21.5607
#                             10498 rows omitted
```

### Fit the null model

```
nullmodel = trajgwas(@formula(LongPheno ~ 1 + AGE + GENDER),
                    @formula(LongPheno ~ 1 + AGE),
                    @formula(LongPheno ~ 1 + AGE + GENDER),
                    :IID,
                    LongPheno,
                    nothing;
                    solver=solver,
                    solver_config = solver_config)

# ******************************************************************************
# This program contains Ipopt, a library for large-scale nonlinear optimization.
# Ipopt is released as open source code under the Eclipse Public License (EPL).
# For more information visit https://github.com/coin-or/Ipopt
# ******************************************************************************
# run = 1, ‖Δβ‖ = 0.379288, ‖Δτ‖ = 0.076489, ‖ΔL‖ = 0.653537, status = LOCALLY_SOLVED, time(s) = 0.800000
# run = 2, ‖Δβ‖ = 0.015746, ‖Δτ‖ = 0.028828, ‖ΔL‖ = 0.169548, status = LOCALLY_SOLVED, time(s) = 0.086000
# Within-subject variance estimation by robust regression (WiSER)
# Mean Formula:
# LongPheno ~ 1 + AGE + GENDER
# Random Effects Formula:
# LongPheno ~ 1 + AGE
# Within-Subject Variance Formula:
# LongPheno ~ 1 + AGE + GENDER
# Number of individuals/clusters: 1000
# Total observations: 10515
# Fixed-effects parameters:
# ─────────────────────────────────────────────────────────
#                    Estimate  Std. Error       Z  Pr(>|Z|)
# ─────────────────────────────────────────────────────────
# β1: (Intercept)  37.2366      1.13462     32.82    <1e-99
# β2: AGE          -0.327652    0.022705   -14.43    <1e-46
# β3: GENDER        0.712463    0.094477     7.54    <1e-13
# τ1: (Intercept)  -1.33006     2.07856     -0.64    0.5222
# τ2: AGE           0.0566825   0.042218     1.34    0.1794
# τ3: GENDER        0.132336    0.0642011    2.06    0.0393
# ─────────────────────────────────────────────────────────
# Random effects covariance matrix Σγ:
#  "γ1: (Intercept)"  67.3923   -1.32643
#  "γ2: AGE"          -1.32643   0.0266415
```

## Obtain model residuals

Please continue to run the following code in Julia

### Obtain model residuals of testing $\beta$<sub>g</sub> = 0 (i.e., the mean profile)

```
rownames = nullmodel.ids

ResidMatFile_beta = split(PhenoFile,"simuLongPHENO.txt")[1] * "ResidMatFile_beta.txt"

f1 = open(ResidMatFile_beta, "w")
writedlm(f1, ["SubjID" "Resid"])

for j in 1:length(nullmodel.data)
        Resid_beta = sum(nullmodel.data[j].Dinv_r - transpose(nullmodel.data[j].rt_UUt))
        writedlm(f1, [rownames[j] Resid_beta])
end
close(f1)

ResidMat_beta = CSV.read(ResidMatFile_beta, DataFrame)

# 1000×2 DataFrame
#   Row │ SubjID    Resid      
#       │ String15  Float64    
# ──────┼──────────────────────
#     1 │ Subj-1     0.806224
#     2 │ Subj-10    0.964086
#     3 │ Subj-100  -1.00028
#     4 │ Subj-101   0.156806
#     5 │ Subj-102  -0.62983
#     6 │ Subj-103   0.0177747
#     7 │ Subj-104  -0.0753876
#     8 │ Subj-105   0.769811
#   ⋮   │    ⋮          ⋮
#   994 │ f9_3      -0.132167
#   995 │ f9_4       0.142017
#   996 │ f9_5      -1.25016
#   997 │ f9_6       0.265729
#   998 │ f9_7      -1.84335
#   999 │ f9_8      -0.883836
#  1000 │ f9_9       0.627392
#              985 rows omitted
```

### Obtain model residuals of testing $\tau$<sub>g</sub> = 0 (i.e., the within-subject variability)

```
rownames = nullmodel.ids

ResidMatFile_tau = split(PhenoFile,"simuLongPHENO.txt")[1] * "ResidMatFile_tau.txt"

f2 = open(ResidMatFile_tau, "w")
writedlm(f2, ["SubjID" "Resid"])

for j in 1:length(nullmodel.data)
        Resid_tau = - sum(nm.data[j].diagDVRV)
        writedlm(f2, [rownames[j] Resid_tau])
end
close(f2)

ResidMat_tau = CSV.read(ResidMatFile_tau, DataFrame)

# 1000×2 DataFrame
#   Row │ SubjID    Resid      
#       │ String15  Float64    
# ──────┼──────────────────────
#     1 │ Subj-1      6.63029
#     2 │ Subj-10    10.1477
#     3 │ Subj-100    6.96377
#     4 │ Subj-101    2.00466
#     5 │ Subj-102    0.762797
#     6 │ Subj-103   -9.68518
#     7 │ Subj-104    7.93268
#   ⋮   │    ⋮          ⋮
#   995 │ f9_4       -9.50279
#   996 │ f9_5        1.54764
#   997 │ f9_6       -7.54868
#   998 │ f9_7        9.64257
#   999 │ f9_8        1.72898
#  1000 │ f9_9        0.151542
#              987 rows omitted
```