# 🧬 Single-Cell RNA-seq Analysis — Tutorial Implementations

This repository contains my implementations of three bioinformatics tutorials focused on **single-cell RNA sequencing (scRNA-seq)** data analysis. The work was done on **Google Colab** using Python-based tools from the [scverse](https://scverse.org/) ecosystem.

Single-cell RNA sequencing allows researchers to measure gene expression in **individual cells** rather than bulk tissue — revealing cell-type diversity, developmental trajectories, and disease states that would otherwise be hidden.

---

## 🎯 Purpose

The goal of these tutorials is to:
- Understand the **end-to-end scRNA-seq analysis pipeline**, from raw sequencing data to biological interpretation
- Learn how to use **Scanpy**, the leading Python toolkit for single-cell analysis
- Understand the **AnnData** data structure that underlies all scverse tools
- Practice **pre-processing raw 10X Genomics data** using Galaxy bioinformatics tools
- Gain hands-on experience running reproducible analysis in **Google Colab**

---

## 📂 Repository Contents

| File | Description |
|---|---|
| `basic-scrna-tutorial_updated.ipynb` | Full Scanpy clustering & annotation pipeline on 3k PBMCs |
| `anndata_getting_started.ipynb` | Exploring the AnnData object structure |
| *(Galaxy tutorial)* | Pre-processing of 10X scRNA data using Galaxy tools |
| `README.md` | This file |

---

## 📚 Tutorial 1 — Getting Started with AnnData

**Source:** [scverse-tutorials.readthedocs.io](https://scverse-tutorials.readthedocs.io/en/latest/notebooks/anndata_getting_started.html)

### What is AnnData?

Before doing any analysis, it is essential to understand **AnnData** — the core data structure used by Scanpy and all scverse tools. Think of it as a smart container that holds not just your gene expression matrix, but all the associated metadata about cells and genes in one organized object.

An AnnData object looks like this:
### Key Components of AnnData

| Slot | What it stores | Example |
|---|---|---|
| `adata.X` | The main gene expression matrix (cells × genes) | Normalized log counts |
| `adata.obs` | Per-cell metadata (rows) | Cell type, QC metrics |
| `adata.var` | Per-gene metadata (columns) | Gene names, highly variable flag |
| `adata.obsm` | Multi-dimensional cell embeddings | PCA, UMAP, t-SNE coordinates |
| `adata.layers` | Alternative versions of the expression matrix | Raw counts, scaled counts |
| `adata.uns` | Unstructured metadata | Color palettes, clustering parameters |

### What This Tutorial Covers

- Loading a `.h5ad` file into Python
- Exploring `adata.X` — the sparse expression matrix and why sparsity matters
- Adding and accessing data `layers` (e.g., raw counts alongside normalized counts)
- Working with `adata.obs` and `adata.var` for cell and gene annotations
- Subsetting the AnnData object to select specific cells or genes
- Understanding `obsm` for reduced-dimension coordinates (PCA, UMAP)

### Commands Used

```python
### 🖼️ Images Generated

**AnnData structure diagram**
> *(Insert screenshot of the AnnData object structure diagram here)*

---

## 📚 Tutorial 2 — Pre-processing of 10X Single-Cell RNA Datasets (Galaxy)

**Source:** [Galaxy Training Network](https://training.galaxyproject.org/training-material/topics/single-cell/tutorials/scrna-preprocessing-tenx/tutorial.html)

### What is Galaxy?

[Galaxy](https://usegalaxy.org/) is a **web-based bioinformatics platform** that lets you run complex analysis tools through a graphical interface — no command line or coding required. It is widely used in genomics for reproducible research.

This tutorial uses Galaxy tools to process **raw 10X Genomics sequencing output** into a clean, analysis-ready count matrix.

### Why Pre-processing Matters

Raw scRNA-seq data comes off the sequencer as billions of short DNA reads (FASTQ files). Before any biological analysis can happen, these reads must be:
1. Mapped to a reference genome to find which gene each read came from
2. Counted per cell using cell barcodes and UMIs
3. Filtered to remove empty droplets and low-quality cells

---

### 🔧 Galaxy Tools Used

---

#### Tool 1 — `Faster Download and Extract Reads in FASTQ format` (fasterq-dump)

**What it does:** Downloads raw sequencing data (FASTQ files) from public databases like NCBI SRA directly into Galaxy. FASTQ files contain the raw sequencing reads along with quality scores for each base.

**Why we need it:** The starting point for any scRNA-seq pipeline is raw read data. This tool fetches it without needing to download large files to a local machine — everything stays in the cloud.

**Input:** SRA accession number (e.g., `SRR9003770`)
**Output:** FASTQ files (Read 1 = cell barcode + UMI, Read 2 = cDNA sequence)

---

#### Tool 2 — `FastQC` — Read Quality Control

**What it does:** Analyzes the quality of raw sequencing reads and produces a visual quality report. It checks for:
- Per-base quality scores (are the bases called confidently?)
- GC content (is it within expected range?)
- Adapter contamination (are sequencing adapters present in reads?)
- Sequence duplication levels

**Why we need it:** Poor-quality reads lead to incorrect alignments and unreliable gene counts. FastQC helps identify problems early before spending time on costly computation.

**Input:** Raw FASTQ files
**Output:** An HTML quality report with plots

🖼️ **FastQC Quality Report**
> *(Insert FastQC per-base quality score plot here)*

---

#### Tool 3 — `STARsolo` — Alignment and Cell Barcode Demultiplexing

**What it does:** Aligns raw reads to a reference genome AND simultaneously identifies which cell each read came from (using cell barcodes) and removes PCR duplicates (using UMIs). It produces a sparse Cell × Gene count matrix directly. STARsolo is specifically optimized for droplet-based platforms like 10X Genomics Chromium.

**Why we need it:** Each read must be matched to:
- A **genomic location** → which gene does this read come from?
- A **cell barcode** → which cell was this RNA molecule from?
- A **UMI** → was this molecule sequenced multiple times (PCR duplicate)?

STARsolo does all three in a single efficient step.

**Key parameters:**
- Reference genome (e.g., human GRCh38)
- 10X Chromium chemistry version (v2 or v3)
- Whitelist of valid 10X cell barcodes

**Input:** FASTQ files + reference genome
**Output:** Sparse matrix folder containing `matrix.mtx`, `barcodes.tsv`, `features.tsv`

🖼️ **Alignment Summary Statistics**
> *(Insert STARsolo alignment log / mapping rate summary here)*

---

#### Tool 4 — Knee Plot — Cell Barcode Filtering

**What it does:** Distinguishes **real cells** from **empty droplets**. In 10X Genomics experiments, most droplets don't contain a cell — they capture only ambient RNA floating in solution. The knee plot ranks all cell barcodes by their total UMI count. Real cells form a clear "knee" (inflection point) in this curve, while empty droplets fall off sharply below it.

**Why we need it:** Without this step, thousands of empty droplets would be treated as cells, creating false clusters and noisy results downstream.

**Input:** The raw (unfiltered) cell × gene matrix from STARsolo
**Output:** A filtered matrix containing only real cells above the knee

🖼️ **Knee Plot — Distinguishing Cells from Empty Droplets**
> *(Insert barcode rank plot / knee plot here — x-axis: barcode rank, y-axis: total UMI count)*

---

#### Tool 5 — `MultiQC` — Aggregated Quality Report

**What it does:** Combines quality reports from multiple tools (FastQC, STARsolo, etc.) into a single interactive HTML report. When analyzing multiple samples, it lines them up side-by-side so you can spot outliers or batch effects at a glance.

**Why we need it:** Reviewing individual FastQC reports for dozens of samples is tedious and error-prone. MultiQC aggregates everything into one clean summary.

**Input:** Output reports from FastQC and STARsolo
**Output:** A single interactive HTML report comparing all samples

🖼️ **MultiQC Summary Report**
> *(Insert MultiQC aggregated report screenshot here)*

---

## 📚 Tutorial 3 — Basic scRNA-seq Analysis with Scanpy (3k PBMCs)

**Source:** [scverse/scanpy-tutorials](https://github.com/scverse/scanpy-tutorials/blob/main/basic-scrna-tutorial.ipynb)
**My updated notebook:** [basic-scrna-tutorial_updated.ipynb](https://github.com/tahashmi/algos/blob/main/basic-scrna-tutorial_updated.ipynb)

### Dataset

The **3,000 Peripheral Blood Mononuclear Cells (PBMCs)** dataset from 10X Genomics. PBMCs are a mixture of immune cell types including T cells, B cells, NK cells, and monocytes — making them an ideal benchmark dataset because the expected cell types are well-known.

---

### Step-by-Step Analysis

---

#### Step 1 — Installation and Setup

```python
---

✅ Conclusion

Through these three tutorials, a complete scRNA-seq analysis pipeline has been implemented — from raw sequencing reads to annotated cell types. Key takeaways:

- **AnnData** is the backbone of the scverse ecosystem — understanding its structure makes all downstream tools easier to use
- **Pre-processing quality matters** — poor filtering in the Galaxy step leads to false clusters downstream
- **Scanpy** provides a clean Python API for the full analysis pipeline
- **Parameters require judgment** — QC thresholds, number of PCs, and clustering resolution all need biological reasoning, not just defaults
- **Google Colab** makes this accessible without local installation, but requires re-installing packages every session

---

## 📖 Sources and References

1. Wolf et al. *SCANPY: large-scale single-cell gene expression data analysis.* Genome Biology (2018). https://doi.org/10.1186/s13059-017-1382-0
2. Virshup et al. *The scverse project provides a computational ecosystem for single-cell omics data analysis.* Nature Biotechnology (2023). https://doi.org/10.1038/s41587-023-01733-8
3. Basic scRNA-seq Tutorial — https://github.com/scverse/scanpy-tutorials/blob/main/basic-scrna-tutorial.ipynb
4. AnnData Getting Started — https://scverse-tutorials.readthedocs.io/en/latest/notebooks/anndata_getting_started.html
5. Galaxy 10X Pre-processing Tutorial — https://training.galaxyproject.org/training-material/topics/single-cell/tutorials/scrna-preprocessing-tenx/tutorial.html
6. PBMC 3k Dataset — https://www.10xgenomics.com/resources/datasets
7. Traag et al. *From Louvain to Leiden.* Scientific Reports (2019). https://doi.org/10.1038/s41598-019-41695-z
8. McInnes et al. *UMAP: Uniform Manifold Approximation and Projection.* arXiv (2018). https://arxiv.org/abs/1802.03426

---

## 👤 Author

**Taha Shmi**
Bioinformatics Assignment — Single-Cell RNA-seq Analysis
Implemented on Google Colab | Tutorials from scverse and Galaxy Training Network
