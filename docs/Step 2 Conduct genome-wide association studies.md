---
layout: default
title: Step 2 Conduct genome-wide association studies
nav_order: 4
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
has_children: true
has_toc: true
---

# Genome-wide association studies

We have demonstrated in step 1 that model residuals share the same format for a wide range of phenotypes and statistical models. Therefore, SPA<sub>GRM</sub> is particularly suitable for the analysis of complex traits with complicated structure, and the application of complex statistical models.

SPA<sub>GRM</sub> utilizes precise estimations of joint genotype distributions to approximate the distribution of score statistics and then calibrate p values. To avoid redundant computations in step 2, SPA<sub>GRM</sub>  approximates the discrete joint distribution of genotype vectors in advance. Then SPA<sub>GRM</sub> screen whole genome for marker-level association analysis. 

