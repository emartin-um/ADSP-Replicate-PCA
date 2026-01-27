# Technical Replicates PCA Analysis - Quick Guide

## Overview

This analysis examines PCA results from technical replicates in ADSP R5 to assess technical variation and sequencing quality.

## Files

- **ADSP_R5_Tech.Rmd** - Main analysis script
- **data/TECH_pruned_evec.csv** - PCA eigenvectors (with eigenvalues in first row)
- **data/r5_replicate_covariates_TechReps.csv** - Sample covariate information
- **processed_tech_pca_data.csv** - Processed PCA data (auto-generated)
- **tech_rep_spreads.rds** - Cached SD calculations (auto-generated, delete to recalculate)

## Running the Analysis

### Option 1: RStudio
1. Open `ADSP_R5_Tech.Rmd` in RStudio
2. Click "Knit" button to generate HTML report

### Option 2: Command Line
```bash
# Generate HTML report
Rscript -e "rmarkdown::render('ADSP_R5_Tech.Rmd')"

# Or generate markdown only
Rscript -e "knitr::knit('ADSP_R5_Tech.Rmd')"
```

## Customizing Parameters

Edit these parameters at the top of the code (Section A.3.1):

### File Paths
```r
pca_file <- "data/TECH_pruned_evec.csv"
covariate_file <- "data/r5_replicate_covariates_TechReps.csv"
```

### Analysis Thresholds
```r
# Threshold for flagging technical issues in early PCs
# Default: 1e-4 (0.0001)
# Increase if too many false positives (e.g., 1e-3)
sd_threshold_early_pcs <- 1e-4

# Threshold for flagging issues in replicate split region
# Default: 0.1
# Decrease to be more stringent (e.g., 0.05)
sd_threshold_split_region <- 0.1

# Threshold for flagging issues in late PCs
# Default: 0.02
sd_threshold_late_pcs <- 0.02

# Percentile for highlighting in plots
# Default: 0.90 (top 10%)
top_percentile_cutoff <- 0.90

# PC range for zoomed scree plot
zoom_pc_min <- 6
zoom_pc_max <- 20
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
- DX_harmonized, Age_harmonized, APOE_reported, PCR.free, Technical_Replicate

**PCR.free column values:**
- Expected values: "PCRFree" or "PCRAmplified"
- Will be displayed as "PCR-free" or "PCR-amplified" in plots

## Output Sections

1. **Data Description** - Sample and replicate group summary
2. **Scree Plots** - Variance explained by each PC
3. **Replicate Splits** - Within-group variation analysis (early PCs, split region, late PCs)
4. **Eigenvector Plots** - PC scatter plots with distinct shapes and colors per replicate group
5. **Summary** - Overall quality assessment and key observations

## Key Metrics

### For Technical Replicates:
- **Early PCs (1-5)**: Should show near-zero variation (SD < 1e-4)
- **Replicate Split Region (6-16)**: Where within-group technical variation becomes observable
- **Late PCs (17+)**: Should show low variation (SD < 0.02)
- **Replicate Split**: Expected around PC (n_groups - 1)
- **Quality Assessment**:
  - Excellent: 0 groups with high SD
  - Very Good: < 10% groups with high SD
  - Good: < 25% groups with high SD
  - Variable: ≥ 25% groups with high SD

## Key Findings

- **Library prep (PCR-amplified vs PCR-free) has no detectable effect** on PC coordinates. Within TECH1, TECH2, and TECH3 (which contain both prep types), PCR-amplified samples are never outliers across all 71 PCs.
- **PC1 separation** between TECH1/TECH2/TECH3 and TECH4/TECH5/TECH6 is confounded with genetic relatedness (the first three are from the same family) and cannot be attributed to library prep.
- **Study and Sequencing Center** combinations (not library prep) drive within-group variation in the replicate split region.

## Troubleshooting

### "No replicate groups found"
- Check that "Rep" or "REP" column exists in PCA file
- Verify replicate groups have 2+ samples

### "Samples missing covariates"
- Some samples in PCA file are not in covariate file
- Check SampleID matching between files

### "Cache issues"
Delete the cache file to force recalculation:
```bash
rm tech_rep_spreads.rds
```

## Expected Behavior

Technical replicates should:
1. Show **minimal variation** in early PCs (same biological sample)
2. **Cluster tightly** in PC plots
3. Show variation mainly due to **technical factors**:
   - Study and Sequencing Center combinations
   - Sequencing round differences
   - Library prep differences were not detected in this dataset
