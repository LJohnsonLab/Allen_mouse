# Allen Mouse Brain Cell Atlas: Data Acquisition and Downsampling Tutorial

This repository provides a structured workflow for accessing and managing large-scale data from the 10x Mouse Whole Brain Atlas  ([CCN20230722](https://alleninstitute.github.io/abc_atlas_access/notebooks/cluster_annotation_tutorial.html)). The objective is to generate a manageable dataset that preserves biological diversity by retaining rare cell clusters while reducing the footprint of common cell types. The pipeline utilizes Python for data retrieval and R for statistical sampling.

## Project Structure

The workflow operates on the following directory structure:

* `1.programatic_access.ipynb`: Initial setup and metadata retrieval.
* `2.downsample_metadata.qmd`: Statistical analysis and cell selection.
* `3.Download_expression _matrices.ipynb`: Matrix acquisition and object generation.
* `data/`: Storage for the downsampled cell list and final AnnData object.
* `abc_data/`: Local cache directory for raw Allen Atlas data.

## Workflow Description

Execute the following files in sequential order to replicate the dataset creation process.

### 📂 1. System Setup and Metadata Retrieval
**File:** `1.programatic_access.ipynb`

This notebook establishes the connection to the ABC Atlas API. It performs the necessary installations and initializes the `AbcProjectCache` object, which manages the local storage of downloaded files. The script retrieves and saves cell metadata and the the complete `cell_metadata.csv` for the Whole Mouse Brain (WMB) dataset. This metadata serves as the foundational census required for the subsequent sampling strategy.

### 📊 2. Metadata Analysis and Downsampling
**File:** `2.downsample_metadata.qmd`

This Quarto document executes the cell selection logic using R. Because the complete WMB dataset exceeds standard memory constraints, a stratified sampling approach is required. The script analyzes cluster size distributions to identify rare cell types. It retains all cells from clusters with fewer than 50 members and randomly samples remaining clusters to reach a target of approximately 200,000 cells. The result is exported as `data/downsampled_cells.csv`.

### 🧬 3. Expression Matrix Acquisition
**File:** `3.Download_expression _matrices.ipynb`

This script generates the final biological dataset. It reads the list of target cells generated in the previous step and identifies the specific raw expression matrices required (spanning 10Xv2, 10Xv3, and 10XMulti modalities). The code downloads and saves these matrices and then, subsets them in memory immediately to discard unneeded data. It then concatenates the results into a single `AnnData` object, which is saved locally as `downsampled_expression.h5ad` for downstream analysis.

### 🔍 4. Quality Control and Format Conversion
**File:** `4.inspect_downsampledAnndata.ipynb`

This notebook performs comprehensive quality control analysis on the generated AnnData object using scanpy. It inspects the object structure, calculates cell-level QC metrics including genes per cell and UMI count distributions, and validates data integrity. The script then exports the expression matrix to Matrix Market format (.mtx) along with corresponding barcode and feature tables. This conversion enables interoperability with R-based analysis tools, producing `expression_matrix.mtx`, `barcodes.tsv`, `features.tsv`, and associated metadata files in the `data/mtx/` directory.

### 🔬 5. Seurat Object Construction
**File:** `5.buildSeuratObject.qmd`

This Quarto document transitions the workflow into the R ecosystem by constructing a Seurat object from the exported Matrix Market files. It reads the sparse expression matrix and rejoins the downsampled cells with the complete Allen Atlas metadata to restore full cluster annotations, anatomical labels, and experimental details. The resulting Seurat object integrates both gene-level and cell-level metadata and is serialized using the `qs2` package for efficient storage and rapid loading in subsequent R analyses. The final object is saved as `data/allen10X_down200k_seurat.qs`.