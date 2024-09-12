---
layout: default
title: Longitudinal trand
nav_order: 3
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
parent: Step 1 Fit the null model
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

# **Longitudinal trand**

we plan to extend GWAS to other patterns harbored in the longitudinal trajectory. A notable example is the dynamic process (upward or downward) of complex traits after specific medical treatments or surgical procedures. To characterize this process, we add a gene-environment interaction term to the linear mixed model

$$ y_{ij} = X_{ij}^T \beta + G_i \beta_g + (G_i \circ T_{ij}) \beta_{g\times t} + Z_{ij}^T \gamma_i + \varepsilon_{ij} $$ 

where $T$ is a time-varying variable (e.g., age or medication) that changes with time. We primarily focus on testing $\beta_{g\times t}=0$.
