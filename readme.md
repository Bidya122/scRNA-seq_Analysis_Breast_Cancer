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

## 6. Load the Libraries

```bash
library(Seurat)
library(SeuratDisk)
library(dplyr)
library(R.utils)  
library(ggplot2)
library(ggExtra)
library(RColorBrewer)
library(openxlsx)
library(dplyr)
library(scales)
library(HGNChelper)
library(dittoSeq)
library(harmony)
library(batchelor)
library(zellkonverter)
library(SingleCellExperiment)
```
- Loading required packages for single-cell RNA-seq data would make the process easier for processing and analysis.
- Seurat and SingleCellExperiment are used for single-cell data handling and analysis.
- SeuratDisk and zellkonverter are used for conversion between Seurat, h5Seurat,
- H5AD, and SingleCellExperiment formats.
- dplyr is used for data manipulation, while ggplot2, ggExtra, RColorBrewer,
- scales, and dittoSeq are used for visualization and plotting.
- R.utils provides utility functions for file and data handling.
- openxlsx is used for reading and writing Excel files.
- HGNChelper is used for checking and correcting gene nomenclature.
- Harmony and batchelor provide methods for batch correction and integration of single-cell datasets.

## 7. Calculate QC Metrics for Combined Seurat Object

```bash
#For the directories
base_dir <- "D:/Bidya Work/single/GSE245601_Breast_Cancer"  
outputDir<- "D:/Bidya Work/single/GSE245601_Breast_Cancer/Output"
plotDir <- "D:/Bidya Work/single/GSE245601_Breast_Cancer/Plots"


#Load the .RDS object to R and check
seurat_combined <- readRDS(file.path(outputDir, "GSE245601_seurat_combined_preQC.rds"))
class(seurat_combined)
dim(seurat_combined) # 26506 x 131784
head(colnames(seurat_combined))  ## To view the first 20 row names (gene names) to check the gene naming format
unique(seurat_combined$orig.ident)
head(seurat_combined@meta.data)

# Calculate the percentage of mitochondrial gene counts per cell."^MT-" matches genes beginning with "MT-".
# The result is stored in the Seurat metadata as "percent.mt".
seurat_combined[["percent.mt"]] <- PercentageFeatureSet(
  seurat_combined,
  pattern = "^MT-"
)

# Calculate the percentage of ribosomal gene counts per cell. "^RPL|^RPS" matches genes beginning with "RPL" or "RPS".
# The result is stored in the Seurat metadata as "percent.rb".
seurat_combined[["percent.rb"]] <- PercentageFeatureSet(
  seurat_combined,
  pattern = "^RPL|^RPS"
)

colnames(seurat_combined@meta.data)  # Display metadata column names to confirm that percent.mt and percent.rb have been successfully added.
summary(seurat_combined$percent.mt) # Summarize the distribution of mitochondrial percentages across cells.
summary(seurat_combined$percent.rb) # Summarize the distribution of ribosomal percentages across cells.
summary(seurat_combined$nFeature_RNA) #Summarize the detection of no. of genes in the dataset basically complexity
summary(seurat_combined$nCount_RNA) #Summerizes total RNA
```

<img width="1306" height="257" alt="image" src="https://github.com/user-attachments/assets/a2383a40-2326-4ebf-a956-31250842e3e0" />

- `percent.mt — mitochondrial percentage`    
- Median = 2.94% → half of the cells have mitochondrial counts below ~2.94%.    
- 75% of cells are below ~5.03% → most cells have relatively low mitochondrial contribution.    
- Maximum = 96.51% → there are some extreme cells dominated by mitochondrial transcripts.    
And the important observation is the gap between 3rd quartile = 5.03% and Maximum = 96.51%. That strongly suggests that the extremely high values are concentrated in a relatively small subset of cells rather than representing the general population. The majority of cells show low mitochondrial transcript proportions, while a small subset exhibits extremely high mitochondrial percentages and may represent low-quality cells requiring further QC assessment.

- `percent.rb — ribosomal percentage`    
- 25% of cells have ribosomal percentages below ~13.1%.       
- Median is ~17.85%.       
- 75% of cells are below ~24.08%.      
- A small subset reaches very high values, up to ~71.9%.    
An important distinction is High ribosomal percentage does NOT automatically mean poor-quality cells. Ribosomal genes are highly expressed housekeeping genes, and their proportion can naturally vary between cells/cell types.    

The mitochondrial percentage provides an indicator of cell quality, as cells with unusually high proportions of mitochondrial transcripts may represent stressed, damaged, or dying cells. Ribosomal gene percentage was included as an additional transcriptomic QC metric to assess the composition of RNA transcripts across cells. These metrics will be visualized together with nFeature_RNA and nCount_RNA to assess their distributions and identify appropriate cell-level QC thresholds before filtering.    
  
nFeature_RNA: The median was 1,710 genes/cell, with 50% of cells containing between 897 and 3,342 detected genes. The maximum was 12,738 genes, indicating a small number of cells with very high gene detection.    
nCount_RNA: The median was 4,502 counts/cell, while 50% of cells had between 1,830 and 13,132 counts. The maximum reached 784,177 counts, indicating a small number of cells with exceptionally high RNA counts.    
Overall, most cells showed moderate RNA content and gene detection, but the high maximum values suggest the presence of potential outlier cells that will be examined during QC.    

## 8. Pre-QC Visualization

Initial QC was performed on the complete merged dataset to identify and remove low-quality cells and technical outliers before downstream biological analysis. QC distributions were examined across individual samples to ensure that filtering did not introduce sample-specific bias. 

```bash
study_id <- "GSE245601" # Define study ID
# Add sample information to metadata
seurat_combined <- AddMetaData(seurat_combined,  metadata = seurat_combined$orig.ident,  col.name = "Sample") #Creates a new metadata column called Sample, using the existing orig.ident.
head(seurat_combined@meta.data)

# Function to generate and save QC violin plots for each sample
save_violin_plots_separate <- function(
  seurat_obj,  
  plotDir,
  study_id,
  features = c( "nCount_RNA", "nFeature_RNA",  "percent.mt", "percent.rb" )
) {
 
  # Create plot directory if it does not already exist
  if (!dir.exists(plotDir)) {
    dir.create(plotDir, recursive = TRUE)
  }
  
# Extract cell-level metadata
  meta <- seurat_obj@meta.data

# Convert sample identity to factor for plotting
  meta$sample <- as.factor(meta$Sample)

# Generate one plot for each QC metric
  for (feat in features) {

# Check whether the QC metric exists
    if (!feat %in% colnames(meta)) {
      warning(paste("Skipping", feat, "- not found in metadata"))
      next
    }

    # Violin plot with boxplot overlay
    p <- ggplot( meta, aes(x = sample, y = .data[[feat]])) + 
      geom_violin( trim = TRUE, fill = "Red", alpha = 0.7) +
      geom_boxplot(width = 0.1, outlier.shape = NA, alpha = 0.6) +
      labs( title = feat, x = "Sample",  y = feat ) + theme_bw(base_size = 14) +  theme(
        axis.text.x = element_text(angle = 45,  hjust = 1),
        plot.title = element_text(hjust = 0.5)
      )

    # Save plot
    ggsave(filename = file.path(plotDir, paste0(study_id, "_preQC_", feat, "_violin.png")),
      plot = p, width = 30, height = 10, dpi = 600, bg = "white" )
  }
}

save_violin_plots_separate( seurat_combined, plotDir, study_id)    #Run the above function
```
<img width="1917" height="641" alt="image" src="https://github.com/user-attachments/assets/e41d0447-9897-4726-987d-22debbc5fb70" />

<img width="1917" height="647" alt="image" src="https://github.com/user-attachments/assets/d5f18bda-ec80-458a-90e6-a733a08e277f" /> 

<img width="1808" height="608" alt="image" src="https://github.com/user-attachments/assets/ef0df905-e6fb-40b5-84d7-597d59280cca" />

<img width="1807" height="607" alt="image" src="https://github.com/user-attachments/assets/5e1bef39-e211-4628-8318-2709ca500a1f" />

```bash
# Scatter plot of nCount vs nFeature with marginal histograms

study_id <- "GSE245601" # Define study ID
density_scatter_plot <- function(seurat_obj, filename) {
  
  ## Create a data frame containing QC metrics for each cell
  df <- data.frame(
    log1p_nCount_RNA = log1p(seurat_obj$nCount_RNA),       # Total RNA molecules per cell
    log1p_nFeature_RNA = log1p(seurat_obj$nFeature_RNA), # Number of detected genes per cell
    GSM = seurat_obj$GSM
  )
  
  ## Generate scatter plot
  p <- ggplot( df, aes(x = log1p_nCount_RNA, y = log1p_nFeature_RNA, color = GSM)) +
    geom_point(alpha = 0.3, size = 1.0) +
    theme_minimal() +
    theme(
      plot.margin = margin(10, 20, 20, 30),
      axis.title = element_text(size = 12),
      axis.text = element_text(size = 10) ,
      legend.position.inside = c(0.05, 0.95),
      legend.justification = c("left", "top"),
      legend.key.size = unit(0.5, "cm")) +
    guides(  color = guide_legend(
        override.aes = list(size = 5) ) ) +
    labs( x = "log1p(nCount_RNA)",  y = "log1p(nFeature_RNA)", colour = "Sample") +
    guides(colour = guide_legend(ncol = 1))
  
  ## Add marginal histograms
  p <- ggMarginal( p, type = "histogram", fill = "skyblue", bins = 40)
  
  ## Save plot
  ggsave( filename, plot = p,  width = 8, height = 10,  dpi = 600, bg = "white" )
}

density_scatter_plot( seurat_combined, file.path( plotDir, paste0(study_id, "_preQC_density-scatter.png")))
```

<img width="743" height="928" alt="image" src="https://github.com/user-attachments/assets/906564ab-52c9-4785-8368-d9b50b4f0e12" />

**Interpretation of the preQC for filtering**     

`nFeature_RNA` — Observations (Violin Plot):     
- Most samples do not have a distinct lower tail.    
- The two T47D samples have longer lower tails, but these tails are thin.    
- All samples start from y = 0, but the violins are not widest at 0.    
- Near y = 0, the violins become progressively narrower, but they do not form an extremely thin line.    
- The violins are widest around nFeature_RNA = 2,500–4,000.    
- Most samples have a similar overall violin shape, although there are some differences between samples.    
- When comparing the tamoxifen-treated and control samples within the same sample/group, their distributions look ~80% similar.    
- At the upper end, above approximately 5,000 nFeature_RNA, the distribution is mostly a thin line/upper extension, rather than a wide population.

*The major cell population across samples is concentrated around 2,500–4,000 detected genes. Lower nFeature_RNA values are present, extending toward 0, but the density decreases toward the lower end rather than remaining maximal there. The high-feature region above ~5,000 is predominantly represented by thin upper extensions/outliers. T47D samples show somewhat longer lower tails compared with the other samples.*

`nCount_RNA` -- Observations (Violin Plot):
- The violin distributions across all 26 samples are very small/petite.
- Most samples do not show a distinct lower tail.
- The two T47D samples show small lower tails.
- The y-axis starts at 0e+00.
- The violins are very narrow near the bottom of the plot.
- The boxplots are also positioned very close to the bottom of the y-axis.
- Most samples extend up to approximately 2 × 10⁵ or below.
- One sample extends much further, to approximately 8 × 10⁵.
- The extension toward 8 × 10⁵ is mostly a very thin line, rather than a wide distribution.
- The large range of that one sample compresses the distributions of the other samples visually.
- A y-axis display limit around 1 × 10⁵ may make the distributions of the majority of samples easier to visualize.
- This 1 × 10⁵ value is being considered only as a visualization limit at this stage, not as a filtering cutoff.

*The nCount_RNA violin plots showed very narrow distributions across most samples. The majority of samples extended to approximately 2 × 10⁵ counts or below, whereas one sample showed an extended range reaching approximately 8 × 10⁵. This upper extension was predominantly a thin line rather than a broad distribution. The large range of this sample compressed the distributions of the remaining samples near the lower end of the y-axis. The two T47D samples showed small lower extensions compared with the other samples. For clearer visualization of the majority of samples, an upper display limit of approximately 1 × 10⁵ could be considered.*        

`percent.mt` -- Observations (Violin Plot):
- The violins are tiny near the bottom of the y-axis.
- The widest range/distribution is approximately between 3–5%.
- Above approximately 10%, the violins mostly form thin upper lines/tails.
- There are no distinct lower tails in any of the samples.
- Most violins form a conical narrowing toward 0%.
- The overall trend/shape is similar across all samples.
- The control and treated samples within the same sample group look approximately 90% similar.

*The majority of cells showed mitochondrial RNA proportions concentrated around 3–5%, with only a small number of cells extending above ~10%. The absence of a broad high-mitochondrial population suggests that elevated mitochondrial content is not a widespread feature across the dataset. However, cells in the upper tail should be evaluated together with nFeature_RNA and nCount_RNA before determining an appropriate filtering threshold.*

`percent.rb` -- Observations (Violin Plot):
- In the overall graph, the violins show a wide distribution approximately between 10% and 30%.
- Unlike nCount_RNA and nFeature_RNA, the violins are clearly visible across the samples.
- The treated and control samples show broadly similar distributions, with only a few exceptions.
- All samples show an upper tail extending beyond the main distribution.
- The overall distribution pattern is broadly similar across the samples.

*The percent.rb distributions were broadly similar across samples, with the majority of cells showing ribosomal RNA proportions within approximately 10–30%. All samples displayed an upper tail, indicating a subset of cells with higher ribosomal RNA proportions. However, elevated ribosomal content alone is not sufficient evidence of poor cell quality and should be evaluated jointly with nFeature_RNA, nCount_RNA, and percent.mt before defining a filtering threshold.*

The density plot showed a positive association between nCount_RNA and nFeature_RNA, with the majority of cells forming a dense diagonal population. This indicates that cells with higher total RNA counts generally had a greater number of detected genes. Cells at the low-count/low-feature end and extreme high-count/high-feature regions were identified as populations requiring further evaluation using the other QC metrics rather than being excluded based on this plot alone.    

## 9. sanity check for preQC before the Application of stringent filters
Biological context is an important consideration in QC because cells with unusual QC metrics may still represent biologically meaningful populations. Therefore, the aim was to balance removal of genuinely poor-quality cells with preservation of biologically informative cells, avoiding both over-filtering and under-filtering.    
The QC plots provided an overall visual assessment of the data, but numerical summaries and joint metric analysis were required to quantify extreme populations and determine whether multiple QC abnormalities occurred within the same cells. Biological context was also considered because cells with unusual QC metrics may still represent biologically meaningful populations. Therefore, this assessment was performed before filtering to balance the removal of genuinely poor-quality cells with the preservation of biologically informative cells, avoiding both over-filtering and under-filtering.

```bash
##This chunk was done inorder to have a clear statistical and biological understanding of the data. Visualization of plots and the numbers definitely have different conclusions. I did not want to lose necessary data hence I performed this statistical chunk to reach to a logical conclusion which will be check visually again. 
# ------------------------------------------------------------
# 1. nFeature_RNA
# ------------------------------------------------------------
# Number of genes detected in each cell. Used as a measure of transcriptomic complexity.
# Very low values may indicate empty droplets, poor capture or low-quality cells.
# Very high values can indicate genuinely high-complexity cells or potential doublets/multiplets.
summary(seurat_combined$nFeature_RNA) 
quantile(seurat_combined$nFeature_RNA,
         probs = c(0, 0.01, 0.05, 0.25, 0.5, 0.75, 0.95, 0.99, 1))

# ------------------------------------------------------------
# 2. nCount_RNA
# ------------------------------------------------------------
# Total number of detected RNA/UMI counts per cell.
# Low values can indicate poor RNA capture or low-quality cells.
# Extremely high values may represent high-RNA cells or potential doublets/multiplets.
#
# nCount_RNA is strongly right-skewed in this dataset, so both the central distribution and the extreme upper tail are examined.

summary(seurat_combined$nCount_RNA)
quantile(seurat_combined$nCount_RNA,
         probs = c(0, 0.01, 0.05, 0.25, 0.5, 0.75, 0.95, 0.99, 1))

# ------------------------------------------------------------
# 3. percent.mt
# ------------------------------------------------------------
# Percentage of RNA counts derived from mitochondrial genes.
# Elevated mitochondrial RNA can indicate cellular stress, damage, or compromised cell integrity.
summary(seurat_combined$percent.mt)
quantile(seurat_combined$percent.mt,
         probs = c(0, 0.01, 0.05, 0.25, 0.5, 0.75, 0.95, 0.99, 1))

# ------------------------------------------------------------
# 4. percent.rb
# ------------------------------------------------------------
# Percentage of RNA counts derived from ribosomal genes.
# Ribosomal transcripts are naturally abundant in cells.
# Therefore, a high ribosomal fraction does not by itselfindicate poor cell quality.

summary(seurat_combined$percent.rb)
quantile(seurat_combined$percent.rb,
         probs = c(0, 0.01, 0.05, 0.25, 0.5, 0.75, 0.95, 0.99, 1))
#Investigate the potentially extreme populations
sum(seurat_combined$nFeature_RNA >= 7000)
sum(seurat_combined$percent.mt >= 15)
sum(seurat_combined$nCount_RNA > 100000)
sum(seurat_combined$nCount_RNA > 200000)
sum(seurat_combined$percent.rb >= 40)

# ============================================================
# JOINT QC METRIC ANALYSIS
# ============================================================
# Individual QC metrics are not sufficient to classify a cell as poor quality. Therefore, combinations of metrics are examined to identify cells showing concordant evidence of poor quality or unusual complexity.

# High-feature + high-count cells
sum(
  seurat_combined$nFeature_RNA >= 7000 &
  seurat_combined$nCount_RNA >= 100000
)
# High-feature + high-mitochondrial cells
sum(
  seurat_combined$nFeature_RNA >= 7000 &
  seurat_combined$percent.mt >= 15
)
# Low-feature + low-count + high-mitochondrial cells
sum(
  seurat_combined$nFeature_RNA < 1000 &
  seurat_combined$nCount_RNA < 2000 &
  seurat_combined$percent.mt >= 15
)

# ============================================================
# SAMPLE-WISE ASSESSMENT OF LOW-QUALITY CELLS
# ============================================================
table(seurat_combined$GSM[    #thresholds such as 1000, 2000 and 15% are dataset-informed working boundaries, not universal biological cutoffs.
  seurat_combined$nFeature_RNA < 1000 &
  seurat_combined$nCount_RNA < 2000 &
  seurat_combined$percent.mt >= 15
])

low_qc <- seurat_combined$nFeature_RNA < 1000 &
          seurat_combined$nCount_RNA < 2000 &
          seurat_combined$percent.mt >= 15

sum(low_qc)  #6196cells

qc_by_sample <- data.frame(Sample = seurat_combined$GSM, Low_QC = low_qc) # Calculate the proportion of low-quality cells in each sample
head(qc_by_sample) 
qc_summary <- aggregate(Low_QC ~ Sample, data = qc_by_sample, FUN = sum) # Number of low-QC cells per sample
total_summary <- aggregate(Low_QC ~ Sample, data = qc_by_sample, FUN = length) # Total number of cells per sample
qc_summary$Total_Cells <- total_summary$Low_QC # Add total cell numbers
qc_summary$Percent_Low_QC <- (qc_summary$Low_QC / qc_summary$Total_Cells) * 100  # Calculate percentage of low-QC cells in each sample
qc_summary

library(writexl) # Save the sample-wise QC summary
write_xlsx(
  qc_summary,
  path = file.path(outputDir, "GSE245601_low_QC_summary.xlsx")
)
# ============================================================
# INVESTIGATION OF HIGH-COMPLEXITY CELLS
# ============================================================
# Very high nCount_RNA and nFeature_RNA values can represent genuinely high-complexity cells or potential doublets/multiplets. These cells are therefore investigated rather than removed automatically.

#Define the high-complexity population. TRUE  → cell has BOTH nCount_RNA ≥ 100,000 AND nFeature_RNA ≥ 7,000, FALSE → cell does not meet both conditions
high_qc <- seurat_combined$nCount_RNA >= 100000 &
           seurat_combined$nFeature_RNA >= 7000 ##1226 cells in total

table(seurat_combined$GSM[high_qc]) ##per sample how are the 1226 cells distributed 
summary(seurat_combined$percent.mt[high_qc]) #we calculated the summary of percent.mt of these 1226 cells and saw that their mediam were low so not all of them are bad!! 

quantile(  ##It means that the typical high-complexity cell has low mitochondrial contribution.That makes these cells much less suspicious as a general population.
  seurat_combined$percent.mt[high_qc],
  probs = c(0, 0.25, 0.5, 0.75, 0.95, 0.99, 1)
)
sum( high_qc & seurat_combined$percent.mt >= 15)  #How many high-complexity cells also have high mt% = 89cells
table(seurat_combined$GSM[ high_qc & seurat_combined$percent.mt >= 15])  #Which samples contain those 89 cells = "GSM7845550", "GSM7845551" both are cell line data

sum(
  seurat_combined$GSM %in% c("GSM7845550", "GSM7845551") & #Check whether T47D (cell line) is responsible for our LOW-QC population 
  seurat_combined$nFeature_RNA < 1000 &
  seurat_combined$nCount_RNA < 2000 &
  seurat_combined$percent.mt >= 15
) #14cells

#------------------------------------------------
#Create final QC summary
#------------------------------------------------
qc_before_after <- data.frame(
  Metric = c( "Cells before filtering", "Cells flagged for removal",  "Cells retained", "Percentage removed"),
  Value = c( ncol(seurat_combined), sum(low_qc), sum(!low_qc), round(mean(low_qc) * 100, 2)
  )
)

qc_before_after
```
<img width="1057" height="703" alt="image" src="https://github.com/user-attachments/assets/722dad03-b0dd-4cff-be6d-87bfa866b65e" />

The numerical QC assessment showed that the median cell had 1,710 detected genes (nFeature_RNA) and 4,502 RNA counts (nCount_RNA), while the upper quartiles were 3,342 genes and 13,132 counts, respectively. The lower tails were gradual rather than showing a sharp drop, with the 5th percentiles at 389 genes and 703 counts, indicating that there was no obvious natural cutoff around commonly used thresholds such as 1,000 genes or 2,000 counts. At the upper end, 5,067 cells had ≥7,000 detected genes and 1,226 cells had >100,000 counts, indicating that these were not necessarily rare enough to be removed automatically and therefore required further investigation.     
For mitochondrial content, the median was 2.93% and the 75th percentile was 5.03%, indicating that most cells had relatively low mitochondrial contribution; however, the 95th and 99th percentiles increased to 17.25% and 40.84%, respectively, showing a substantial high-mitochondrial tail. A total of 8,068 cells had ≥15% mitochondrial reads, making a simple 15% cutoff potentially too stringent for this dataset and requiring evaluation together with other QC metrics.     
For ribosomal content, the median was 17.85% and the 75th percentile was 24.08%, with 2,846 cells having ≥40%. Since ribosomal transcripts are naturally abundant, this metric was used mainly to characterize the dataset rather than as an independent exclusion criterion. Overall, these numerical results showed that none of the individual thresholds could be safely applied in isolation, supporting the subsequent use of combined QC metrics to identify genuinely low-quality cells.    

<img width="640" height="345" alt="image" src="https://github.com/user-attachments/assets/2f94efca-e262-41f4-b8ff-e8bea5c3a713" />

The joint QC analysis showed that 1,226 cells had both high gene detection (≥7,000 nFeature_RNA) and high RNA counts (≥100,000 nCount_RNA), identifying a high-complexity population that required investigation rather than automatic removal. Only 179 cells had both high gene detection (≥7,000) and elevated mitochondrial content (≥15%), indicating that high mitochondrial content was not generally associated with the high-complexity population. In contrast, 6,196 cells simultaneously showed low gene detection (<1,000), low RNA counts (<2,000), and high mitochondrial content (≥15%). This combination provides concordant evidence of low transcriptomic complexity, low RNA capture, and cellular stress/compromised quality, making these 6,196 cells a much stronger candidate population for removal than cells identified by any single QC metric alone.      

<img width="1415" height="472" alt="image" src="https://github.com/user-attachments/assets/570de0b5-4026-4536-885b-df4df0bd7d80" />

<img width="487" height="680" alt="image" src="https://github.com/user-attachments/assets/83eb858d-48ae-4017-86cf-d37c41ef9355" />

The 6,196 cells identified by the combined low-QC criteria were assessed sample-wise to determine whether the flagged population was driven by a single problematic sample. The table() output shows that low-QC cells were present across all 26 samples, with the largest numbers occurring in GSM7845555 (655), GSM7845563 (587), GSM7845569 (516), GSM7845547 (489), and GSM7845570 (490). This indicates that the low-QC population is distributed across the dataset rather than being restricted to one sample. The subsequent sample-wise summary calculates both the number and percentage of flagged cells in each sample, allowing the extent of low-quality cells to be evaluated relative to each sample's total cell number. This supports cell-level filtering rather than removing an entire sample solely because it contains a higher proportion of low-QC cells. In the excel file I found that the percentage ranged from 0.1% to 14%. Therefore, the QC abnormalities were not sufficient to justify excluding any entire sample, and filtering was applied at the individual-cell level to remove cells with concordant evidence of poor quality while retaining the remaining cells.    

<img width="1573" height="726" alt="image" src="https://github.com/user-attachments/assets/ff2aa9c3-bffe-4559-9f9c-a6d0819d2053" />

The high-complexity population was defined as cells with both nCount_RNA ≥100,000 and nFeature_RNA ≥7,000, resulting in 1,226 cells. These cells were distributed across multiple samples, with the largest contribution from GSM7845550 (286 cells), followed by GSM7845552 (130) and GSM7845546 (115), indicating that the population was not restricted to a single sample. Importantly, the mitochondrial content of these 1,226 cells was generally low, with a median of only 2.26% and 75% of cells below 8.34%, suggesting that most high-complexity cells did not show evidence of cellular stress based on mitochondrial content. Only 89 of the 1,226 high-complexity cells had percent.mt ≥15%, demonstrating that high RNA counts and high gene detection were generally not accompanied by elevated mitochondrial content. These 89 cells were almost entirely restricted to GSM7845550 (87 cells) and GSM7845551 (2 cells), which correspond to the T47D cell-line samples. Therefore, the high-complexity population was retained rather than removed automatically, as the available QC metrics did not provide sufficient evidence that these cells were predominantly low-quality or doublets.    
A further check examined whether the T47D cell-line samples were responsible for the low-QC population identified using the combined criteria of low gene detection, low RNA counts and high mitochondrial content. Only 14 cells from these two T47D samples met all three low-QC criteria, indicating that the T47D samples contributed very little to the 6,196-cell low-QC population and therefore did not explain the overall low-QC signal.    

<img width="1096" height="437" alt="image" src="https://github.com/user-attachments/assets/7bdeef65-cd27-49ce-b284-dfe594286008" />

The final pre-filtering summary showed 131,784 cells in the combined dataset, of which 6,196 cells (4.7%) were identified as low-quality based on the combined criteria of low gene detection, low RNA counts and elevated mitochondrial content. This leaves 125,588 cells (95.3%) for downstream analysis. Removing only 4.7% of cells indicates that the filtering strategy is relatively conservative and specifically targets cells with concordant evidence of poor quality, while retaining the majority of the dataset and minimizing the risk of over-filtering biologically informative cells.

## 10. Cell-level QC filtering - Four Metrics and Doublets

```bash
seurat_filtered <- subset( seurat_combined,
   subset =
    nCount_RNA < 100000 &
    nFeature_RNA < 7000 &
    percent.mt < 12 &
    percent.rb < 50
)

# Check dimensions
dim(seurat_filtered)   # 26506genes  116174cells

# Number of cells retained
ncol(seurat_filtered)   #116174

# Number of cells removed
ncol(seurat_combined) - ncol(seurat_filtered) # 15610

# Percentage retained
ncol(seurat_filtered) / ncol(seurat_combined) * 100  #88.15


##for doublets


## Identify samples
sample_ids <- unique(seurat_filtered$orig.ident)

## Store doublet results for each sample
doublet_results <- list()

## Run scDblFinder sample-wise
for (sample in sample_ids) {
  
  cat("\nProcessing:", sample, "\n")
  
  sample_obj <- subset( seurat_filtered, subset = orig.ident == sample)
  
  sce <- as.SingleCellExperiment(sample_obj)
  sce <- scDblFinder(sce)
  
  doublet_results[[sample]] <- data.frame(
    cell = colnames(sce),
    scDblFinder.class = sce$scDblFinder.class,
    scDblFinder.score = sce$scDblFinder.score )
  
  cat( "Doublets:", sum(sce$scDblFinder.class == "doublet"), "of",   ncol(sce), "\n")
  
  rm(sample_obj, sce)
  gc()
}

doublet_summary <- do.call( rbind,
  lapply(  names(doublet_results),
    function(x) { data.frame( Sample = x,
        Total_Cells = nrow(doublet_results[[x]]),
        Doublets = sum( doublet_results[[x]]$scDblFinder.class == "doublet"  ),
        Doublet_Percent = mean( doublet_results[[x]]$scDblFinder.class == "doublet"  ) * 100)}
  )
)

doublet_summary

doublet_cells <- unlist(
  lapply( doublet_results,
    function(x) { x$cell[x$scDblFinder.class == "doublet"] }
  )
)

seurat_cleanQC <- subset( seurat_filtered,
  cells = setdiff( colnames(seurat_filtered), doublet_cells ))  #106334
```

So as shown above, the preQC distribution of the data was:      

| Metric | Median | 95th Percentile | 99th Percentile | Maximum |
|---|---:|---:|---:|---:|
| nFeature_RNA | 1,710 | 6,584 | 8,482 | 12,738 |
| nCount_RNA | 4,502 | 47,975 | 97,750 | 784,177 |
| percent.mt | 2.93% | 17.25% | 40.84% | 96.51% |
| percent.rb | 17.85% | 35.63% | 43.02% | 71.86% |

The distributions showed clear upper tails for all four metrics, particularly nCount_RNA and percent.mt. Based on the distributions and QC visualization the thresholds were set.          
`nFeature_RNA < 7000` removed cells with unusually high numbers of detected genes while retaining the main distribution.       
`nCount_RNA < 100000` removed the extreme high-count tail. This cutoff was close to the pre-QC 99th percentile (~97,750), making it a selective upper-tail filter rather than a broad removal of high-RNA cells.       
`percent.mt < 12` removed cells with relatively high mitochondrial content while retaining the majority of cells in the main distribution.        
`percent.rb < 50` removed cells with exceptionally high ribosomal contribution. Since ribosomal transcripts are naturally abundant, this was used as an upper-tail filter rather than interpreting moderate ribosomal percentages as poor quality.         

So Intially the merged dataset initially contained 131,784 cells and after applying the four QC thresholds, Cells before QC: 131,784; Cells after QC: 116,174; Cells removed: 15,610; Cells retained: 88.15%. 

After QC filtering, potential doublets were identified using scDblFinder. A doublet occurs when two cells are captured together and assigned to a single barcode. Such cells can contain a mixed transcriptional profile and may create artificial cell populations during downstream clustering and cell-type annotation. Because the dataset contained 26 independent samples, doublet detection was performed sample-wise using the orig.ident metadata field. Each sample was converted to a SingleCellExperiment object and processed using scDblFinder with its default parameters. After Doublet detection the results were: 
Cells after QC: 116,174    
Doublets removed: 9,840    
Final cells: 106,334    
Retention from original dataset: 80.69%    
Retention after QC: ~91.53%    

As a final QC sanity check, stringent low-quality criteria were applied simultaneously using nFeature_RNA < 1000, nCount_RNA < 2000, and percent.mt >= 15. No cells (0) met all three criteria simultaneously, indicating that the final dataset did not contain cells showing this combination of strong low-quality characteristics, suggesting that no cells in the final dataset exhibited this combination of low gene detection, low RNA counts, and high mitochondrial content.

```bash
low_qc <- seurat_cleanQC$nFeature_RNA < 1000 &
          seurat_cleanQC$nCount_RNA < 2000 &
          seurat_cleanQC$percent.mt >= 15

sum(low_qc) #0 = not a single cell simultaneously meets all three stringent low-QC conditions

# Cell retention summary

initial_cells <- ncol(seurat_combined)

after_qc_cells <- ncol(seurat_filtered)

after_doublet_cells <- ncol(seurat_cleanQC)

retention_summary <- data.frame(
  Stage = c( "Initial", "After QC filtering", "After doublet removal" ),
  Cells = c( initial_cells, after_qc_cells, after_doublet_cells ),
  Retained_percent = round(
    c(  initial_cells, after_qc_cells, after_doublet_cells ) / initial_cells * 100, 2 )
)

retention_summary #106334 cells = 80.69%
```

<img width="977" height="122" alt="image" src="https://github.com/user-attachments/assets/99616f01-635d-4cda-81c0-78cbf5b9de1a" />

## 11. Post QC Visualization

```bash
# Scatter plot of nCount vs nFeature with marginal histograms

study_id <- "GSE245601" # Define study ID
density_scatter_plot <- function(seurat_obj, filename) {
  
  ## Create a data frame containing QC metrics for each cell
  df <- data.frame(
    log1p_nCount_RNA = log1p(seurat_obj$nCount_RNA),       # Total RNA molecules per cell
    log1p_nFeature_RNA = log1p(seurat_obj$nFeature_RNA), # Number of detected genes per cell
    GSM = seurat_obj$GSM
  )
  
  ## Generate scatter plot
  p <- ggplot( df, aes(x = log1p_nCount_RNA, y = log1p_nFeature_RNA, color = GSM)) +
    geom_point(alpha = 0.3, size = 1.0) +
    theme_minimal() +
    theme(
      plot.margin = margin(10, 20, 20, 30),
      axis.title = element_text(size = 12),
      axis.text = element_text(size = 10) ,
      legend.position.inside = c(0.05, 0.95),
      legend.justification = c("left", "top"),
      legend.key.size = unit(0.5, "cm")) +
    guides(  color = guide_legend(
        override.aes = list(size = 5) ) ) +
    labs( x = "log1p(nCount_RNA)",  y = "log1p(nFeature_RNA)", colour = "Sample") +
    guides(colour = guide_legend(ncol = 1))
  
  ## Add marginal histograms
  p <- ggMarginal( p, type = "histogram", fill = "skyblue", bins = 40)
  
  ## Save plot
  ggsave( filename, plot = p,  width = 8, height = 10,  dpi = 600, bg = "white" )
}

density_scatter_plot( seurat_cleanQC, file.path( plotDir, paste0(study_id, "_postQC_density-scatter.png")))
```
<img width="742" height="927" alt="image" src="https://github.com/user-attachments/assets/fc07ff0c-1158-4f99-b7e7-4ab8417335ba" />

A density scatter plot of nCount_RNA against nFeature_RNA was generated to examine the relationship between sequencing depth and the number of genes detected per cell. This plot provides a complementary view to the individual QC metric distributions and helps identify cells occupying unusual regions of the expression landscape. Most cells formed a dense main population, showing the expected positive relationship between total RNA counts and the number of detected genes: cells with more RNA counts generally had more detected genes. A smaller population extended into the high-count and high-feature region, representing cells with unusually large transcriptomic profiles. These cells were considered as part of the overall QC assessment because extreme values can arise from high-RNA cells as well as potential multiplets or doublets. The post-QC density pattern remained broadly similar to the pre-QC distribution, indicating that the main population of cells was retained and that QC filtering did not substantially alter the overall structure of the dataset. The plot was therefore used as a supporting visualization rather than as a standalone filtering criterion. Final QC decisions were based on the combined assessment of nFeature_RNA, nCount_RNA, percent.mt, and percent.rb, followed by sample-wise doublet detection using scDblFinder.    

```bash
study_id <- "GSE245601" # Define study ID
head(seurat_cleanQC@meta.data)

# Function to generate and save QC violin plots for each sample
save_violin_plots_separate <- function(
  seurat_obj,  
  plotDir,
  study_id,
  features = c( "nCount_RNA", "nFeature_RNA",  "percent.mt", "percent.rb" )
) {
 
  # Create plot directory if it does not already exist
  if (!dir.exists(plotDir)) {
    dir.create(plotDir, recursive = TRUE)
  }
  
# Extract cell-level metadata
  meta <- seurat_obj@meta.data

# Convert sample identity to factor for plotting
  meta$sample <- as.factor(meta$GSM)
 cutoffs <- c(nCount_RNA=100000, nFeature_RNA=7000, percent.mt=12, percent.rb=50)
 
# Generate one plot for each QC metric
  for (feat in features) {

# Check whether the QC metric exists
    if (!feat %in% colnames(meta)) {
      warning(paste("Skipping", feat, "- not found in metadata"))
      next
    }

    # Violin plot with boxplot overlay
    p <- ggplot( meta, aes(x = sample, y = .data[[feat]])) + 
      geom_violin( trim = TRUE, fill = "Red", alpha = 0.7) +
      geom_boxplot(width = 0.1, outlier.shape = NA, alpha = 0.6) +
       geom_hline(yintercept=cutoffs[feat], linetype="dashed") +
      labs( title = feat, x = "Sample",  y = feat ) + theme_bw(base_size = 14) +  theme(
        axis.text.x = element_text(angle = 45,  hjust = 1),
        plot.title = element_text(hjust = 0.5)
      )

    # Save plot
    ggsave(filename = file.path(plotDir, paste0(study_id, "_postQC_", feat, "_violin.png")),
      plot = p, width = 30, height = 10, dpi = 600, bg = "white" )
  }
}

save_violin_plots_separate( seurat_cleanQC, plotDir, study_id)    #Run the above function

saveRDS( seurat_cleanQC, file = file.path(outputDir, "GSE245601_seurat_cleanQC.rds"))
file.exists(file.path(outputDir, "GSE245601_seurat_cleanQC.rds")) #TRUE
ncol(seurat_cleanQC) #106334
```
<img width="1816" height="617" alt="image" src="https://github.com/user-attachments/assets/81e0e80e-dce4-42af-b4eb-98f2ac932f21" />

<img width="1823" height="627" alt="image" src="https://github.com/user-attachments/assets/d8e79392-c1ab-4ce2-a3ef-c1a5f2caf6ca" />

<img width="1836" height="632" alt="image" src="https://github.com/user-attachments/assets/0728ce7f-835e-45ff-8497-11efcf7592ac" />

<img width="1828" height="633" alt="image" src="https://github.com/user-attachments/assets/29716df1-8c97-4a65-86ae-3c7fe1a42157" />

After QC filtering and doublet removal, violin plots were generated separately for each sample using the four QC metrics: nCount_RNA, nFeature_RNA, percent.mt, and percent.rb. The plots were grouped by GSM/sample to assess whether the QC filtering produced consistent distributions across samples. Overall, the post-QC violin plots showed that the majority of cells were concentrated around the central distribution, with the boxplots providing a clear view of the median and interquartile range. A small number of upper outliers were still visible, but the extreme populations observed before QC were substantially reduced.      
The main exception was nCount_RNA, which continued to show a relatively long upper tail across several samples. These cells were retained because they remained within the predefined nCount_RNA < 100,000 threshold and were not removed solely based on their position in the upper tail. Since high RNA counts can also occur in biologically high-RNA cells, an additional arbitrary cutoff was not applied at this stage. The nCount_RNA distribution will therefore be monitored during subsequent analysis. If a specific downstream phase or selected sample subset reveals a clear technical concern associated with these high-count cells, their effect can be evaluated and addressed at that stage rather than removing them prematurely.    
Overall, the post-QC visualizations indicated that the major cell population was retained while the most extreme QC populations had been removed.      

## 12. Fetching and filtering the Protein-Coding Genes

Following cell-level quality control, genes were annotated using the GENCODE human GRCh38 annotation. Gene biotypes were used to distinguish protein-coding genes from other genomic features such as lncRNAs, pseudogenes, and other non-protein-coding transcripts. To fetch the list I used GENCODE. For this analysis, GENCODE v37 was used to identify genes annotated as protein_coding before downstream normalization and analysis. (Gencode Release 37)[https://www.gencodegenes.org/human/release_37.html]. 















































## Project Status

🚧 **Work in progress**

The project is being developed incrementally, with each stage of the analysis documented to maintain reproducibility and facilitate understanding of the underlying biological and computational methods.
