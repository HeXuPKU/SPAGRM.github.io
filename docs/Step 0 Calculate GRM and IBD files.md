---
layout: default
title: Step 0 Calculate GRM and IBD files
nav_enabled: true
nav_order: 2
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
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

# **Genetic Relationship Matrix (GRM)**

The genetic relationship matrix (GRM) is the covariance matrix calculated from a set of genetic variants of the individuals. As an important ingredient in mixed models, GRM usually represents the genetic relatedness among individuals. When the entries of the GRM below a specified threshold (usually 0.05) are set to zero, the GRM is transformed into a sparse GRM, which is approximately equivalent to a family kinship matrix.

For example, consider a family with four members. The pedigree and the corresponding (sparse) GRM is expected to be:

![GRM](https://raw.githubusercontent.com/Fantasy-XuHe/SPAGRM.github.io/main/docs/assets/images/GRM.png)

SPA<sub>GRM</sub> is a sparse-GRM-based approach to control for sample relatedness. SPA<sub>GRM</sub> uses the sparse GRM to 
- Capture close family relationships.
  - Two individuals who have a non-zero element in sparse GRM is assumed in a family, and families are indipedent to each other.
- Estimate precise distributions of genotypes.
  - GRM can be used to estimate the variance of genotypes. But it's limited to estimate more precise distributions of genotypes.
  - To understand this, just see the above example. The kinship coefficient (half of the corrsponding element in GRM) for `N-1` and `N-3` is 0.25, and the the kinship coefficient for `N-3` and `N-4` is also 0.25. The kinship coefficient for these two pairs are same, but they are genetically different!
  - Therefore, we introduce identity by descent sharing probabilities, which can exactly describe the genotype distribution between two subjects.

# **Identity by Descent (IBD)**

At a genetic locus, alleles that are inherited copies of a common ancestral allele are said to be identical by descent. The term identical by descent is
generally used for referring to recent, rather than ancient, common ancestry. IBD-sharing probabilities are widely used in genetic analyses of samples with related individuals. 

Denote $\delta_{ij}^{(0)}$, $\delta_{ij}^{(1)}$, and $\delta_{ij}^{(2)}$ to be the probabilities that two subjects _i_ and _j_ share 0, 1, and 2 alleles identical by decent, respectively, at a locus. $\Phi_{ij}$ is the kinship coefficient for _i_ and _j_ (half of the corrsponding element in GRM). Then the kinship coefficient and IBD-sharing probabilities have the function

$$ \Phi_{ij} = 1/2 \times \delta_{ij}^{(2)} + 1/4 \times \delta_{ij}^{(1)} $$

To understand this, just see below examples.

- `N-1` and `N-3` always share one allele in a genetic locus, so that their IBD-sharing probabilities are $\delta_{ij}^{(0)} = 0$, $\delta_{ij}^{(1)} = 1$, and $\delta_{ij}^{(2)} = 0$. The kinship coefficient is $1/2 \times 0 + 1/4 \times 1 = 0.25$.
  - If genotype of `N-1` is <code style="color : fuchsia">AA</code> in one locus, then `N-3` must have at least one <code style="color : fuchsia">A</code> in this locus, regardless of other circumstances.
  - If genotype of `N-1` is <code style="color : fuchsia">aa</code> in one locus, then `N-3` must have at least one <code style="color : fuchsia">a</code> in this locus, regardless of other circumstances.

- `N-3` and `N-4`'s IBD-sharing probabilities are $\delta_{ij}^{(0)} = 0.25$, $\delta_{ij}^{(1)} = 0.5$, and $\delta_{ij}^{(2)} = 0.25$. The kinship coefficient is $1/2 \times 0.25 + 1/4 \times 0.5 = 0.25$.
  - If genotypes of `N-1` and `N-2` are both <code style="color : fuchsia">Aa</code> in one locus, then there is a 0.25 probability that the genotype of `N-3` is <code style="color : fuchsia">AA</code>, a 0.5 probability that `N-3` is <code style="color : fuchsia">Aa</code>, and a 0.25 probability that `N-3` is <code style="color : fuchsia">aa</code>, so as `N-4`. Therefore, `N-3` and `N-4` have a 0.25 probability of sharing two alleles, a 0.5 probability of sharing one allele, and a 0.25 probability of sharing no alleles in one locus.
