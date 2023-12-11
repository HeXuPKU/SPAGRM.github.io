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

## Prepare PLINK binary files
