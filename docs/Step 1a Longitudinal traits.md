---
layout: default
title: Longitudinal traits
nav_order: 1
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

# **Longitudinal traits**

In GWAS, longitudinal traits are those phenotype data measured longitudinally or repeatedly for each individual in the sample. Longitudinal traits can characterize the evolution of health status. Thus, the application of GWAS to longitudinal traits is expected to yield novel discoveries.

Linear mixed effect models and generalized estimation equations are two fundamental approaches for longitudinal trait analyses that have superior properties than cross-sectional analyses. For example, these methods can incorporate time-varying covariates and the correlation structure of repeated measures into analysis. 

## **Linear mixed effect models**

Consider a commonly used Linear mixed effect model for modeling longitudinal data

$$ y_{ij} = X_{ij}^T \beta + G_i \beta_g + Z_{ij}^T \gamma_i + \varepsilon_{ij} $$ 
where $y_{ij}$ is individual $i$'s measurement at occasion $j$.

## **Generalized estimation equations**

aaaaa


