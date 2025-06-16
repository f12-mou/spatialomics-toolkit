# Spatial Omics Analysis with `spatialdata`: MERFISH & Visium Workflows

This repository provides well-documented Jupyter notebooks for analyzing **MERFISH** and **Visium 2.1.0** spatial transcriptomics data using the [`spatialdata`](https://github.com/scverse/spatialdata) toolkit. It supports both:

- ✅ **Raw data formats** (unsandboxed) → processed via [`spatialdata-sandbox`](https://github.com/scverse/spatialdata-sandbox)
- ✅ **Pre-sandboxed datasets** → ready for direct use with `spatialdata`

These notebooks were created as part of a tutorial for an academic seminar and demonstrate a **general-purpose analysis pipeline**.

---

## Getting Started (Recommended: Google Colab)

You can run the notebooks on your local machine or **Google Colab**. For your local machine, create a directory named 'content' or adjust the path parameters shown below.

### ▶️ Open in Colab

> Before running the notebook:
> - Go to **Runtime > Change runtime type** → Select `Python 3` and GPU as `T4`.
> - Install necessary packages from the first few cells.
> - Follow in-notebook **restart instructions** after installing packages (especially `spatialdata` and `dask`).

---



##  Data Sources

This repository demonstrates workflows on two major types of spatial transcriptomics datasets:

| Dataset         | Description                                                   | Species | Format        | Source                                                                                 |
|------------------|---------------------------------------------------------------|---------|---------------|----------------------------------------------------------------------------------------|
| **MERFISH**       | MERFISH dataset covering mouse hypothalamic region            | Mouse   | `.zarr`        | Moffitt et al. (2018) [Science](https://doi.org/10.1126/science.aau5324)               |
| **Visium 2.1.0**  | 10x Genomics Visium data from human brain cancer (FFPE)       | Human   | `.h5ad` + raw  | [10x Genomics Dataset Portal](https://www.10xgenomics.com/resources/datasets)         |

Both datasets illustrate the flexibility of the `spatialdata` toolkit:

- **MERFISH**: already in `.zarr` format, directly usable with `spatialdata`
- **Visium 2.1.0**: requires conversion using [`spatialdata-sandbox`](https://github.com/scverse/spatialdata-sandbox)

## 📚 Citation

If you use this repository or adapt the code, please cite the original data sources:

### MERFISH

**Moffitt, J. R., Bambah-Mukku, D., Eichhorn, S. W., Vaughn, E., Shekhar, K., Perez, J. D., ... & Zhuang, X.** (2018).  
*Molecular, spatial, and functional single-cell profiling of the hypothalamic preoptic region.*  
**Science**, 362(6416), eaau5324. [https://doi.org/10.1126/science.aau5324](https://doi.org/10.1126/science.aau5324)

### Visium 2.1.0

**10x Genomics**  
*Visium Spatial Gene Expression for FFPE — Human Brain Cancer*  
Dataset retrieved from: [10x Genomics Dataset Portal](https://www.10xgenomics.com/resources/datasets)

---

## What the Notebook Does (Pipeline Overview)

Each notebook guides you through a reproducible pipeline for spatial transcriptomics analysis. Below is a breakdown of each step, with **inputs and outputs** clearly defined.

---

### ✅ Step 1: Environment Setup

**Purpose:** Install all required packages and ensure compatibility with Colab (especially for `dask`, `spatialdata`, and I/O issues).

- **Input:** None  
- **Output:** Clean Python environment with all necessary libraries installed

---

### ✅ Step 2: Data Download & Extraction

**Purpose:** Automatically download the dataset archive (`.zip`) from a specified URL and extract its contents.

- **Input:**  
  - `DATA_URL` (e.g., MERFISH dataset from EMBL)

- **Output:**  
  - Extracted dataset folder (e.g., `.zarr`, `.h5ad`, `.tif`, or related metadata)  
  - Paths like `/content/merfish_unzipped/` become available

*You can change the input URL to work with other similar spatial omics datasets*

---

### ✅ Step 3: Load SpatialData Object

**Purpose:** Load the dataset using `spatialdata.read_zarr()` or `read_image()` into a `SpatialData` object.

- **Input:**  
  - Path to extracted dataset (e.g., `.zarr` folder or `.h5ad` file)

- **Output:**  
  - A `SpatialData` object `sdata` with internal components:
    - `.images`
    - `.labels`
    - `.table`
    - `.shapes`

*The object allows flexible access to spatial coordinates and gene expression*

---

### ✅ Step 4: Gene List Export

**Purpose:** Extract all gene names from the `AnnData` object inside `sdata.table` and save them as a CSV file.

- **Input:**  
  - `sdata.table` (accessed automatically from the loaded spatial object)

- **Output:**  
  - 📄 `all_genes.csv` — list of all detected genes

---

### ✅ Step 5: Spatial Coordinates Plot

**Purpose:** Plot the x–y coordinates of all cells/spots for a general spatial layout.

- **Input:**  
  - `sdata.table` — columns for `x`, `y` coordinates

- **Output:**  
  - 📊 `scatter_plot.png` — a spatial scatter plot of the sample

*Size and transparency are adjustable via parameters like `SCATTER_POINT_SIZE` and `SCATTER_ALPHA`*

---

### ✅ Step 6: Gene Expression Visualization

**Purpose:** Visualize the expression of a **single gene** as a heatmap in spatial coordinates.

- **Input:**  
  - Gene name string (e.g., `"Asic4"` via `EXAMPLE_GENE`)

- **Output:**  
  - 📊 Heatmap image saved as  
    `gene_plots/Asic4_heatmap.png`

*This step demonstrates how to query and plot any gene of interest in the spatial context*

---

### ✅ Step 7: Joint Expression Analysis

**Purpose:** For a user-defined list of genes, compute a **joint expression score** per cell/spot and visualize it as a heatmap.

- **Input:**  
  - CSV file of candidate genes  
    📄 `candidate_genes.csv`

- **Output:**  
  - 📄 `joint_expression_scores.csv` — average expression score per spot  
  - 📊 `joint_expression_heatmap.png` — spatial heatmap of joint expression

*Useful for summarizing multiple marker genes or signature scores across the tissue*

---



## 🔧 Parameter Configuration (Highly Customizable)

Each notebook begins with a **Parameter Configuration Cell**, where you can define paths, filenames, and analysis parameters. This makes the notebooks easily reusable for your own datasets with minimal changes.

```python
# ====== PARAMETERS / CONFIGURATION ======

# Dataset URL (for MERFISH or other zipped datasets)
DATA_URL = "https://s3.embl.de/spatialdata/spatialdata-sandbox/merfish.zip"  # Replace with desired dataset

# Paths for downloaded and unzipped data
DATA_ZIP_PATH = "/content/merfish.zip"
DATA_UNZIP_DIR = "/content/merfish_unzipped"

# Gene list and output files
ALL_GENES_CSV = "/content/all_genes.csv"                      # Output: All detected genes in dataset
CANDIDATE_GENE_LIST_PATH = "/content/candidate_genes.csv"     # Input: Custom gene list for joint expression
JOINT_EXPR_CSV = "/content/joint_expression_scores.csv"       # Output: CSV with joint expression + coordinates

# Output directories and plots
GENE_PLOTS_DIR = "/content/gene_plots/"                       # Folder for individual gene heatmaps
SCATTER_PLOT_PATH = "/content/scatter_plot.png"               # Spatial scatter plot of all spots
JOINT_EXPR_PLOT_PATH = "/content/joint_expression_heatmap.png"  # Heatmap of joint expression

# ⚙Visualization parameters
SCATTER_POINT_SIZE = 5
SCATTER_ALPHA = 0.6

# Example gene for demo visualization
EXAMPLE_GENE = "Asic4"
EXAMPLE_GENE_HEATMAP = "/content/gene_plots/Asic4_heatmap.png"

---





