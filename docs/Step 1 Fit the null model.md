---
layout: default
title: Step 1 Fit the null model
nav_order: 3
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
has_children: true
has_toc: true
---

# Fit the null model

SPA<sub>GRM</sub> is designed as a universal GWAS analysis framework for various complex traits, such as longitudinal, quantitative, binary traits, and more. 

For SPA<sub>GRM</sub>, it is optional, rather than required, to incorporate a random effect to characterize sample relatedness. Therefore, SPA<sub>GRM</sub> is particularly suitable for the analysis of complex traits with complicated structure, and the application of complex statistical models.

To conduct a GWAS analysis containing related subjects, users only need to fit the null model only once with/without incorporating random effcts and obtain model residuals defined as the gradient of the likelihood function. we will latter show you how to implement it through longitudinal, quantitative, binary trait analysis.
