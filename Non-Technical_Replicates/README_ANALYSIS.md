# Non-Technical Replicates PCA Analysis - Quick Guide

## Overview

This analysis examines PCA results from non-technical replicates in ADSP R5 to evaluate biological variation, population structure, and technical replication quality across sequencing rounds. Non-technical replicates are distinct biological samples from related individuals identified through identity-by-descent (IBD) analysis.

## Files

- **ADSP_R5_Non_Tech_Replicates.Rmd** - Main analysis script
- **data/Non-TECH_pruned_evec.csv** - PCA eigenvectors (with eigenvalues in first row)
- **data/r5_replicate_covariates.csv** - Sample covariate information
- **processed_pca_data.csv** - Processed PCA data (auto-generated)
- **rep_spreads_hold.rds** - Cached SD calculations (auto-generated, delete to recalculate)

## Running the Analysis

### Option 1: RStudio
1. Open `Non-Technical_Replicates.Rproj` in RStudio
2. Open `ADSP_R5_Non_Tech_Replicates.Rmd`
3. Click "Knit" button to generate HTML report

### Option 2: Command Line
```bash
# Generate HTML report
Rscript -e "rmarkdown::render('ADSP_R5_Non_Tech_Replicates.Rmd')"

# Or generate markdown only
Rscript -e "knitr::knit('ADSP_R5_Non_Tech_Replicates.Rmd')"
```

## Expected Input Format

### PCA File (CSV)
- **Row 1**: Header with eigenvalues starting from column 4
  - Format: `ID,Rep,index,eigenval1,eigenval2,...`
- **Rows 2+**: Sample data
  - Format: `SampleID,RepGroupID,index,PC1,PC2,...`

### Covariate File (CSV)
**Required columns:**
- SampleID, Cohort_Site, Study, Round, Sex, Race, Ethnicity

**Optional columns:**
- DX_harmonized, Age_harmonized, Age_baseline, APOE_reported

## Replicate Groups

- **1013 Groups** (all include at least one Round 5 sample)
  - 889 Pairs
  - 116 Trios
  - 8 Quads
- **2158 Total Samples**

## Output Sections

1. **Data Description** - Sample and replicate group summary, covariate coding
2. **Scree Plots** - Variance explained by each PC (full and zoomed views)
3. **Replicate Splits** - Within-group SD analysis across PC regions
4. **Eigenvector Plots** - PC scatter plots with cluster identification
5. **Summary** - Key findings and quality assessment

## Key Findings

- **Population Structure**: PC1-PC3 capture race and ethnicity differences (Asian ancestry on PC1, Black-to-White gradient on PC2, Native American ancestry on PC3).
- **Family Structure**: PCs 4-25 resolve family clusters and relatedness. Quads separate by PC14, followed by trios and pairs in higher PCs.
- **Replicate Split**: Within-group SDs remain near zero through PC1012. Splits begin at PC1013, matching the expected threshold of (n_groups - 1 = 1012).
- **Technical Replication Quality**: The replicate split pattern indicates excellent technical replication across sequencing rounds.

## Troubleshooting

### "Samples missing covariates"
- Some samples in PCA file are not in covariate file
- Check SampleID matching between files

### "Cache issues"
Delete the cache file to force recalculation:
```bash
rm rep_spreads_hold.rds
```

## Expected Behavior

Non-technical replicates should:
1. Show **population structure** in early PCs (race, ethnicity, ancestry)
2. Show **family structure** in intermediate PCs (relatedness among group members)
3. Show **near-zero within-group SD** until the replicate split region
4. **Split at approximately PC (n_groups - 1)**, where individual replicate groups begin to separate
