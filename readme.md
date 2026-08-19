# Single-Cell RNA-seq Analysis of Estrogen Receptor Positive Primary Human Breast Tumors

## Project Overview

This project focuses on the analysis of single-cell RNA sequencing (scRNA-seq) data from ER-positive, HER2-negative breast cancer to investigate cellular heterogeneity and differences in response to tamoxifen treatment. The study uses an ex vivo model in which freshly resected primary breast tumor tissues were dissociated into single-cell suspensions and treated with either control media or tamoxifen before single-cell RNA sequencing.        
The analysis uses the publicly available `GSE245601` dataset from the NCBI Gene Expression Omnibus (GEO). The dataset contains scRNA-seq data from primary breast tumor samples under control and tamoxifen-treated conditions, allowing paired comparison of treatment responses across tumor samples.    
The primary aim of this project is to characterize the cellular composition of primary breast tumors, investigate transcriptional changes associated with tamoxifen treatment, and explore heterogeneous treatment responses across malignant and non-malignant cell populations. Particular attention will be given to identifying distinct malignant-cell states and molecular programs associated with tamoxifen response or reduced responsiveness. The analysis is being developed as a step-by-step and reproducible computational biology workflow using R and Seurat, with Python planned for selected downstream analyses and visualization.    

## Dataset

GEO accession: GSE245601    
Organism: Homo sapiens    
Disease: ER-positive, HER2-negative breast cancer    
Primary tumor samples: 10    
Conditions: Control and Tamoxifen    
Treatment: 10 µM tamoxifen for 12 hours ex vivo    
Technology: 10x Genomics single-cell RNA sequencing    
Data format: 10x Genomics .h5 expression matrices    
Dataset source: NCBI Gene Expression Omnibus (GEO)    

## Sample Types and Abbreviations

GSE245601 contains single-cell RNA-seq samples from primary breast tumors, normal breast tissue, and the T47D breast cancer cell line. The samples were treated ex vivo with either control media or tamoxifen for 12 hours to investigate differences in cellular response to treatment. 

Sample Types:    
Normal — Normal breast tissue samples    
Tumor — Primary breast tumor samples    
T47D — T47D breast cancer cell line    
Control — Samples treated with control media    
Tamoxifen — Samples treated with 10 µM tamoxifen for 12 hours     

The dataset also contains:

Normal_01_Control / Normal_01_Tamoxifen
Normal_02_Control / Normal_02_Tamoxifen
T47D_Control / T47D_Tamoxifen


## Study Design and Planned Analysis
The analysis will be divided into two main phases to first establish the cellular and molecular characteristics of breast tumor tissue and then investigate the response to tamoxifen treatment.    

**Phase 1 — Normal vs Tumor:** The first phase will compare normal breast tissue with primary breast tumor samples to characterize the differences in cellular composition and transcriptional profiles.    
The analysis will focus on:    
 
Identifying major cell populations present in normal and tumor tissue    
Comparing cellular composition between normal and tumor samples    
Characterizing tumor-associated cellular populations    
Identifying genes and biological pathways that distinguish tumor from normal tissue    

**Phase 2 — Tumor Control vs Tamoxifen:**  The second phase will focus on the paired primary tumor samples to investigate the cellular and molecular response to tamoxifen treatment.    
The analysis will focus on:

Comparing cellular composition between Control and Tamoxifen-treated tumor samples    
Identifying treatment-associated differentially expressed genes    
Investigating biological pathways affected by tamoxifen    
Characterizing treatment-associated transcriptional states    
Exploring heterogeneous responses among malignant cell populations    
Identifying potential molecular features associated with tamoxifen response or reduced responsiveness    

The primary treatment-response analysis will focus on paired primary tumor samples, while the normal breast tissue samples will provide biological and cellular reference for interpreting tumor-associated changes.

<img width="753" height="733" alt="image" src="https://github.com/user-attachments/assets/e7fbd7bf-a117-4104-ad5f-b30147249595" />    

Phase 1 — Normal vs Tumor    
Normal: 2 samples    
Tumor Control: 10 samples    
So Phase 1 should be Normal vs Tumor Control, giving 12 samples total.    

Phase 2 — Tumor Control vs Tamoxifen    
Tumor Control: 10    
Tumor Tamoxifen: 10    
Total: 20 samples    

# Dataset Acquisition
The single-cell RNA-seq dataset used in this project was obtained from the NCBI Gene Expression Omnibus (GEO), a public repository for functional genomics data. Go to the NCBI Gene Expression Omnibus (GEO) website and type GSE245601 on the search bar, which will lead to a Breast Cancer study titled "Tamoxifen Response at Single Cell Resolution in Estrogen Receptor Positive Primary Human Breast Tumors."    

 [Click to view Dataset](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE245601)     

<img width="921" height="926" alt="image" src="https://github.com/user-attachments/assets/21ef8d0d-e67c-4046-a980-16d2deff7b86" />    
   

TO BE CONTINUED..





## Project Status

🚧 **Work in progress**

The project is being developed incrementally, with each stage of the analysis documented to maintain reproducibility and facilitate understanding of the underlying biological and computational methods.
