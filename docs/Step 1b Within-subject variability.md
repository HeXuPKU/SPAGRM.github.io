---
layout: default
title: Within-subject variability
nav_order: 2
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
parent: Step 1 Fit the null model
has_children: false
---

# **Within-subject variability**

Recently, TrajGWAS method introduced a mixed-effects multiple location scale model into large-scale longitudinal trait GWAS. Building on regular linear mixed effect models, they further formulated $\sigma_{\varepsilon_{ij}}$ via

$$ \sigma_{\varepsilon_{ij}} = exp(W_{ij}^T \tau + G_i \tau_g + w_i) $$

The standard error $\sigma_{\varepsilon_{ij}}$ is determined by covariate vector $W_{ij}$, genotype $G_i$, and a random intercept $w_i$. This additional formula allows TrajGWAS to test genetic effects on within-subject (WS) variability (i.e. $\tau_g$), which is also an important risk factor for complex diseases. The Julia package [WiSER](https://github.com/OpenMendel/WiSER.jl) can fit the above model.

## Quick start for TrajGWAS

Please run the following code in Julia. Julia package [TrajGWAS](https://github.com/OpenMendel/TrajGWAS.jl) is builded upon the [WiSER](https://github.com/OpenMendel/WiSER.jl) method and can also be used for model fitting. More details can be seen in [WiSER documentation](https://github.com/OpenMendel/WiSER.jl/blob/master/docs/src/model_fitting.md) or [TrajGWAS documentation](https://openmendel.github.io/TrajGWAS.jl/dev/). In this section, our main focus is to demonstrate how to obtain model residuals for testing $\beta$<sub>g</sub> = 0 (i.e., the mean level) from the fitted null model.

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
```

### Fit the null model

```
PhenoFile = "../GRAB/extdata/simuLongPHENO.txt" # please copy the filepath of simuLongPHENO.txt.
LongPheno = CSV.read(PhenoFile, DataFrame)

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

### Obtain model residuals of testing $\tau$<sub>g</sub> = 0 (i.e., the within-subject variability)

```
rownames = nullmodel.ids

ResidMatFile_tau = split(PhenoFile,"simuLongPHENO.txt")[1] * "ResidMat.txt"

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

> **Note**  
> - The column names of <code style="color : fuchsia">ResidMatFile</code> must be exactly <code style="color : fuchsia">SubjID</code> in the first column and <code style="color : fuchsia">Resid</code> in the second column.
> - Each subject should match its corresponding residual.
> - Above code is mostly the same as in [Linear mixed-effect models](https://github.com/HeXuPKU/SPAGRM.github.io/blob/main/docs/Step%201a1%20Linear%20mixed-effect%20models.md#L86). In practice, model residuals for testing the mean and WS variability can be obtained simultaneously from the fitted WiSER model.