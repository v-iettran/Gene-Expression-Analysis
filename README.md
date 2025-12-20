---
editor_options: 
  markdown: 
    wrap: 72
---

# Breast Cancer Gene Expression Analysis

**Author:** Viet Tran - 25216938

## Overview

This analysis performs a comprehensive gene expression study of breast
cancer (BRCA) using data from The Cancer Genome Atlas (TCGA). The
analysis compares gene expression patterns between ERBB2 (gene that
encodes for protein HER2) amplified and non-amplified breast cancer
patients, identifies differentially expressed genes, performs pathway
enrichment analysis, and builds survival prediction models.

## Analysis Workflow

The analysis is organized into 5 main sections:

1.  **Data Preprocessing** - Loading and merging RNA-seq, copy number
    aberration (CNA), and clinical data
2.  **Exploratory Data Analysis** - Visualization of clinical variables
    (age, stage, survival)
3.  **Pathway Enrichment Analysis** - Differential expression analysis
    and functional enrichment
4.  **Heatmap Analysis** - Visualization of top differentially expressed
    genes
5.  **Kaplan-Meier Survival Curve Analysis** - Survival modeling and
    risk prediction

## Prerequisites

### Required R Packages

Install the following R packages before running the analysis:

``` r
# For data manipulation
install.packages(c("dplyr", "tidyr", "ggplot2"))

# For differential expression analysis
if (!require("BiocManager", quietly = TRUE))
    install.packages("BiocManager")
BiocManager::install("DESeq2")

# For pathway enrichment analysis
BiocManager::install(c("clusterProfiler", "enrichplot", "org.Hs.eg.db", 
                       "ReactomePA", "pathview"))

# For visualization
install.packages(c("pheatmap"))

# For survival analysis
install.packages(c("survival", "glmnet"))
```

### Data Requirements

You need the TCGA BRCA Pan-Cancer Atlas 2018 dataset:

-   **File:** `brca_tcga_pan_can_atlas_2018.tar.gz` (which can be
    downloaded from cBioPortal, link below)
-   **Location:** The script expects this file in the directory
    specified by `downloads_path` (default:
    `/Users/luke/Desktop/UCD/Class/Bio Principle` but should be change
    to suit your environment)
-   **Source:** Available from the TCGA data portal or cBioPortal
    (<https://www.cbioportal.org/study/summary?id=brca_tcga_pan_can_atlas_2018>)

The dataset should contain:

-   `data_mrna_seq_v2_rsem.txt`: RNA sequencing expression data

-   `data_clinical_patient.txt`: Clinical patient information

-   `data_cna.txt`: Copy number aberration data

## How to Run

### Option 1: RStudio (Recommended)

1.  Open RStudio
2.  Open the file `25216938_Gene_Expression_Analysis.qmd`
3.  Ensure the `downloads_path` variable (line 19) points to the
    directory containing `brca_tcga_pan_can_atlas_2018.tar.gz`
4.  Click "Knit" or press `Ctrl+Shift+K` (Windows/Linux) or
    `Cmd+Shift+K` (Mac) to render the document

### Option 2: R Console

``` r
# Install required packages first (see Prerequisites)
# Then render the document:
rmarkdown::render("25216938_Gene_Expression_Analysis.qmd")
```

### Option 3: Command Line

``` bash
Rscript -e "rmarkdown::render('25216938_Gene_Expression_Analysis.qmd')"
```

## Configuration

Before running, update the data path if needed:

``` r
# Line 19 in the .qmd file
downloads_path = '/Users/luke/Desktop/UCD/Class/Bio Principle'
```

Change this to match your system's file path where the
`brca_tcga_pan_can_atlas_2018.tar.gz` file is located.

## Analysis Details

### 1. Data Preprocessing

-   Extracts the TCGA data archive
-   Loads RNA-seq, CNA, and clinical data
-   Standardizes patient IDs across datasets (12-character TCGA format)
-   Merges datasets to create a consolidated dataset
-   Filters to patients with data in all three datasets

### 2. Exploratory Data Analysis

Generates histograms for:

-   Age at diagnosis

-   Cancer stage distribution

-   Overall survival times (deceased patients only)

### 3. Pathway Enrichment Analysis

**Differential Expression:**

-   Uses DESeq2 to identify genes differentially expressed between ERBB2
    amplified vs non-amplified patients

-   Filters genes with adjusted p-value \< 0.05

-   Performs variance stabilizing transformation (VST) for visualization

-   Creates PCA plots

**Pathway Enrichment:**

-   **Gene Ontology (GO):** Biological process enrichment for over- and
    under-expressed genes

-   **KEGG:** Pathway enrichment analysis

-   **Reactome:** Comprehensive pathway database enrichment

-   Creates dot plots and tree plots showing enriched pathways

### 4. Heatmap Analysis

-   Visualizes top 20 most significantly differentially expressed genes
-   Uses hierarchical clustering
-   Annotates patients by ERBB2 amplification status

### 5. Survival Analysis

-   **Lasso Cox Regression:** Selects genes predictive of survival using
    cross-validated Lasso with n_folds = 10
-   **Risk Stratification:** Divides patients into high/low risk groups
    based on gene expression
-   **Kaplan-Meier Curves:**
    -   Survival by ERBB2 amplification status
    -   Survival by gene expression risk group
-   **Log-rank Tests:** Statistical tests for survival differences

## Output

The rendered PDF document will contain:

-   All code chunks with their outputs
-   Statistical results tables
-   Visualizations (histograms, PCA plots, enrichment plots, heatmaps,
    survival curves)
-   Interpretations of results

## Key Variables

-   **ERBB2:** HER2/neu gene, an important breast cancer biomarker
-   **CNA \> 0:** Indicates gene amplification (copy number gain)
-   **padj:** Adjusted p-value (FDR correction)
-   **log2FoldChange:** Log2 fold change in expression (ERBB2 amplified
    vs non-amplified)
-   **common_ids:** Patient IDs present in all three datasets

## Notes

1.  **Execution Time:** The analysis may take 10-30 minutes depending on
    your system, especially the DESeq2 and pathway enrichment steps.

2.  **Internet Connection:** Pathway enrichment analyses (KEGG,
    Reactome) require internet connection to query databases.

3.  **Memory Requirements:** The analysis processes large datasets.
    Ensure you have sufficient RAM (recommended: 8GB+).

4.  **Data Path:** Make sure to update the `downloads_path` variable to
    match your system.

5.  **Package Installation:** Some Bioconductor packages may require
    additional system dependencies. If installation fails, check the
    package documentation.

## Troubleshooting

### Error: "Cannot find file"

-   Check that `brca_tcga_pan_can_atlas_2018.tar.gz` exists in the
    specified directory
-   Verify the `downloads_path` is correct

### Error: "Package not found"

-   Install missing packages using `install.packages()` or
    `BiocManager::install()`
-   For Bioconductor packages, ensure `BiocManager` is installed first

### Error: "Out of memory"

-   Close other applications
-   Consider processing data in smaller chunks
-   Increase available RAM if possible

### Pathway enrichment returns empty results

-   Check internet connection
-   Some gene sets may not have significant enrichment - this is normal
-   Try adjusting p-value cutoffs if needed

## Citation

If using this analysis, please cite:

-   TCGA Research Network: <https://www.cancer.gov/tcga>

-   DESeq2: Love, M.I., Huber, W., Anders, S. (2014). Moderated
    estimation of fold change and dispersion for RNA-seq data with
    DESeq2. Genome Biology, 15(12):550.

-   clusterProfiler: Yu, G., et al. (2012). clusterProfiler: an R
    package for comparing biological themes among gene clusters. OMICS,
    16(5):284-287.

## License

This analysis is for educational/research purposes. TCGA data usage
should comply with TCGA data use policies.

## Contact

For questions or issues, contact: Viet Tran (quoc.tran\@ucdconnect.ie)
