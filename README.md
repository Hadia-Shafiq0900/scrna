# 🧬 Single-Cell RNA-seq Analysis using 10X Data

---

## 📖 Introduction
Single-cell RNA sequencing (scRNA-seq) allows the study of gene expression at the resolution of individual cells, enabling the identification of cellular heterogeneity and functional states.

In this project, I performed preprocessing and basic analysis of a **10X Genomics scRNA-seq dataset** using:
- Galaxy workflow (for initial preprocessing)
- Python-based analysis (AnnData / Scanpy)

The workflow strictly follows established tutorials to ensure correctness and reproducibility.

---

## 📑 Table of Contents
- [Introduction](#-introduction)
- [Purpose](#-purpose)
- [Tutorials Followed](#-tutorials-followed)
- [Project Structure](#-project-structure)
- [Task 1: Galaxy Workflow](#-task-1-galaxy-workflow)
- [Task 2: Python (Scanpy / AnnData Analysis)](#-task-2-python-scanpy--anndata-analysis)
- [Results](#-results)
- [Reproducibility](#-reproducibility)
- [References](#-references)

---

## 🎯 Purpose
- Understand scRNA-seq preprocessing  
- Learn 10X data handling  
- Perform QC, filtering, normalization  
- Visualize data using PCA & UMAP  
- Understand AnnData structure  

---

## 📚 Tutorials Followed

- Pre-processing of 10X Single-Cell RNA Datasets  
- Basic scRNA-seq Tutorial (updated)  
- AnnData Tutorials:  
  - https://anndata.readthedocs.io/en/latest/tutorials/notebooks/getting-started.html  
  - https://scverse-tutorials.readthedocs.io/en/latest/notebooks/anndata_getting_started.html  

---

## 📂 Project Structure
├── data/
├── galaxy_outputs/
├── notebooks/
├── results/
│ ├── plots/
│ ├── figures/
└── README.md

---

# 🔬 Task 1: Galaxy Workflow

## 🧪 Steps Performed in Galaxy

1. Uploaded 10X dataset  
2. Performed quality control  
3. Filtered cells and genes  
4. Normalized data  
5. Generated intermediate outputs  

## 📊 Outputs
- Filtered expression matrix  
- QC metrics  
- Processed dataset  

(All output files are available in the `galaxy_outputs/` folder)

---

# 💻 Task 2: Python (Scanpy / AnnData Analysis)

## ⚙️ Environment Setup

```bash
pip install scanpy anndata matplotlib numpy
Load Data
import scanpy as scadata = sc.read_10x_mtx("data/", var_names='gene_symbols', cache=True)adata
```
## Quality Control##
```
adata.var['mt'] = adata.var_names.str.startswith('MT-')sc.pp.calculate_qc_metrics(adata, qc_vars=['mt'], inplace=True)sc.pl.violin(adata, ['n_genes_by_counts', 'total_counts', 'pct_counts_mt'], multi_panel=True)
```
🧹 Filtering
```
sc.pp.filter_cells(adata, min_genes=200)sc.pp.filter_genes(adata, min_cells=3)adata = adata[adata.obs.pct_counts_mt < 5, :]
```
📏 Normalization
```
sc.pp.normalize_total(adata, target_sum=1e4)sc.pp.log1p(adata)
```
⭐ Highly Variable Genes
```
sc.pp.highly_variable_genes(adata, min_mean=0.0125, max_mean=3, min_disp=0.5)adata = adata[:, adata.var.highly_variable]
```
⚖️ Scaling
```
sc.pp.scale(adata, max_value=10)
```
📉 PCA
```
sc.tl.pca(adata, svd_solver='arpack')sc.pl.pca(adata)
```
🌐 Neighborhood Graph
```
sc.pp.neighbors(adata, n_neighbors=10, n_pcs=40)
```

🧩 Clustering
```
sc.tl.leiden(adata)
```
🗺️ UMAP Visualization
UMAP (Uniform Manifold Approximation and Projection) shows how similar cells group together based on their gene expression profiles.
Each point represents a cell, and clusters indicate cells with similar biological states or types.
Closer points = more similar cells; separated clusters = distinct cell populations.
```
sc.tl.umap(adata)sc.pl.umap(adata, color=['leiden'])
```
<img width="434" height="142" alt="image" src="https://github.com/user-attachments/assets/fcd791a9-82f9-491a-88af-df50f1002ed3" />

<img width="438" height="141" alt="image" src="https://github.com/user-attachments/assets/19c344fd-29f6-4d3b-9e66-7136b54d4442" />

<img width="163" height="140" alt="image" src="https://github.com/user-attachments/assets/7e65e71a-0089-46c3-b84a-d06ee5446497" />

<img width="184" height="148" alt="image" src="https://github.com/user-attachments/assets/919eb92c-aee8-4038-b5aa-
081df334294d" />

<img width="705" height="191" alt="image" src="https://github.com/user-attachments/assets/c4e3a19c-ff0e-456d-a40a-fa39fc2a2acc" />

<img width="380" height="276" alt="image" src="https://github.com/user-attachments/assets/0760b1bb-a2a9-448b-b9a6-06fe3abf8209" />


## quality control

<img width="815" height="264" alt="image" src="https://github.com/user-attachments/assets/3a9992d2-130f-4799-9720-c8d14e4128b0" />

<img width="269" height="245" alt="image" src="https://github.com/user-attachments/assets/3269d9c5-f4a1-42a1-8932-3d30a18720d2" />

## Graphs

<img width="410" height="212" alt="image" src="https://github.com/user-attachments/assets/fafb820a-f161-4bf7-8d42-38f99c91182b" />

## variance ratio
<img width="209" height="230" alt="image" src="https://github.com/user-attachments/assets/cc406643-134a-4376-a827-806e85adbe47" />

## final results

<img width="820" height="230" alt="image" src="https://github.com/user-attachments/assets/384dc3dd-0d33-46fa-8e4d-943712da1c16" />
<img width="442" height="266" alt="image" src="https://github.com/user-attachments/assets/0d847e4e-76fa-4252-8e18-723f71791b5b" />



##🔁 Reproducibility
This project is reproducible because:


Standard tutorials were followed exactly


Same commands and parameters were used


Galaxy outputs are included


Python workflow is documented




## 📚 References


https://anndata.readthedocs.io/en/latest/tutorials/notebooks/getting-started.html


https://scverse-tutorials.readthedocs.io/en/latest/notebooks/anndata_getting_started.html


Pre-processing of 10X Single-Cell RNA Datasets


Basic scRNA-seq Tutorial (updated)




