# Single-Cell RNA-seq Analysis of Oral Squamous Cell Carcinoma (OSCC)

## Project Overview

This project focuses on the analysis of single-cell RNA sequencing (scRNA-seq) data from Oral Squamous Cell Carcinoma (OSCC) to investigate the cellular composition and molecular heterogeneity of the tumor microenvironment. The analysis aims to identify and characterize different cell populations within OSCC, explore gene-expression patterns across cell types, and investigate biological pathways and cellular interactions associated with tumor progression.
The project is being developed as a step-by-step computational biology workflow using R and Seurat, with subsequent analysis and visualization using Python where appropriate.    

This project focuses on the analysis of single-cell RNA sequencing (scRNA-seq) data from oral squamous cell carcinoma (OSCC) using the publicly available GSE198315 dataset from the NCBI Gene Expression Omnibus (GEO). GSE198315 contains single-cell RNA-seq data from 268,131 cells across 36 samples from 10 patients with HPV-negative metastatic OSCC. The samples represent multiple anatomical regions, including tumor core, tumor periphery, adjacent non-tumor tissue, and metastatic lymph nodes.  
The primary aim of this project is to investigate the cellular heterogeneity of OSCC and characterize the tumor microenvironment at single-cell resolution. The analysis will focus on identifying distinct cell populations, characterizing their transcriptional profiles, and exploring biological processes and cellular states associated with OSCC progression and the tumor microenvironment.  
The analysis is being developed as a step-by-step and reproducible computational biology workflow using R and Seurat, with Python planned for selected downstream analyses and visualization.  

## Dataset

GEO accession: GSE198315  
Organism: Homo sapiens  
Disease: Oral squamous cell carcinoma (OSCC)  
Patients: 10  
Samples: 36  
Cells: 268,131  
Study population: HPV-negative metastatic OSCC  
Technology: 10x Genomics single-cell RNA sequencing  
Dataset source: NCBI Gene Expression Omnibus (GEO)  

## Sample Types and Abbreviations

GSE198315 contains single-cell RNA-seq samples collected from different anatomical regions of patients with HPV-negative metastatic OSCC. The study used multiregional sampling to compare the primary tumor with surrounding tissue and metastatic lymph nodes.   
TC-Tumor Core: Central region of the primary tumor  
TP-Tumor Periphery: Peripheral/edge region of the primary tumor  
NT-Non-Tumor: Adjacent non-tumor tissue surrounding the primary tumor  
mLN-Metastatic Lymph Node: Lymph node containing metastatic OSCC cells  


The dataset contains samples from 10 patients (P01–P10). Not every patient has all four sampling regions. The GEO sample identifiers follow the general format:  
"PXX-region"  
For example:  
P01-NT → Patient 01, non-tumor tissue  
P01-TC → Patient 01, tumor core  
P01-mLN → Patient 01, metastatic lymph node  
P02-TP → Patient 02, tumor periphery  

## Biological Meaning of the Sampling Strategy
The multiregional design allows comparison of cells from different locations within and around the tumor:  
NT → TP → TC  
It can broadly represent the transition from surrounding non-tumor tissue toward the tumor center, while mLN provides information about the metastatic environment.  
This design is particularly relevant for studying intratumoral heterogeneity, the tumor microenvironment, field cancerization, and cellular states associated with tumor progression and metastasis. The study specifically investigated spatial differences between tumor core, tumor periphery, surrounding tissue, and metastatic lymph nodes in advanced HPV-negative OSCC.  

# Dataset Acquisition
The single-cell RNA-seq dataset used in this project was obtained from the NCBI Gene Expression Omnibus (GEO), a public repository for functional genomics data. Go to the NCBI Gene Expression Omnibus (GEO) website and type GSE198315 on the search bar, which will lead to a OSCC study titled "Multiregional single-cell profiling reveals extensive field cancerization and an immunosuppressive microenvironment in oral squamous cell carcinoma."    

 [Click to view Dataset](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE198315)     

 <img width="918" height="923" alt="image" src="https://github.com/user-attachments/assets/9383f148-c5b8-4d28-9693-ca38676007d3" />    


GEO may provide several types of files for a sequencing study. These files should not automatically be treated as equivalent.  
Raw sequencing data → generally associated with the SRA  
Processed expression/count data → may be provided as supplementary files  
Metadata/sample information → available through the GEO Series and individual GSM records  
Series-level files → describe or summarize the overall study  
Therefore, before downloading and analysing the data, the available files were examined to determine what each file contains, at what processing stage it was generated, and which file is appropriate for the planned analysis. This distinction is important for ensuring that the subsequent scRNA-seq workflow is based on the correct input data.  

<img width="922" height="290" alt="image" src="https://github.com/user-attachments/assets/ef043c90-120c-42b1-a6c9-163c930910bf" />    

# Data Selection
The available files were examined before downloading to determine which representation of the data is appropriate for downstream single-cell RNA-seq analysis.  
The `barcodes.tsv.gz`, `features.tsv.gz`, and `matrix.mtx.gz` files together represent a sparse single-cell expression matrix that can be imported into a Seurat workflow.  
The `GSE198315_OSCC_UMI_count_matrix.txt.gz` file provides another processed representation of the UMI count data.  
The appropriate input was selected after examining the contents and structure of these files.    

TO BE CONTINUED..





## Project Status

🚧 **Work in progress**

The project is being developed incrementally, with each stage of the analysis documented to maintain reproducibility and facilitate understanding of the underlying biological and computational methods.
