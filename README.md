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