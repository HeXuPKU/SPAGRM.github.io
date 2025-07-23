---
layout: default
title: Longitudinal trajectory
nav_order: 3
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
parent: Step 1 Fit the null model
has_children: true
has_toc: true
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

# **Longitudinal trajectory**

We propose SAGELD, a method for gene-environment interaction analysis of longitudinal traits while controlling for sample relatedness. Consider a linear mixed model:

$$ y_{ij} = X_{ij}^T \beta + G_i \beta_g + (G_i \circ E_{ij}) \beta_{g\times e} + Z_{ij}^T \gamma_i + \varepsilon_{ij} $$ 

where $E$ is a time-varying variable (e.g., age or BMI) that changes within subjects. We primarily focus on testing $\beta_{g\times e}=0$.

SAGELD consists of two steps:

1) fit an LMM without any genetic variant (i.e., $H_c:\beta_g=\beta_{g\times e}=0$), and estimate model parameters and calculate residuals.

2) use a matrix projection strategy to construct test statistics and then apply SPA<sub>GRM</sub> to conduct GWAS while controlling for sample relatedness.
