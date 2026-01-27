# ADSP-Replicate-PCA

PCA-based quality control analyses of replicate samples from the Alzheimer's Disease Sequencing Project (ADSP) Release 5 whole-genome sequencing data.

## Overview

This repository contains R Markdown analyses that evaluate replicate concordance and identify sources of variation (biological, technical, and population structure) using principal component analysis (PCA) of replicate samples identified through identity-by-descent (IBD) analysis.

## Projects

### [Technical_Replicates](Technical_Replicates/)
Analysis of **6 technical replicate groups** (same biological samples sequenced multiple times). Evaluates sequencing reproducibility and assesses the effect of library preparation method (PCR-amplified vs. PCR-free) on PCA coordinates.

- See [Technical_Replicates/README_ANALYSIS.md](Technical_Replicates/README_ANALYSIS.md) for details.

### [Non-Technical_Replicates](Non-Technical_Replicates/)
Analysis of **1013 non-technical replicate groups** (related individuals identified via IBD: 889 pairs, 116 trios, 8 quads). Evaluates population structure, family structure, and technical replication quality across sequencing rounds.

- See [Non-Technical_Replicates/README_ANALYSIS.md](Non-Technical_Replicates/README_ANALYSIS.md) for details.

## PCA Protocol

PCA was conducted following the ADSP QC Working Group protocol:

1. Select variants that are polymorphic among replicates, have no genotype missingness, MAF > 0.05, and pass QC filters (GLNexusPass=1, CallBad=0, HiDepth=0)
2. LD prune selected variants (r2 threshold of 0.8)
3. Conduct PCA using EIGENSOFT/EIGENSTRAT to obtain all eigenvectors and eigenvalues

## Data

Input data files (PCA eigenvectors and sample covariates) are not tracked in this repository due to size. Each project subdirectory contains a `data/` folder where input files should be placed. See the individual README_ANALYSIS.md files for expected file formats.

## Requirements

- R (>= 4.0)
- R packages: rmarkdown, knitr, dplyr, ggplot2, tidyr, DT, RColorBrewer
