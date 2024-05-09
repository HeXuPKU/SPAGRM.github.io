---
layout: default
title: Generalized estimation equations
nav_order: 2
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
parent: Longitudinal traits
grand_parent: Step 1 Fit the null model
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

# **Generalized estimation equations**

We display how to use [geepack](https://cran.r-project.org/web/packages/geepack/index.html), a R package to fit the null model for longitudinal traits. In this online tutorial, our main focus is to demonstrate how to obtain model residuals from the fitted null model.
