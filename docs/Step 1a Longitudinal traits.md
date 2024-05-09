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

We let $n$ denote the number of subjects, $m_i$ denote the number of measurements for subject $i$, and $m=\sum_{i=1}^n m_i$ denote the total number of measurements for all subjects. Consider a commonly used Linear mixed effect model for modeling longitudinal data

$$ y_{ij} = X_{ij}^T \beta + G_i \beta_g + Z_{ij}^T \gamma_i + \varepsilon_{ij} $$ 

where $y_{ij}$ is individual $i$'s measurement at occasion $j$, $X_{ij} (p\times1)$ and $Z_{ij} (q\times1)$ are two vector of covariates with fixed coefficient $\beta$ and random coefficient $\gamma_i$. $G_i$ is the genotype of a single variant with genetic effect $\beta_g$. Random term $\varepsilon_{ij}$ follows a normal distribution with a mean of zero and a standard error of $\sigma_{\varepsilon_{ij}}$.

Linear mixed effect models are subject-specific methods that consider the correlation structure of repeated measures via the random coefficient $\gamma_i$. In GWAS, we scan te genome to test if $\beta_g=0$ (i.e. test if a genetic variant alter the mean level of the biomarker). Many useful tools can fit the above model (e.g. [lme4](https://cran.r-project.org/web/packages/lme4/index.html) or [optimx](https://cran.r-project.org/web/packages/optimx/index.html) in R).

Recently, TrajGWAS method introduced a mixed-effects multiple location scale model into large-scale longitudinal trait GWAS. Building on regular linear mixed effect models, they further formulated $\sigma_{\varepsilon_{ij}}$ via

$$ \sigma_{\varepsilon_{ij}} = exp(W_{ij}^T \tau + G_i \tau_g + w_i) $$

The standard error $\sigma_{\varepsilon_{ij}}$ is determined by covariate vector $W_{ij}$, genotype $G_i$, and a random intercept $w_i$. This additional formula allows TrajGWAS to test genetic effects on within-subject (WS) variability (i.e. $\tau_g$), which is also an important risk factor for complex diseases. The Julia package [WiSER](https://github.com/OpenMendel/WiSER.jl) can fit the above model.

## **Generalized estimation equations**

Compared to linear mixed effect models, generalized estimation equations are another popular method for modeling longitudinal data. Generalized estimation equations are population average models that consider the correlation structure of repeated measures via various user-defined correlation structures. Use the same legend, a classic GEE model can be

$$ y_{ij} = X_{ij}^T \beta + G_i \beta_g + \varepsilon_{ij} $$ 

where without random terms, generalized estimation equations assume $\varepsilon_{ij}$ follows a multivariate normal distribution with a mean of vector of zeros and a working correlation matrix. Commonly used correlation structures can be independent, exchangeable, autoregressive, and unstructured working correlation matrix. More details about this can be seen [elsewhere](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC6433418/). In GWAS, we scan te genome to test if $\beta_g=0$ (i.e. test if a genetic variant alter the mean level of the biomarker). Many useful tools can fit the above model (e.g. [gee](https://cran.r-project.org/web/packages/gee/gee.pdf) or [geepack](https://cran.r-project.org/web/packages/geepack/index.html) in R).
