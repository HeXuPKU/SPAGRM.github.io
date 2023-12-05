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

SPA<sub>GRM</sub> is a sparse-GRM-based approach to 