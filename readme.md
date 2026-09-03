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

Phase 1 — Normal Control vs Tumor Control    
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
Below is the workflow, The commands are self explanatory with comments and the .Rmd script is also provided.    

## 1. Library Download
```bash
# Install GEOquery from Bioconductor
if (!requireNamespace("BiocManager", quietly = TRUE))
    install.packages("BiocManager")

BiocManager::install("GEOquery")

# Load GEOquery
library(GEOquery)
```

## 2. Dataset Download
The GEO Series GSE245601 was accessed using the GEOquery package. The associated GSM accession IDs were extracted from the GEO Series record, and a metadata table containing the GSM IDs and sample descriptions was generated and saved as a CSV file.    
Supplementary files associated with each GSM were then downloaded using getGEOSuppFiles(). A tryCatch() structure was implemented to report failed downloads while allowing the loop to continue with the remaining samples.    

```bash
library(GEOquery)
base_dir <- "D:/Bidya Work/single/GSE245601_Breast_Cancer"  ##create directory
dir.create(base_dir, recursive = TRUE, showWarnings = FALSE)

# Retrieve the GEO Series record for GSE245601
# GSEMatrix = FALSE retrieves the GEO record and its associated GSM entries without loading the expression matrix
gse <- getGEO("GSE245601", GSEMatrix = FALSE)   
gsm_ids <- names(gse@gsms)  # Extract all GSM accession IDs associated with the GSE
gsm_ids 

##To create a metadata table for the GSM ids and their description
gsm_info <- do.call( rbind,   
  lapply(gse@gsms, function(x) {
    data.frame(
      GSM = x@header$geo_accession,
      Title = x@header$title,
      stringsAsFactors = FALSE)
  })
)
gsm_info
write.csv( gsm_info, "D:/Bidya Work/single/GSE245601_Breast_Cancer/GSE245601_GSM_metadata.csv", row.names = FALSE) 

# Download supplementary files for all GSM samples
# tryCatch() allows the loop to continue even if an individual download fails

for (gsm in gsm_ids) {   
  message("\n==============================")
  message("Downloading: ", gsm)
  message("==============================")
  tryCatch({
    getGEOSuppFiles(
      gsm,
      baseDir = base_dir
    )
    message("✓ Completed: ", gsm)
    }, error = function(e) {
    message("✗ Failed: ", gsm)
    message("Error: ", e$message)
     })
}
```
<img width="1437" height="50" alt="image" src="https://github.com/user-attachments/assets/aa8423fa-3787-47e2-ba9b-54d112473676" />

<img width="1648" height="612" alt="image" src="https://github.com/user-attachments/assets/adddee05-dd78-49e8-b492-5c03efd06bb4" />

So, each of the GSM files were fetched and supplementary files related to each of the GSM files were downloaded into separate folders. Each folder containing the .h5 file which will be used in the downstream process. Next I am only doing a sanity check of my data to ensure if I have all the files with the correct names to start with my analysis

```bash
##In this chunk I am only checking if I have all the required data with the correct names or not
base_dir <- "D:/Bidya Work/single/GSE245601_Breast_Cancer"
files <- list.files(base_dir, pattern = "\\.h5$", recursive = TRUE, full.names = TRUE) ##list of files in the base_dir with.h5 as names
length(files) 
basename(files)
gsm_info 
library(Seurat)
test <- Read10X_h5(files[1])  ##testing one file first
class(test) #"dgCMatrix"
dim(test) #33538 genes 4267 cells
head(rownames(test))
head(colnames(test))
test[1:5, 1:5]
```
<img width="1235" height="328" alt="image" src="https://github.com/user-attachments/assets/adc8555e-ff21-4644-84ef-de9555b5ef68" />

## 3. Creating Individual seurat objects per sample

```bash
## Each .h5 file contains the raw UMI count matrix of one sample.
## Here, each count matrix is read and converted into an individual Seurat object. So, basically individual seurat objects for each sample

seurat_list <- lapply(files, function(x) {    #x=individual file names all 26, so iteration x=1 and continue the function, then x=2 then continue the funtion with second file.
  counts <- Read10X_h5(x)
  
  CreateSeuratObject(
    counts = counts,   #Use the count matrix I just created (counts) as the raw expression data.
    project = basename(x),  #gives the Seurat object a project name based on the filename.
    min.cells = 3,   #Keep a gene only if it is detected in at least 3 cells.
    min.features = 200  #Keep a cell only if it has at least 200 detected genes.
  ) 
})

names(seurat_list) <- basename(files) 
length(seurat_list)       ## Number of Seurat objects created
names(seurat_list)        ## Names of the samples
```
<img width="1078" height="212" alt="image" src="https://github.com/user-attachments/assets/ad09b45b-e73f-451f-9957-0c69e39a9ca8" />

This shows I have successfully created individual seurat objects per sample.

## 4. Adding Metadata to the Seurat Object

```bash
basename(files)
head(gsm_info)
colnames(gsm_info)

# Extract GSM ID from each Seurat object name
gsm_ids <- sub("_.*", "", names(seurat_list))

# Check that every GSM ID exists in the metadata
all(gsm_ids %in% gsm_info$GSM)   ##TRUE

##Go through each Seurat object, find which GSM it belongs to, then add that GSM and its Title to the object's metadata. Because for now our seurat objects of every sample only has the count matrix not metadata or anyother detail. So we are adding this detail to our seurat object. 

for (i in seq_along(seurat_list)) {  #For each position/index in my Seurat list, do the following.
  
  gsm <- gsm_ids[i]
  
  seurat_list[[i]]$GSM <- gsm_info[gsm, "GSM"]
  seurat_list[[i]]$Title <- gsm_info[gsm, "Title"]
}

head(seurat_list[[1]]@meta.data)
colnames(seurat_list[[1]]@meta.data)
```
<img width="681" height="50" alt="image" src="https://github.com/user-attachments/assets/733fbdc5-36e4-4b43-8799-73d66b208c6f" />
<img width="1807" height="305" alt="image" src="https://github.com/user-attachments/assets/100fd395-f9c4-4eb4-b503-c1c3a5f13c59" />

Individual .h5 files from GSE245601 were imported using Read10X_h5() and converted into Seurat objects using CreateSeuratObject(). Basic filtering was performed using min.cells = 3 and min.features = 200. The 26 sample-specific Seurat objects were stored in seurat_list. Sample identity and experimental information were retained through the existing orig.ident metadata. GSM accession numbers and sample descriptions were added to the metadata of each Seurat object using the corresponding GEO sample information (gsm_info). This provides explicit GEO identifiers and descriptive sample information for each cell and facilitates sample tracking and downstream analysis but the original sample identity was also retained in `orig.ident`.

## 5. Merging the Seurat Objects

```bash
sapply(seurat_list, ncol)
sapply(seurat_list, function(obj) {
  sum(duplicated(rownames(GetAssayData(obj, layer = "counts"))))  ##checking duplicates
})
clean_seurat_list <- seurat_list ##just easier to remember for the downflow

##Now next I merged all the 26 seurat objects to one so I could do QC and all necessary steps together

seurat_combined <- merge(   #merge(x = first_object, y = other_object(s))
  x = clean_seurat_list[[1]],
  y = clean_seurat_list[-1],
  add.cell.ids = names(clean_seurat_list)
) 
class(seurat_combined)
dim(seurat_combined) #131,784 cells and 26,506 genes
unique(seurat_combined$orig.ident)
head(colnames(seurat_combined))

saveRDS(                                  ##so that next time we can upload this file and start working. 
  seurat_combined,
  file = file.path(outputDir, "GSE245601_seurat_combined_preQC.rds")
)
```
<img width="1443" height="327" alt="image" src="https://github.com/user-attachments/assets/6e4690b8-bf6f-4029-9abe-f852643c4477" />

The 26 sample-specific Seurat objects were merged into a single Seurat object to enable joint downstream analysis of the complete GSE245601 dataset.    
The orig.ident metadata was retained to preserve the identity of the original sample for each cell. add.cell.ids was used during merging to prefix each cell barcode with its corresponding sample name, ensuring that cells could be traced back to their source sample and preventing barcode collisions between samples.    
The resulting combined Seurat object contained 131,784 cells and 26,506 genes before QC filtering.    







## Project Status

🚧 **Work in progress**

The project is being developed incrementally, with each stage of the analysis documented to maintain reproducibility and facilitate understanding of the underlying biological and computational methods.
