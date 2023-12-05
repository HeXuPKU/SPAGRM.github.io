---
layout: default
title: Step 0 Calculate GRM and IBD files
nav_order: 2
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
has_children: true
has_toc: true
---

# Genetic Relationship Matrix (GRM)

The genetic relationship matrix (GRM) is the covariance matrix calculated from a set of genetic variants of the individuals. As an important ingredient in mixed models, GRM usually represents the genetic relatedness among individuals. When the entries of the GRM below a specified threshold (usually 0.05) are set to zero, the GRM is transformed into a sparse GRM, which is approximately equivalent to a family kinship matrix.

For example, consider a family with four members. The pedigree and the corresponding (sparse) GRM is expected to be:

![GRM](https://raw.githubusercontent.com/Fantasy-XuHe/SPAGRM.github.io/main/docs/assets/images/GRM.png)

SPA<sub>GRM</sub> is a sparse-GRM-based approach to control for sample relatedness. SPA<sub>GRM</sub> uses the sparse GRM to 
- capture close family relationships.
- - aaa
- estimate precise distribution of genotypes.
