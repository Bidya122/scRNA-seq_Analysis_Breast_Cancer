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

Following cell-level quality control, genes were annotated using the GENCODE human GRCh38 annotation. Gene biotypes were used to distinguish protein-coding genes from other genomic features such as lncRNAs, pseudogenes, and other non-protein-coding transcripts. To fetch the list I used GENCODE. For this analysis, GENCODE v37 was used to identify genes annotated as protein_coding before downstream normalization and analysis. [Gencode Release 37](https://www.gencodegenes.org/human/release_37.html). 

```bash

seurat_cleanQC <- readRDS("D:/Bidya Work/single/GSE245601_Breast_Cancer/Output/GSE245601_seurat_cleanQC.rds")  #Load the seurat_cleanQC file
gtf_path <- file.path(
  "D:/Bidya Work/single/GSE245601_Breast_Cancer",
  "gencode.v37.annotation.gtf.gz"
)
file.exists(gtf_path)
gtf <- import(gtf_path)
colnames(gtf) #NULL = gtf is a GRanges object, so its annotation fields are stored as metadata columns, which we can access with mcols(gtf).
head(gtf)
table(gtf$type)
table(gtf$gene_type)[1:20]
#protein_coding = 2729272  That's the number of GTF annotation rows whose gene_type is protein-coding—because the GTF contains gene, transcript, exon, CDS, UTR, etc. records.
protein_coding_genes_gtf <- gtf[ gtf$type == "gene" & gtf$gene_type == "protein_coding"]
length(protein_coding_genes_gtf) #19951
head(data.frame(
    gene_id = protein_coding_genes_gtf$gene_id,
    gene_name = protein_coding_genes_gtf$gene_name,
    gene_type = protein_coding_genes_gtf$gene_type))

protein_coding_gene_names <- protein_coding_genes_gtf$gene_name
sum(rownames(seurat_cleanQC) %in% protein_coding_gene_names) #17706 #we'd be retaining about 66.8% of the current features.
dim(seurat_cleanQC)

non_protein_coding <- setdiff(rownames(seurat_cleanQC),  protein_coding_gene_names) #So we are checking about the rest of it
length(non_protein_coding) #8800
head(non_protein_coding, 50)
grep("^MT-", non_protein_coding, value = TRUE)[1:20]


#Checking the 8800 non coding features properly so we don't lose important ones
# Sanity check: verify how representative excluded genes are classified in GENCODE v37.
# This confirms that genes such as FAM87B and LINC00115 are annotated as non-coding and were therefore correctly excluded from the protein-coding gene set.
gtf[ gtf$type == "gene" & gtf$gene_name == "FAM87B", c("gene_id", "gene_name", "gene_type")]
gtf[ gtf$type == "gene" & gtf$gene_name %in% c("LINC00115", "LINC01342", "FAM87B"), c("gene_id", "gene_name", "gene_type")] ## Check multiple excluded genes to confirm consistent GENCODE annotation.

seurat_protein_coding <- subset( seurat_cleanQC, features = protein_coding_gene_names)
dim(seurat_protein_coding) #  17706 genes 106334 cells = Verification that all have protein coding genes
all(rownames(seurat_protein_coding) %in% protein_coding_gene_names) #Verify that all remaining features are protein-coding
saveRDS( seurat_protein_coding, file.path(outputDir, "GSE245601_seurat_protein_coding.rds"))
```
Following cell-level quality control, the genes detected in the dataset were cross-referenced against the GENCODE human GRCh38 annotation. GENCODE Release 37 was used to identify genes annotated as protein_coding at the gene level. Only gene-level records with the biotype protein_coding were retained, resulting in 19,951 protein-coding genes in the GENCODE reference. These gene symbols were matched against the features present in the quality-controlled Seurat object. Of the 26,506 features in the object, 17,706 matched the GENCODE protein-coding gene set. The remaining 8,800 features included non-protein-coding genomic features such as lncRNAs and antisense transcripts. Representative excluded features were cross-checked against the GENCODE annotation to verify their assigned gene biotypes.
The Seurat object was then subsetted to the 17,706 protein-coding genes while retaining all 106,334 QC-passed cells. The resulting object was saved as GSE245601_seurat_protein_coding.rds and used as the input for downstream normalization and analysis.    

# PHASE 1 - NORMAL v/s TUMOR SAMPLES

The first phase of this analysis focuses on characterizing transcriptomic differences between normal breast tissue and primary breast tumor tissue at single-cell resolution. To establish a baseline comparison without introducing treatment-related effects, only Control samples were included in this phase. Tamoxifen-treated samples and the T47D cell-line samples were excluded. The analysis includes: 2 normal control samples: Normal_01_Control and Normal_02_Control and 10 primary tumor control samples: Tumor_01_Control through Tumor_10_Control. This resulted in 57,420 cells across 12 control samples after quality control and protein-coding gene filtering. 

[Get the Phase1 Script](https://github.com/Bidya122/scRNA-seq_Analysis_Breast_Cancer/blob/main/02_GSE245601_Breast_Cancer_Phase1.Rmd)


## 1. Dataset Check before the Downstream Analysis

```bash
seurat_protein_coding <- readRDS( file.path(outputDir, "GSE245601_seurat_protein_coding.rds")) #Load the RDS file 
dim(seurat_protein_coding) #17706 genes 106334 cells
head(seurat_protein_coding@meta.data)
unique(seurat_protein_coding$Title)
seurat_protein_coding$Condition <- ifelse( grepl("Normal_", seurat_protein_coding$Title),"Normal",
  ifelse(  grepl("Tumor_", seurat_protein_coding$Title), "Tumor", "T47D"))
table(seurat_protein_coding$Condition) #Normal: 21,385 cells; Tumor: 82,968 cells; T47D: 1,981 cells. Total = 106,334 cells.

#Create Phase1 Object
seurat_phase1 <- subset(seurat_protein_coding,
  subset = grepl("Normal_0[12]_Control", Title) |
           grepl("Tumor_.*_Control", Title))
dim(seurat_phase1) # 17430 genes 57420 cells
table(seurat_phase1$Condition) #Normal 13767 Tumor  43653 
sample_info <- unique( seurat_phase1@meta.data[, c("GSM", "Title")])
sample_info #12 Samples
unique(seurat_phase1$Title)

saveRDS( seurat_phase1, file.path( outputDir, "GSE245601_seurat_phase1_normal_vs_tumor_control.rds" ))
```
<img width="1061" height="143" alt="image" src="https://github.com/user-attachments/assets/acc2cb19-e09b-413c-86cb-7dc428e34eb8" /> 

After saving the correct sample data into .RDS I went on to check the QC again, to check if anything else was needed to be omitted before the downstream analysis involving Normalization, UMAP, PCA, Normal v/s Tumor Characterization and Marker Analysis. 

## 2. QC Check before the downstream analysis

```bash
study_id <- "GSE245601"

save_violin_plots_separate <- function( seurat_obj, phase1Dir, study_id,
                                       features = c( "nCount_RNA",  "nFeature_RNA", "percent.mt", "percent.rb" )) {
  
  # Ensure output directory exists
  if (!dir.exists(phase1Dir)) {
    dir.create(phase1Dir, recursive = TRUE)}
  
  # Extract metadata
  meta <- seurat_obj@meta.data
  
  # Create sample identity for x-axis
  meta$sample <- as.factor(meta$orig.ident)
  
  # Generate one plot for each QC metric
  for (feat in features) {
    
    if (!feat %in% colnames(meta)) {
      warning(paste("Skipping", feat, "- not found in metadata"))
      next
    }
    
    p <- ggplot( meta,
      aes(x = sample, y = .data[[feat]]) ) +
      geom_violin(  trim = TRUE, fill = "red", alpha = 0.7 ) +
      geom_boxplot( width = 0.1, outlier.shape = NA, alpha = 0.6 ) +
      labs( title = feat, x = "Sample", y = feat ) +
      theme_bw(base_size = 14) +
      theme( axis.text.x = element_text( angle = 45, hjust = 1 ),
        plot.title = element_text(hjust = 0.5))
    
    ggsave( filename = file.path( phase1Dir, paste0(  study_id, "_Phase1_check_", feat, "_violin.png" )),
      plot = p, width = 16, height = 9,  dpi = 400, bg = "white"  )
  }
}

save_violin_plots_separate( seurat_phase1, phase1Dir, study_id)
```
<img width="1297" height="737" alt="image" src="https://github.com/user-attachments/assets/6e6a9c4c-3bc8-4acb-a36a-75728b9c9ed4" />

<img width="1306" height="746" alt="image" src="https://github.com/user-attachments/assets/7058b9e5-4c3e-4751-b932-bde84316addf" />

<img width="1302" height="747" alt="image" src="https://github.com/user-attachments/assets/c913837e-9d1f-4de0-afa9-4046c47a3df6" />

<img width="1307" height="748" alt="image" src="https://github.com/user-attachments/assets/f0f005b4-c550-4bb6-b323-4553ceeb61d2" />

```bash
sum(seurat_phase1$nCount_RNA > 25000)

seurat_phase1@meta.data %>%
    dplyr::filter(nCount_RNA > 25000) %>%
    dplyr::summarise(
        n_cells = dplyr::n(),
        median_nFeature = median(nFeature_RNA),
        median_nCount = median(nCount_RNA),
        median_mt = median(percent.mt),
        median_rb = median(percent.rb)
    )

seurat_phase1@meta.data %>%
    dplyr::filter(nCount_RNA > 25000) %>%
    dplyr::count(orig.ident, name = "cells_above_25k") %>%
    dplyr::arrange(desc(cells_above_25k))
```

Cells were filtered based on the following QC criteria before: nFeature_RNA ≥ 200 and <7,000, nCount_RNA <100,000, percent.mt <12%, and percent.rb <50%. All the 12 samples which was going to be taken downstream were plotted again. A stricter nCount_RNA cutoff of 25,000 was evaluated but not applied. Although 3,404 cells had nCount_RNA >25,000, these cells were retained because they showed a median of 5,630 detected genes, low median mitochondrial content (2.08%), and no independent indication of poor cell quality. Therefore, a stricter nCount_RNA cutoff of 25,000 was not applied. So the file to be taken forward for the rest of the analysis is `GSE245601_seurat_phase1_normal_vs_tumor_control.rds`. 

## 3. Normalization and Scaling

```bash
# Normalize gene expression values for each cell using LogNormalize.
# Expression counts are normalized by the total RNA count per cell, multiplied by a scale factor of 10,000, and log-transformed.
cat("Normalizing data using LogNormalize method (scale factor = 10,000)...\n")
seurat_phase1_processed <- NormalizeData(  seurat_phase1, normalization.method = "LogNormalize",  scale.factor = 10000)

# Identify highly variable genes using the VST method.
# These genes are used to capture major sources of variationin downstream dimensionality reduction.
seurat_phase1_processed <- FindVariableFeatures( seurat_phase1_processed, selection.method = "vst",  nfeatures = 2500) 
#2,500 is a conventional/default-type choice in many Seurat workflows, not a magical biological cutoff.

# Center and scale the expression values of the selected features. This gives each gene approximately mean = 0 and SD = 1 before PCA.
seurat_phase1_processed <- ScaleData(seurat_phase1_processed)

# Perform PCA using the scaled highly variable genes. Up to 100 PCs are calculated for subsequent dimensionality assessment.
seurat_phase1_processed <- RunPCA( seurat_phase1_processed, npcs = 100)
```
<img width="673" height="513" alt="image" src="https://github.com/user-attachments/assets/d95e2ee1-f3f1-43a7-85b7-1d547c37a325" /> <img width="1317" height="662" alt="image" src="https://github.com/user-attachments/assets/6355da42-9551-41a1-8755-df36c26bc0a9" />

After QC and doublet removal, the filtered dataset was normalized using LogNormalize with a scale factor of 10,000 to reduce differences in sequencing depth between cells and make gene expression values comparable. I then selected 2,500 highly variable genes using the VST method, as these genes capture the major sources of variation across cells and provide informative features for downstream analysis. The selected features were centered and scaled before performing PCA. I initially calculated 100 principal components so that the variance captured by each PC could be evaluated before deciding how many PCs to use for downstream clustering and UMAP analysis.      

## 4. Principal Component Selection and Variance Analysis_Elbow Plot

```bash
study_id <- "GSE245601"
# Get the standard deviation for each PC and calculate the variance explained.
stdev <- seurat_phase1_processed[["pca"]]@stdev

# Calculate the proportion of variance explained by each PC.
var_explained <- stdev^2 / sum(stdev^2)

# Calculate cumulative variance explained across PCs.
cum_var <- cumsum(var_explained)

# Store PCA variance metrics in a data frame.
pca_var_df <- data.frame( PC = 1:length(stdev), Variance = var_explained, CumulativeVariance = cum_var)
pca_var_df

# Identify the number of PCs required to explain at least 95% of the variance.
num_PCs_95 <- min(which(cum_var >= 0.95))
num_PCs_95

# Generate the PCA Elbow Plot.
elbow_plot <- ElbowPlot( seurat_phase1_processed, ndims = 100,  reduction = "pca") +
    labs(title = "PCA Elbow Plot for Dimensionality Selection")

ggsave( file.path(phase1Dir, paste0(study_id, "_ElbowPlot.png")),
    elbow_plot, width = 8, height = 6, bg = "white")
```
<img width="981" height="738" alt="image" src="https://github.com/user-attachments/assets/e98b1d3b-08e5-4668-9a71-810a77ab41aa" />

<img width="1048" height="35" alt="image" src="https://github.com/user-attachments/assets/74bade5a-5472-4497-8703-d758d056a577" />
<img width="361" height="56" alt="image" src="https://github.com/user-attachments/assets/c6728463-ef49-45a2-87c2-fa5f99358cbc" />

```bash
pca_stdev <- seurat_phase1_processed[["pca"]]@stdev  # Extract the standard deviation for each principal component (PC) from the PCA reduction object.
pca_var_explained <- (pca_stdev^2) / sum(pca_stdev^2) * 100  # Calculate the percentage of variance explained by each PC. Variance explained is computed as the squared standard deviation and divided by the total variance, multiplied by 100.
total_var<- sum(pca_var_explained[1:35])  # Compute the total variance explained by the top 35 PCs. This helps quantify how much biological variation is retained when using 35 dimensions for downstream analyses.

cat("Total variance explained by top PCs:", round(total_var, 2), "%\n")   # Print the total variance explained by the top PCs.
##60–85% variance explained is very typical
##100% is neither possible nor desirable (that would mean you kept all the noise)
##80% is a healthy balance between signal retention and noise reduction
```

PCA dimensionality was assessed using the variance explained by individual PCs, cumulative variance, and the elbow plot. The first 100 PCs were evaluated, with the elbow plot showing a major inflection around PC20. The first 35 PCs explained 80.37% of the total variance, while 79 PCs were required to explain 95% of the cumulative variance. Based on the elbow plot and the substantial variance retained by the first 35 PCs, PCs 1–35 were selected for downstream UMAP visualization, nearest-neighbor graph construction, and clustering.     
PCA dimensionality assessment:    
- PCs calculated: 100
- Major elbow: ~PC20
- Variance explained by PCs 1–35: 80.37%
- PCs required for 95% cumulative variance: 79
- PCs selected for downstream analysis: 1–35

## 5. UMAP & Clustering on unintegrated data

```bash
library(glue)
study_id <- "GSE245601"

# Run UMAP using the first 35 PCs selected during PCA assessment.
# UMAP provides a 2D representation of the high-dimensional PCA space while preserving local cell-neighborhood relationships.
seurat_phase1_processed <- RunUMAP(  seurat_phase1_processed, dims = 1:35)

# Construct a nearest-neighbor graph using the first 35 PCA dimensions.
# This graph represents similarity between cells and is used for clustering.
seurat_phase1_processed <- FindNeighbors( seurat_phase1_processed,  dims = 1:35, reduction = "pca", graph.name = "pca_nn")

# Perform graph-based clustering.
# Resolution = 0.8 controls cluster granularity.
seurat_phase1_processed <- FindClusters(  seurat_phase1_processed, resolution = 0.8, graph.name = "pca_nn", cluster.name = "pca_clusters")

# Count the number of identified clusters.
n_clusters <- length(unique(seurat_phase1_processed$pca_clusters))

cat(glue( "Clustering complete. Number of clusters: {n_clusters}\n"))

# Display the number of cells in each cluster.
table(seurat_phase1_processed$pca_clusters)

# UMAP colored by PCA-based clusters, shows the cell types, so cells that are similar stay close together in 2D. Each cluster (represented by a color) groups cells with similar overall gene expression patterns.

umap_clusters <- DimPlot( seurat_phase1_processed, reduction = "umap",  group.by = "pca_clusters", label = TRUE) +
  labs(title = "UMAP: PCA-Based Clusters")

ggsave(filename = file.path(phase1Dir, paste0(study_id, "_UMAP_pca_clusters.png")), plot = umap_clusters,  width = 8,  height = 6,  bg = "white")
```
<img width="983" height="747" alt="image" src="https://github.com/user-attachments/assets/0d485cf1-419c-4ef5-970f-7832b1862350" />

<img width="1167" height="432" alt="image" src="https://github.com/user-attachments/assets/ddd85bb8-1a57-4fbe-9509-0a356e70fe79" />

<img width="1161" height="251" alt="image" src="https://github.com/user-attachments/assets/2f7e3fed-d696-4df5-84fb-93e664521fd8" />

<img width="997" height="81" alt="image" src="https://github.com/user-attachments/assets/545fef9d-0ead-4baa-b217-dc2c9b54ba7b" />

Using the first 35 selected PCs, UMAP was performed to generate a two-dimensional representation of the cellular transcriptomic structure while preserving local neighborhood relationships. A PCA-based nearest-neighbor graph was then constructed using the same 35 PCs, followed by Louvain graph-based clustering at a resolution of 0.8. The analysis included 57,420 cells and resulted in 24 final clusters. Cluster sizes ranged from 121 to 6,672 cells. The resulting UMAP was visualized and saved for downstream assessment of cluster structure and biological identity.    

## 6. clustering by condition and Saving the Unintegrated Seurat Object 

```bash
head(seurat_phase1_processed@meta.data, 3)
colnames(seurat_phase1_processed@meta.data)
table(seurat_phase1_processed$Condition)
umap_condition <- DimPlot(  seurat_phase1_processed,  reduction = "umap", group.by = "Condition") +
    labs(title = "UMAP: Normal vs Tumor (Condition)")

ggsave(  filename = file.path(  phase1Dir, paste0(study_id, "_UMAP_basedon_condition.png") ),
    plot = umap_condition, width = 8, height = 6, bg = "white")

##Inspecting the object first and then saving it
merged <- JoinLayers(seurat_phase1_processed)
sce <- as.SingleCellExperiment(merged, assay = "RNA")
dim(sce) #17430genes x 57420cells
assayNames(sce)
reducedDimNames(sce) # "PCA" "UMAP"
head(colData(sce), 2)
assay(sce, "counts")[1:5, 1:5]  ##counts contains raw expression values
assay(sce, "logcounts")[1:5, 1:5] ##logcounts contains normalized/log-transformed expression
 
phase1Dir <- "D:/Bidya Work/single/GSE245601_Breast_Cancer/Phase1"
h5seurat_name <- "GSE245601_seurat_phase1_normal_vs_tumor.h5seurat"
h5ad_name <- "GSE245601_seurat_phase1_normal_vs_tumor.h5ad"
SaveH5Seurat( object = seurat_phase1_processed, filename = file.path(phase1Dir, h5seurat_name), overwrite = TRUE,  version = "3")
writeH5AD( sce, file = file.path(phase1Dir, h5ad_name), X_name = "counts")
```
<img width="861" height="647" alt="image" src="https://github.com/user-attachments/assets/307d25e2-2799-41d9-87a1-ba1dfa24dfbd" />

<img width="1336" height="452" alt="image" src="https://github.com/user-attachments/assets/6f4bb61c-3922-4fa1-9d1c-b315df1cbf80" />

The UMAP shows the transcriptional distribution of cells across the two experimental conditions: Normal and Tumor. Each point represents a cell, with cells positioned according to similarities in their gene-expression profiles. Cells from the Normal and Tumor conditions were visualized using the Condition metadata variable. The distribution of the two conditions across the UMAP was examined to identify regions showing condition-specific enrichment as well as regions where Normal and Tumor cells overlap. This visualization provides an initial assessment of condition-associated transcriptional structure and helps determine whether Normal and Tumor cells occupy distinct or shared transcriptional states. Further biological interpretation requires examination of cell-type composition, marker expression, clustering, and differential expression analysis.      
The same UMAP generated from the PCA-based clustering was visualized by Condition instead of cluster identity. The original UMAP contains the same 24 transcriptional clusters; only the coloring has been changed to show the two conditions, Normal and Tumor. This allows visualization of how the two conditions are distributed across the existing transcriptional clusters.  

The processed Phase 1 Seurat object was saved in two formats for reproducibility and downstream analysis. The processed Seurat object was saved in H5Seurat format. This preserves the Seurat-based analysis object and provides a convenient disk-based format for storing the processed single-cell dataset. The processed data were also converted to SingleCellExperiment and exported as H5AD. H5AD is commonly used by Python-based single-cell analysis frameworks such as Scanpy, allowing the processed dataset to be used outside the Seurat/R workflow. The H5AD export was configured with the raw expression matrix as the primary counts assay.    

## 7. Run Harmony & Perform Clustering on Harmony integrated data

```bash
## Visualize the existing PCA-based UMAP by biological condition and by individual sample before Harmony integration.
## This helps assess the distribution of Normal/Tumor cells and identify sample-specific structure before batch correction.
p1 <- DimPlot( seurat_phase1_processed,  group.by = "orig.ident",  shuffle = TRUE,  pt.size = 0.5) + 
  labs(title = "UMAP: Sample Distribution (GSM)")
ggsave(file.path(phase1Dir, "before_harmony.png"), plot = p1, width = 15, height = 10, dpi = 300)
```
<img width="967" height="642" alt="image" src="https://github.com/user-attachments/assets/343b3a27-50b7-47d0-ad1f-d1aaf86d2177" />

Before Harmony integration, the PCA-based UMAP was visualized by clusters, biological condition (Normal vs Tumor), and individual GSM/sample. These plots provide a baseline view of the cellular structure, biological condition, and sample-level distribution before integration, helping assess whether cells show distinct clustering, Normal/Tumor separation, or sample-specific structure. ells from most samples were distributed across multiple clusters, with only a few clusters showing more noticeable sample-specific enrichment. These plots provide a baseline for evaluating cellular structure, biological condition, and sample-level distribution before Harmony integration.

```bash
sra_metadata <- read.csv( "D:/Bidya Work/single/GSE245601_Breast_Cancer/SraRunTable.csv",
  stringsAsFactors = FALSE,
  check.names = FALSE)
colnames(sra_metadata)
table( sra_metadata$Instrument, sra_metadata$is_tumor)
sra_control <- sra_metadata[grepl("_Control$", sra_metadata$`Sample Name`),]
sra_control[, c( "Sample Name", "Instrument", "is_tumor", "submitted_subject_id")]
phase1_sra <- subset( sra_metadata, grepl("_Control$", `Sample Name`) & !grepl("^T47D_", `Sample Name`))
table( phase1_sra$Instrument,
  ifelse( grepl("^Normal_", phase1_sra$`Sample Name`), "Normal", "Tumor"))
```
<img width="1340" height="390" alt="image" src="https://github.com/user-attachments/assets/b864a1b1-d727-4e99-b0af-0229a88974dd" />

SRA metadata was examined to identify potential technical confounding between sequencing instrument and biological condition. In the Phase 1 control samples, after excluding T47D samples, all 5 NextSeq 2000 samples were Tumor, while the NextSeq 500 samples included 2 Normal and 5 Tumor samples. Thus, sequencing instrument was partially confounded with biological condition in this dataset. This was considered when interpreting sample-level structure and the subsequent Harmony integration. 

```bash
seurat_phase1_processed <- readRDS( file.path( phase1Dir, "GSE245601_seurat_phase1_PCA_clusters_UMAP.rds" ))

# Run Harmony to integrate data across batches (here, "Sample" is the batch variable)
# Harmony adjusts PCA embeddings to remove batch effects while preserving biological variation
harmony_phase1_processed <- RunHarmony(seurat_phase1_processed, c("GSM"), plot_convergence = TRUE)

# Compute UMAP based on Harmony-corrected embeddings (low-dimensional visualization)
# Using the first 50 Harmony dimensions
harmony_phase1_processed <- RunUMAP(harmony_phase1_processed, reduction = "harmony", dims = 1:50)

# Construct a nearest-neighbor graph from Harmony embeddings for clustering
harmony_phase1_processed <- FindNeighbors(harmony_phase1_processed, reduction = "harmony", dims = 1:50, graph.name = "harmony_nn")

# Perform graph-based clustering on the Harmony-corrected neighbor graph
# The resolution parameter controls the number of clusters (higher = more clusters)
harmony_phase1_processed <- FindClusters(harmony_phase1_processed, graph.name = "harmony_nn", resolution = 0.8, group.name = "Harmony_clusters")
length(unique(harmony_phase1_processed$seurat_clusters))

#found 25 clusters

saveRDS(harmony_phase1_processed, file = paste0(phase1Dir,"GSE245601_harmony_phase1_corrected.rds"))
harmony_phase1_processed <- readRDS(file = paste0(phase1Dir, "GSE245601_harmony_phase1_corrected.rds"))

merged1 = JoinLayers(harmony_phase1_processed) 
sce_phase1_harmony <- as.SingleCellExperiment(merged1, assay = "RNA")

dim(sce_phase1_harmony)
assayNames(sce_phase1_harmony)
reducedDimNames(sce_phase1_harmony)
head(colData(sce_phase1_harmony),2)

phase1Dir <- "D:/Bidya Work/single/GSE245601_Breast_Cancer/Phase1"
h5seurat_name1 <- "GSE245601_harmony_phase1_corrected.h5seurat"
h5ad_name1 <- "GSE245601_harmony_phase1_corrected.h5ad"

SaveH5Seurat(
  object = harmony_phase1_processed,
  filename = file.path(phase1Dir, h5seurat_name1),
  overwrite = TRUE,
  version = "3"
)

writeH5AD( sce_phase1_harmony, file = file.path( phase1Dir,  h5ad_name1 ))
```
<img width="832" height="695" alt="image" src="https://github.com/user-attachments/assets/c1c5366f-35d1-4501-a104-51b47341d7d9" />

<img width="1180" height="427" alt="image" src="https://github.com/user-attachments/assets/4e18e7a7-3364-4f99-a81f-d0893528327b" />

<img width="1147" height="80" alt="image" src="https://github.com/user-attachments/assets/2daa7120-c649-49ae-afb0-af5e7016c879" />

<img width="1202" height="297" alt="image" src="https://github.com/user-attachments/assets/4d342c58-97cd-4e12-b74d-787254f41245" />

<img width="1501" height="287" alt="image" src="https://github.com/user-attachments/assets/4f7d2ec0-fcc5-4fae-9f9a-6b89d10f7c5f" />

<img width="712" height="447" alt="image" src="https://github.com/user-attachments/assets/e86b3bac-d9c2-4f26-ab74-62628c8fdef9" />


Harmony integration was performed using GSM/sample identity to account for sample-level variation. Harmony convergence was monitored during integration by plotting the objective function across clustering steps. The objective function decreased across successive iterations and approached a stable value, indicating convergence of the Harmony optimization process. A Harmony-based UMAP was generated using the first 50 Harmony dimensions, followed by construction of a nearest-neighbor graph and graph-based clustering at a resolution of 0.8. This resulted in 25 clusters. The integrated object was then saved and exported in both H5Seurat and H5AD formats for downstream analysis.    

```bash
p2 <- DimPlot( harmony_phase1_processed, group.by = "orig.ident", shuffle = TRUE, pt.size = 0.5) + 
  labs(title = "HarmonyUMAP: Sample Distribution (GSM)")

ggsave(file.path(phase1Dir, "after_harmony.png"), plot = p2, width = 15, height = 10, dpi = 600)

p3 <- DimPlot( harmony_phase1_processed, group.by = "seurat_clusters", shuffle = FALSE, label = TRUE, pt.size = 0.5) +
  labs(title = "Harmony UMAP: Clusters")

ggsave( file.path(phase1Dir, "harmony_clusters.png"), plot = p3, width = 15, height = 10, dpi = 600)

p4 <- DimPlot( harmony_phase1_processed, group.by = "Condition", shuffle = TRUE, pt.size = 0.5) +
  labs(title = "Harmony UMAP: Normal vs Tumor")

ggsave( file.path(phase1Dir, "harmony_condition.png"), plot = p4,  width = 15, height = 10, dpi = 600)

```
<img width="1738" height="588" alt="image" src="https://github.com/user-attachments/assets/a2a9d8d5-0a27-4c85-a7db-1b16eb5daa12" />
The Harmony-based UMAP was visualized by individual GSM/sample to assess changes in sample-level structure after integration. Compared with the pre-Harmony UMAP, the post-Harmony embedding showed greater mixing of samples across the clusters, with the previously observed sample-enriched regions becoming less apparent. This indicates reduced sample-specific structure following Harmony integration.    

<img width="1817" height="640" alt="image" src="https://github.com/user-attachments/assets/46e7e76b-f525-4319-b29a-9273ecf0dd38" />
Graph-based clustering was performed using the PCA-derived neighborhood graph, resulting in 25 clusters at the selected resolution. The PCA-based UMAP showed distinct groups corresponding to the identified clusters, providing an initial view of the cellular structure before Harmony integration. These clusters were subsequently compared with the Harmony-based clustering results to assess changes in cluster structure after sample-level integration.    

<img width="1791" height="627" alt="image" src="https://github.com/user-attachments/assets/d0500708-9348-43fb-a7b7-a10c2d4d03f4" />
The Harmony-based UMAP was visualized by biological condition (Normal vs Tumor) to assess whether condition-associated structure remained interpretable after sample-level integration. Compared with the pre-Harmony embedding, the post-Harmony UMAP showed somewhat greater mixing of Normal and Tumor cells across the embedding, while condition-associated structure remained visible. This indicates that Harmony altered sample-level structure without completely eliminating the observed biological variation between conditions.

## 8. KNN-Based Batch Mixing Function

```bash
library(ggplot2)
library(FNN)

# Computes the mean fraction of k-nearest neighbors
# that belong to the same batch/sample

compute_knn_batch_mixing <- function( seu, batch_var, reduction = "pca", dims = 1:50, k = 20){
  
  # Check whether the batch metadata column exists
  if (!batch_var %in% colnames(seu@meta.data)) {
    stop( paste0( "Metadata column '",  batch_var, "' not found in seu@meta.data") )
  }
  
  # Check whether the requested dimensional reduction exists
  if (!reduction %in% Reductions(seu)) { stop( paste0( "Reduction '", reduction,
        "' not found in the Seurat object."))
  }
  
  # Extract the selected dimensions
  emb <- Embeddings( seu, reduction = reduction )[, dims, drop = FALSE]
  
  # Find k nearest neighbors
  nn <- FNN::get.knn(  emb, k = k )$nn.index
  
  # Extract batch/sample labels
  labs <- seu@meta.data[[batch_var]]
  
  # Calculate the fraction of neighbors
  # belonging to the same batch as each cell
  same <- vapply( seq_len(nrow(nn)),
    function(i) { mean( labs[nn[i, ]] == labs[i], na.rm = TRUE) },
    numeric(1))
  
  # Store cell-level results
  df <- data.frame( batch = labs, frac_same_batch = same)
  
  # Calculate mean same-batch fraction for each batch
  aggregate( frac_same_batch ~ batch, data = df, FUN = mean)
}

```
UMAP was used as a qualitative and visual assessment of sample-level structure before and after Harmony integration. To complement this visualization, a quantitative K-nearest-neighbor (KNN) analysis was performed to measure sample mixing in the low-dimensional space. For each cell, the 20 nearest neighbors were identified using the PCA embedding before Harmony and the Harmony embedding after integration. The fraction of neighbors belonging to the same GSM was calculated for each cell and summarized by sample. A higher same-GSM neighbor fraction indicates stronger local sample-specific structure, whereas a lower fraction indicates greater mixing between samples. 

```bash
batch_column <- "GSM"

# Before Harmony
mix_df <- compute_knn_batch_mixing(
  seu       = seurat_phase1_processed,
  batch_var = batch_column,
  reduction = "pca",
  dims      = 1:50,
  k         = 20)

# After Harmony
mix_df_harmony <- compute_knn_batch_mixing(
  seu       = harmony_phase1_processed,
  batch_var = batch_column,
  reduction = "harmony",
  dims      = 1:50,
  k         = 20)


# Add Before/Harmony labels and combine
mix_df_combined <- rbind( transform( mix_df, Status = "Before" ),
  transform( mix_df_harmony, Status = "Harmony"))
mix_df_combined$Condition <- seurat_phase1_processed$Condition[ match(
    mix_df_combined$batch, seurat_phase1_processed$GSM )]
unique( mix_df_combined[, c("batch", "Condition")])
# Plot
batch_cor_plot <- ggplot( mix_df_combined, aes( x = batch, y = frac_same_batch, fill = Status )) +
  geom_col( position = position_dodge(width = 0.8), width = 0.7 ) +
  labs( title = "KNN-Based Sample Mixing Before and After Harmony",
    x = "orig.ident",
    y = "Mean same-GSM neighbor fraction") +
  theme_minimal( base_size = 15 ) +
  theme( axis.text.x = element_text( angle = 45,  hjust = 1),
    legend.title = element_blank(),
    plot.title = element_text( face = "bold", hjust = 0.5 ))

batch_cor_plot

# Save plot
ggsave( filename = file.path( phase1Dir, "GSE245601_KNN_batch_mixing_before_after_Harmony.png" ),
  plot = batch_cor_plot, width = 23, height = 8, dpi = 600)
```
The analysis was performed using GSM as the sample/batch identifier, with the first 50 PCA dimensions used before Harmony and the first 50 Harmony dimensions used after integration. The resulting same-GSM neighbor fractions were compared between the pre-Harmony and post-Harmony embeddings. The KNN analysis showed a reduction in the same-GSM neighbor fraction across the analyzed samples after Harmony integration, providing quantitative support for the increased sample mixing observed in the post-Harmony UMAP. The magnitude of this reduction varied between samples, indicating that the degree of sample mixing was not uniform across the dataset. 
Together, the UMAP visualization and KNN-based analysis provide complementary assessments of integration: UMAP provides a qualitative visual assessment of sample distribution, while KNN provides a quantitative, statistics-based measurement of local sample mixing.    

<img width="1402" height="501" alt="image" src="https://github.com/user-attachments/assets/6c126b15-e4fa-4811-bc8b-84b12d60a3e5" />
We wanted to make sure that the clusters were not being formed mainly due to technical differences between the GSM samples. Therefore, we assessed how well cells from different samples mixed after Harmony correction. Sample mixing was first visualized using UMAP to check whether cells from different GSMs were distributed across the same clusters.   
To support the visual observation, we also performed KNN-based sample mixing analysis. This measured how frequently cells from different GSM samples occurred within the local neighbourhood of each cell. Thus, UMAP was used for visual assessment, while KNN provided a quantitative and statistical assessment of sample mixing. Good sample mixing suggests that the clustering was not primarily driven by GSM-specific technical variation.    

## 9. Cell-Type Annotation

After Harmony correction and KNN-based sample-mixing assessment in R, the processed dataset was moved to Python for cell-type annotation. CellTypist was used with the Cells_Adult_Breast.pkl model, which contains reference cell types from adult human breast tissue.
Workflow
AnnData → Harmony-based neighbors → Over-clustering → CellTypist → Majority voting

The resulting cell-type annotations will be used for Normal vs Tumor cell-composition and downstream biological analysis. 
(Download the Python Script Here.)[] 

**Setting up the Python Environment**
The Python environment required for CellTypist was already configured during a previous project. Therefore, the same existing Python kernel was selected for this analysis rather than creating a new environment.  To download and setup a python environment [Please view this readme.md](https://github.com/Bidya122/scRNA-seq-Analysis_kidney_disease).

```bash
import scanpy as sc
import anndata as ad
import pandas as pd
import numpy as np
import celltypist 

print("Scanpy:", sc.__version__)
print("AnnData:", ad.__version__)
```
```bash
import warnings   ## Import the warnings module to manage and silence non-critical system alerts

# Suppress PerformanceWarnings from pandas, which often occur when highly 
# fragmented DataFrames are created during large-scale single-cell data processing
warnings.simplefilter(action='ignore', category=pd.errors.PerformanceWarning)
```
```bash
# Import the os module to interact with the operating system, 
# allowing for file path management and directory navigation
import os
```
```bash
##ad is reserved for AnnData (the library that handles the data structure Scanpy uses).
import anndata as ad ## Import anndata using the 'ad' alias
```
These chunks import the main Python libraries required for single-cell RNA-seq analysis and CellTypist, check the installed package versions, suppress non-critical performance warnings, and import os for managing file paths and directories. AnnData is imported as ad because it is the data structure used by Scanpy to store and process single-cell datasets.

```bash
# Set the current working directory to the specified folder for saving plots
os.chdir("D:/Bidya Work/single/GSE245601_Breast_Cancer/Phase1")
os.getcwd()
```
```bash
# Load the Phase 1 AnnData object
# This object contains the processed Seurat data, including PCA/UMAP and Harmony-corrected information from the R workflow.
GSE245601_phase1 = ad.read_h5ad("D:/Bidya Work/single/GSE245601_Breast_Cancer/Phase1/GSE245601_harmony_phase1_corrected.h5ad")

GSE245601_phase1

# Check the available embeddings

print("Embeddings:", list(GSE245601_phase1.obsm.keys()))
```
<img width="675" height="70" alt="image" src="https://github.com/user-attachments/assets/ff7c53b3-6ca9-494b-a7b2-a9b7a6847c67" />
<img width="190" height="20" alt="image" src="https://github.com/user-attachments/assets/f2a5a726-4576-4f03-836f-2d5b62dc2f9f" />

This section sets the working directory, loads the Harmony-corrected Phase 1 AnnData object generated from the R workflow, and checks the available embeddings such as PCA, UMAP, and Harmony for use in the downstream CellTypist analysis.

```bash
# Check Normal vs Tumor cell counts

print("Condition:")
print(GSE245601_phase1.obs["Condition"].value_counts())

print("\nSeurat clusters:")
print(GSE245601_phase1.obs["seurat_clusters"].value_counts().sort_index())

print("\nGSM samples:")
print(GSE245601_phase1.obs["GSM"].value_counts().sort_index())
```
<img width="270" height="541" alt="image" src="https://github.com/user-attachments/assets/d2e15b31-5dbb-450e-bba0-310ae162aeac" />

This section performs a basic check of the dataset composition after importing the Phase 1 AnnData object. It reports the number of cells in the Normal and Tumor conditions, the number of cells assigned to each Seurat cluster, and the number of cells contributed by each GSM sample. This helps confirm that the expected metadata, clustering information, and sample distribution were successfully retained during the R-to-Python transfer.

```bash
# Load and align UMAP coordinates exported from Seurat

umap_df = pd.read_csv("D:/Bidya Work/single/GSE245601_Breast_Cancer/Phase1/GSE245601_harmony_phase1_umap_coordinates.csv",  index_col=0)

# Verify dimensions and cell matching
print("UMAP dimensions:", umap_df.shape)
print("AnnData cells:", GSE245601_phase1.n_obs)
print(  "All AnnData cells found in UMAP CSV:",  GSE245601_phase1.obs_names.isin(umap_df.index).all())

# Align UMAP coordinates to the exact cell order in AnnData
GSE245601_phase1.obsm["X_umap"] = umap_df.loc[ GSE245601_phase1.obs_names].values

print("Stored UMAP shape:", GSE245601_phase1.obsm["X_umap"].shape)

# Visualize Seurat clusters with axes

sc.pl.umap(
    GSE245601_phase1,
    color="seurat_clusters",
    palette="tab20",
    legend_loc="on data",
    legend_fontsize=9,
    frameon=True,
    size=8)
```
<img width="401" height="318" alt="image" src="https://github.com/user-attachments/assets/f12c23a2-937d-4afc-88c9-d48e4c05eaca" />

This section imports the UMAP coordinates generated in the Seurat workflow, verifies that the dimensions and cell identities match the AnnData object, and aligns the coordinates to the exact cell order. The Seurat clusters are then visualized on the UMAP to confirm the clustering structure.

```bash
# Visualize Normal vs Tumor distribution

sc.pl.umap(
    GSE245601_phase1,
    color="Condition",
    frameon=True,
    size=4,
    alpha=0.5
)
```
<img width="428" height="267" alt="image" src="https://github.com/user-attachments/assets/8fc1c2e4-6b0a-4961-9533-510a75a901a6" />

This section visualizes the Normal vs Tumor cell distribution on the same UMAP embedding. The Condition metadata determines the color of each cell, while size=4 and alpha=0.5 make the dense regions easier to visualize and allow overlapping cells to remain visible. This helps assess whether Normal and Tumor cells are distributed across the overall transcriptional landscape.

```bash
# Calculate Normal vs Tumor composition within each Seurat cluster

cluster_condition = pd.crosstab(
    GSE245601_phase1.obs["seurat_clusters"],
    GSE245601_phase1.obs["Condition"]
)

cluster_condition_pct = (
    cluster_condition
    .div(cluster_condition.sum(axis=1), axis=0)
    * 100
)

print("Cell counts:")
display(cluster_condition)

print("\nPercentage of cells within each cluster:")
display(cluster_condition_pct.round(2))
```
<img width="188" height="547" alt="image" src="https://github.com/user-attachments/assets/fdc211d0-e6c2-4852-b72d-9f0a8af332cc" /> <img width="205" height="538" alt="image" src="https://github.com/user-attachments/assets/fec11b45-99fb-4640-8162-80f2b1a0af8c" />

This section examines the Normal and Tumor composition of each Seurat cluster. First, the number of Normal and Tumor cells in every cluster is calculated using a contingency table. The counts are then converted into percentages within each cluster, allowing us to see whether individual clusters are predominantly composed of Normal or Tumor cells. This provides a quantitative view of the condition distribution across the identified clusters. The cluster-level composition showed substantial variation in Normal and Tumor representation. For example, cluster 3 was predominantly Normal (86.37%), whereas clusters 1, 5, 6, 13, and 15 were strongly Tumor-enriched (>98%). Clusters 8, 9, and 14 showed a relatively mixed Normal–Tumor composition. Very small clusters containing only a few cells were not considered biologically interpretable based on composition alone. 

```bash
import celltypist

print("CellTypist version:", celltypist.__version__)

print("\nAvailable local models:")
print(celltypist.models.models_path)
```
```bash
import os

model_dir = r"C:\Users\HP\.celltypist\data\models"

models = [
    f for f in os.listdir(model_dir)
    if f.endswith(".pkl")
]

for model in sorted(models):
    print(model)
```
The CellTypist installation was verified by checking the installed version and local model directory. The available .pkl files were then listed to confirm that the Cells_Adult_Breast.pkl model was successfully downloaded and available locally for breast cancer cell-type annotation but as I did not have it I had to download it.

```bash
import celltypist

# Show the models available for download
models = celltypist.models.models_description()
print(models)
```
```bash
# Filter CellTypist's model list for models potentially relevant
# to breast cancer / solid-tissue annotation.

keywords = ["breast", "cancer", "tumor", "mammary", "epithelial", "tissue"]

relevant_models = models[
    models["description"].str.contains(
        "|".join(keywords),
        case=False,
        na=False
    )
]

print(relevant_models.to_string(index=False))
```
```bash
import celltypist

# Download the adult human breast CellTypist model
celltypist.models.download_models(
    force_update=False,
    model="Cells_Adult_Breast.pkl"
)

print("Adult breast model downloaded.")
```
```bash
import os
import shutil

source = r"C:\Users\HP\.celltypist\data\models\Cells_Adult_Breast.pkl"
destination = r"D:/Bidya Work/single/GSE245601_Breast_Cancer/Phase1/Cells_Adult_Breast.pkl"

shutil.copy2(source, destination)

print("Copied to:")
print(destination)
print("File exists:", os.path.exists(destination))
```
```bash
GSE245601_phase1.X = GSE245601_phase1.layers["logcounts"].copy()
sc.pp.neighbors( GSE245601_phase1,  use_rep="HARMONY") # 1. Build the neighbor graph using Harmony-corrected embeddings
sc.tl.leiden( GSE245601_phase1,  resolution=10, key_added="celltypist_clusters") # 2. Generate high-resolution clusters for CellTypist over-clustering
print(GSE245601_phase1)
print(GSE245601_phase1.obsm["HARMONY"].shape)
print(GSE245601_phase1.obs["celltypist_clusters"].nunique())

# 3. Predict cell types using the adult human breast model
ct_pred = celltypist.annotate(
    GSE245601_phase1,
    model=r"D:/Bidya Work/single/GSE245601_Breast_Cancer/Phase1/Cells_Adult_Breast.pkl",
    majority_voting=True,
    over_clustering="celltypist_clusters")

# 4. View predicted cell types
print(ct_pred.predicted_labels.head())
```
<img width="852" height="506" alt="image" src="https://github.com/user-attachments/assets/0fb5faf1-a6de-4971-8818-403f67f14f31" />

CellTypist's available model catalogue was reviewed and filtered using breast cancer- and solid-tissue-related keywords to identify suitable models for annotation. The Cells_Adult_Breast.pkl model was selected, downloaded, and copied into the Phase 1 project directory for reproducibility. Before annotation, the Harmony-corrected embedding was used to construct a neighbor graph, followed by Leiden clustering at resolution 10 to generate the clustering structure used for subsequent CellTypist-based cell-type annotation. CellTypist annotation was performed using the Cells_Adult_Breast.pkl model, with 3,787 model features used for prediction. Cell-level predictions were generated and subsequently refined using majority voting across the 141 high-resolution over-clusters. The resulting annotations included breast tissue cell identities such as Fibro-major, Vas-venous, and basal, providing an initial cell-type classification for the Phase 1 dataset.    

```bash
# Count the total number of high-resolution Leiden clusters
# generated for CellTypist over-clustering.
print( f"Number of clusters created: " f"{GSE245601_phase1.obs['celltypist_clusters'].nunique()}")
```
```bash
# Transfer CellTypist majority-voting predictions to the AnnData object.
# Convert labels to strings for compatibility with Scanpy plotting functions.
GSE245601_phase1.obs["majority_voting"] = ( ct_pred.predicted_labels["majority_voting"] .astype(str))

# Display the 5 most frequent cell types.
print( GSE245601_phase1.obs["majority_voting"]
    .value_counts()
    .head())
```
<img width="317" height="37" alt="image" src="https://github.com/user-attachments/assets/c8348e1a-d8a2-47da-8906-90bd789f90ac" /> 


<img width="262" height="172" alt="image" src="https://github.com/user-attachments/assets/e857d477-deb9-4909-8fdb-91ebce385b5b" />        

The CellTypist majority-voting predictions were transferred to the AnnData metadata as a new majority_voting column. The frequency of each predicted cell type was then calculated to provide an initial census of the major cell populations identified in the Phase 1 breast cancer dataset. 

```bash
import os
import matplotlib.pyplot as plt
import scanpy as sc

# Create output directory if it doesn't exist
out_dir = "D:/Bidya Work/single/GSE245601_Breast_Cancer/Phase1/"
os.makedirs(out_dir, exist_ok=True)

# Generate a UMAP for each condition
for cond in GSE245601_phase1.obs["Condition"].unique():

    adata_subset = GSE245601_phase1[
        GSE245601_phase1.obs["Condition"] == cond]

    fig = sc.pl.umap(
        adata_subset,
        color="majority_voting",
        title=f"Condition: {cond}",
        legend_loc="right margin",
        show=False,
        return_fig=True)

    fig.savefig(
        os.path.join(
            out_dir,
            f"umap_celltypes_celltypist_{cond}.png"),
        dpi=300,
        bbox_inches="tight")

    plt.show()
    plt.close(fig)
```
<img width="1647" height="803" alt="image" src="https://github.com/user-attachments/assets/490b7aea-1baf-4a0f-9f24-2c2fde63278d" />

<img width="1638" height="797" alt="image" src="https://github.com/user-attachments/assets/a3fa1010-103d-478b-a1b9-2e71a0fd2c58" />

Automated cell type annotation using CellTypist resolves pronounced compositional and phenotypic remodeling between the normal and tumor microenvironments across the shared UMAP embedding. In normal tissue, the cellular landscape is dominated by balanced, homeostatic epithelial compartments partitioned clearly into distinct luminal and basal states (`LummHR` and `Lumsec-basal`) with only sparse, baseline resident immune populations. In contrast, the tumor condition reveals marked epithelial distortion, characterized by a massive, consolidated expansion of altered luminal subtypes (`LummHR-major` and `Lumsec` lineages) indicative of aberrant lineage fidelity and malignant outgrowth.

Concurrently, the tumor embedding exhibits extensive recruitment and activation of the immune compartment. A dense, multifaceted lymphoid infiltrate emerges in the tumor microenvironment, prominently enriched for cytotoxic and regulatory T-cell subsets (`CD8-Tem`, `CD8-Trm`, and `CD4-Treg`) as well as `NK-ILCs`. This is accompanied by distinct clusters of tumor-associated myeloid and antigen-presenting cells, including `Macro-m2-CXCL`, mature dendritic cells (`mDC`), and `Mast` cells, alongside expanded memory B-cell and plasma cell populations (`bmem` and `plasma_IgA`), highlighting active immune infiltration within an immunosuppressive niche. Meanwhile, stromal (`Fibro-major`, `Fibro-SFRP4`, `vSMC`) and endothelial compartments (`Vas-capillary`, `Vas-arterial`) remain structurally demarcated, reflecting underlying angiogenic remodeling alongside stromal reorganization across disease progression. 

```bash
pd.crosstab(
    GSE245601_phase1.obs["majority_voting"],
    GSE245601_phase1.obs["Condition"]
)
```
<img width="313" height="817" alt="image" src="https://github.com/user-attachments/assets/bfecedfd-d684-4104-b20e-fb8e57362c50" />    
CellTypist-based cell-type composition was compared between Normal and Tumor samples using the majority-voting annotations. Because the number of cells differed substantially between conditions (13,767 Normal vs 43,653 Tumor), comparisons were based on the proportion of cells within each condition rather than raw cell counts. The predicted composition showed notable differences between conditions. Lumsec-basal represented a large fraction of the Normal population (43.16%) but a smaller fraction of the Tumor population (4.24%), whereas several immune populations showed higher relative representation in Tumor, including CD4-naive (8.47%), CD8-Tem (5.66%), CD4-Treg (3.33%), Mast (3.24%), and bmem_switched (3.37%). LummHR-major showed a relatively similar representation between Normal (25.41%) and Tumor (28.39%), while Fibro-major accounted for 9.86% of Normal and 12.46% of Tumor cells. These results describe differences in the CellTypist-predicted cellular composition between conditions and will be further evaluated using marker-gene expression to validate the predicted identities.    

```bash
import matplotlib.pyplot as plt
import seaborn as sns

# Step 1: Count cells for each CellTypist cell type within each condition
n_cells_condition = (
    GSE245601_phase1.obs
    .groupby(["Condition", "majority_voting"])
    .size()
    .reset_index(name="count")
)

# Step 2: Calculate cell-type proportions within each condition
n_cells_condition["total"] = (
    n_cells_condition
    .groupby("Condition")["count"]
    .transform("sum")
)

n_cells_condition["proportion"] = (
    n_cells_condition["count"] /
    n_cells_condition["total"]
) * 100

# Step 3: Order cell types by their average proportion
avg_proportions = (
    n_cells_condition
    .groupby("majority_voting")["proportion"]
    .mean()
    .sort_values(ascending=False)
)

ordered_celltypes = avg_proportions.index.tolist()

# Step 4: Generate horizontal bar plot
plt.figure(figsize=(12, 18))

ax = sns.barplot(
    data=n_cells_condition,
    y="majority_voting",
    x="proportion",
    hue="Condition",
    order=ordered_celltypes,
    dodge=True
)

# Step 5: Add percentage labels
for container in ax.containers:
    ax.bar_label(
        container,
        fmt="%.1f%%",
        label_type="edge",
        padding=2,
        fontsize=8
    )

plt.xlabel("Proportion (%)", fontsize=12, fontweight="bold")
plt.ylabel("Cell Type", fontsize=12, fontweight="bold")
plt.title(
    "Cell Type Proportions: Normal vs Tumor",
    fontsize=14,
    fontweight="bold"
)

plt.tight_layout()

plt.savefig(
    "D:/Bidya Work/single/GSE245601_Breast_Cancer/Phase1/Cellproportions_barplot.png",
    dpi=600,
    bbox_inches="tight"
)

plt.show()

# Save the table
n_cells_condition.to_csv(
    "D:/Bidya Work/single/GSE245601_Breast_Cancer/Phase1/Cellproportions.csv",
    index=False
)
```
<img width="635" height="941" alt="image" src="https://github.com/user-attachments/assets/be53c447-7218-4441-aadc-3e39c3989233" />

<img width="878" height="557" alt="image" src="https://github.com/user-attachments/assets/5ba80ead-41b0-4cd7-8f3b-93601a5f1d95" />

CellTypist-derived cell-type proportions were compared between Normal and Tumor samples. The observed cellular composition differed substantially between the two conditions. Lumsec-basal cells represented the largest population in Normal samples (43.16%) but a smaller proportion of Tumor cells (4.24%). In contrast, several immune populations showed higher observed proportions in Tumor samples, including CD4-naive (0.57% vs 8.47%), CD8-Tem (0.16% vs 5.66%), CD4-Treg (0.01% vs 3.33%), and Mast cells (0.04% vs 3.24%). Fibro-major cells also increased in relative proportion from 9.86% in Normal to 12.46% in Tumor, while LummHR-SCGB increased from 1.29% to 5.78%. Among vascular populations, Vas-arterial cells decreased from 4.10% to 0.57%. These results describe differences in the observed cellular composition of the retained Normal and Tumor cell populations; they are descriptive and do not by themselves establish statistical significance or biological enrichment.    

```bash
# Create a combined cell-type + condition label
# Format: "CellType_Condition" (e.g., "Lumsec-basal_Normal")

GSE245601_phase1.obs["celltype_condition"] = (
    GSE245601_phase1.obs["majority_voting"].astype(str)
    + "_"
    + GSE245601_phase1.obs["Condition"].astype(str))
GSE245601_phase1.obs["celltype_condition"].value_counts()
```
<img width="177" height="626" alt="image" src="https://github.com/user-attachments/assets/1ab1fa25-0d7c-4c0b-930f-ff24e5d3811a" />


```bash
def find_unique_markers(
    adata, 
    groupby="celltype_condition", 
    method="wilcoxon", 
    pval_threshold=0.05, 
    logfc_threshold=0.25,
    top_n=3,
    min_cells_per_group=2
):
    """
    Identify significant and unique DEGs for each cell-type/condition group.
    """

    # 1. Remove groups with too few cells
    group_counts = adata.obs[groupby].value_counts()
    valid_groups = group_counts[
        group_counts >= min_cells_per_group
    ].index.tolist()

    # 2. Subset to valid groups
    adata_filtered = adata[
        adata.obs[groupby].isin(valid_groups)
    ].copy()

    # 3. Differential expression analysis
    sc.tl.rank_genes_groups(
        adata_filtered,
        groupby=groupby,
        method=method )

    # 4. Extract DE results
    all_degs = sc.get.rank_genes_groups_df(
        adata_filtered,
        group=None )

    # 5. Filter by adjusted p-value and logFC
    filtered_degs = all_degs[
        (all_degs["pvals_adj"] < pval_threshold) &
        (abs(all_degs["logfoldchanges"]) > logfc_threshold)]

    # 6. Keep genes significant in only one group
    unique_genes = (
        filtered_degs
        .groupby("names")["group"]
        .nunique()
        .reset_index()
        .query("group == 1")["names"]
        .tolist() )

    # 7. Keep only unique DEGs
    unique_degs_df = filtered_degs[
        filtered_degs["names"].isin(unique_genes)
    ].copy()

    # 8. Select top N DEGs per group
    top_unique_degs_df = (
        unique_degs_df
        .sort_values(
            ["group", "logfoldchanges"],
            ascending=[True, False] )
        .groupby("group")
        .head(top_n)
        .reset_index(drop=True) )

    return top_unique_degs_df


# Run on GSE245601 Phase 1
top_markers = find_unique_markers(
    GSE245601_phase1,
    groupby="celltype_condition",
    method="wilcoxon",
    pval_threshold=0.05,
    logfc_threshold=0.25,
    top_n=3,
    min_cells_per_group=2)

print(top_markers)
```

```bash
# 1. Identify the top 3 highly specific markers for each cell type/condition group
# Uses the Wilcoxon rank-sum test to identify significant genes
# that are exclusive to only one group.

unique_markers = find_unique_markers(
    GSE245601_phase1,
    groupby="celltype_condition",
    method="wilcoxon",
    pval_threshold=0.05,
    logfc_threshold=0.25,
    top_n=3,
    min_cells_per_group=2
)

# 2. Validation and Export
print(unique_markers.head())

print(
    unique_markers["group"].drop_duplicates()
)

unique_markers.to_csv(
    "D:/Bidya Work/single/GSE245601_Breast_Cancer/Phase1/Unique_cluster_markers.csv",
    index=False
)
```
group	names	scores	logfoldchanges	pvals	pvals_adj
CD4-Th_Tumor	B3GALT2	3.8123035	4.65608	0.000137678	0.000483913
CD4-Treg_Tumor	FOXP3	17.772234	9.019729	1.16E-70	5.03E-69
CD4-Treg_Tumor	LAIR2	4.1807265	7.009485	2.91E-05	9.28E-05
CD4-Treg_Tumor	GNG8	4.6847134	6.735691	2.80E-06	9.89E-06
CD4-naive_Tumor	CD40LG	3.6399949	3.9734037	0.000272644	0.000586906
CD4-naive_Tumor	FXYD7	2.6543088	3.258666	0.007947104	0.015218416
CD4-naive_Tumor	RNF157	2.3582373	2.2330937	0.018361956	0.033871192
CD8-Tem_Tumor	DTHD1	9.7075615	5.664247	2.80E-22	1.51E-21
CD8-Tem_Tumor	PACSIN1	2.5291317	5.035926	0.011434512	0.023255955
CD8-Tem_Tumor	VNN2	2.402299	4.627514	0.016292385	0.032450721
Fibro-SFRP4_Tumor	CADM3	4.351523	6.814854	1.35E-05	6.93E-05
Fibro-SFRP4_Tumor	WIF1	3.3871772	6.632409	0.000706157	0.002792269
Fibro-SFRP4_Tumor	AFP	4.12612	6.4899077	3.69E-05	0.000177492
Fibro-major_Normal	CAPN6	3.9097157	6.0387454	9.24E-05	0.000412133
Fibro-major_Normal	FRMD1	2.837395	4.9622884	0.004548332	0.015615014
Fibro-major_Normal	DBX2	2.5149212	4.668783	0.011905898	0.037499061
Fibro-major_Tumor	SLC24A2	6.9477243	7.0973754	3.71E-12	1.37E-11
Fibro-major_Tumor	KLK4	8.009791	6.4652486	1.15E-15	4.73E-15
Fibro-major_Tumor	ADAMTS16	3.0974817	6.2851396	0.001951725	0.004394028
LummHR-SCGB_Tumor	DIO1	9.12257	6.171775	7.34E-20	2.31E-19
LummHR-SCGB_Tumor	HS3ST6	6.575408	5.927587	4.85E-11	1.25E-10
LummHR-SCGB_Tumor	IGSF21	17.8053	5.480395	6.43E-71	4.34E-70
LummHR-major_Normal	LPAR3	2.719302	2.3633559	0.006541984	0.016675458
LummHR-major_Normal	CTNND2	2.6082315	2.285144	0.00910114	0.022687767
LummHR-major_Normal	MNT	-2.3383176	-0.31212363	0.019370782	0.045607556
LummHR-major_Tumor	TUBA3E	2.6136675	6.491005	0.008957616	0.015308486
LummHR-major_Tumor	KRTAP3-2	10.267497	5.6118736	9.87E-25	2.81E-24
LummHR-major_Tumor	TUBA3D	4.3968215	5.4888744	1.10E-05	2.16E-05
Lumsec-KIT_Tumor	MAFA	3.7816024	4.2510166	0.000155822	0.000522103
Lumsec-basal_Normal	CLDN10	8.124776	4.741779	4.48E-16	3.35E-15
Lumsec-basal_Normal	CSN1S1	4.288814	4.626049	1.80E-05	6.92E-05
Lumsec-basal_Normal	SERPINB7	6.9019156	4.241206	5.13E-12	3.16E-11
Lumsec-basal_Tumor	VGLL1	4.7400503	6.1216016	2.14E-06	1.75E-05
Lumsec-basal_Tumor	KRTAP2-3	9.423462	5.6919365	4.36E-21	1.26E-19
Lumsec-basal_Tumor	GSTA1	5.555621	5.3366156	2.77E-08	2.86E-07
Lumsec-prol_Tumor	SPC25	10.43019	10.2453	1.81E-25	2.78E-23
Lumsec-prol_Tumor	PBK	11.424761	9.293164	3.15E-30	7.21E-28
Lumsec-prol_Tumor	HJURP	7.913549	9.207227	2.50E-15	1.14E-13
Macro-lipo_Tumor	MCEMP1	3.8354561	8.979352	0.000125331	0.000636517
Macro-lipo_Tumor	TREM2	4.6423373	8.017723	3.44E-06	2.15E-05
Macro-lipo_Tumor	LILRB2	7.71956	7.9060106	1.17E-14	1.42E-13
Macro-m2-CXCL_Tumor	NIPAL4	4.8231554	5.868719	1.41E-06	9.77E-06
Macro-m2-CXCL_Tumor	HTRA4	4.802038	5.4282646	1.57E-06	1.08E-05
Macro-m2-CXCL_Tumor	FOLR2	2.4692614	4.9527335	0.013539224	0.049065678
Mast_Tumor	GATA1	6.628209	9.473623	3.40E-11	2.53E-10
Mast_Tumor	SIGLEC6	4.7632623	9.136103	1.90E-06	9.13E-06
Mast_Tumor	TRIM63	5.2159696	9.077504	1.83E-07	9.71E-07
NK-ILCs_Tumor	SH2D1B	4.909967	7.922236	9.11E-07	4.51E-06
NK-ILCs_Tumor	KLRF1	5.8989053	7.194981	3.66E-09	2.38E-08
NK-ILCs_Tumor	ADGRG3	2.366046	6.0949593	0.017979214	0.047124575
Vas-capillary_Tumor	VIP	2.6778905	3.981964	0.007408742	0.019057611
Vas-capillary_Tumor	TMCC2	2.3861225	3.4003015	0.017027082	0.041111239
Vas-capillary_Tumor	LRIG2	-2.5092752	-1.0304465	0.012097916	0.029927147
Vas-venous_Tumor	PHOSPHO1	2.9744866	6.907054	0.002934793	0.014678175
Vas-venous_Tumor	SLCO2A1	3.5651398	5.4597282	0.000363662	0.002210895
Vas-venous_Tumor	SLC10A6	3.1777759	5.4018536	0.001484095	0.00794953
basal_Normal	MRGPRX3	4.012684	6.0801854	6.00E-05	0.000712295
basal_Normal	SLC27A6	3.5827196	5.386661	0.000340036	0.003311073
basal_Normal	RNF183	4.059156	4.0094166	4.93E-05	0.000600873
basal_Tumor	LMOD1	4.0668716	4.51274	4.76E-05	0.000501517
basal_Tumor	NOG	2.8533564	3.736045	0.004326009	0.027731641
basal_Tumor	KLHL13	3.4730952	3.6800802	0.000514493	0.00422602
bmem_switched_Tumor	COL19A1	2.6634676	8.700417	0.007733987	0.018735705
bmem_switched_Tumor	TCL1A	3.4129863	8.16944	0.000642552	0.001808149
bmem_switched_Tumor	FCER2	2.2989628	7.842661	0.021507044	0.048715762
bmem_unswitched_Tumor	FCRL4	5.6834774	8.753288	1.32E-08	2.61E-07
bmem_unswitched_Tumor	CD1C	3.9299557	7.404049	8.50E-05	0.000887286
bmem_unswitched_Tumor	CLEC17A	4.03871	6.7537274	5.37E-05	0.000584034
mDC_Tumor	AOC1	4.001543	11.838259	6.29E-05	0.000944775
mDC_Tumor	LAMP3	17.337946	10.771255	2.43E-67	2.49E-64
mDC_Tumor	CCL17	7.977692	9.408492	1.49E-15	8.61E-14
pericytes_Tumor	CCL21	4.525155	5.830129	6.04E-06	5.98E-05
pericytes_Tumor	LGI4	3.2248082	5.613811	0.001260571	0.007758386
pericytes_Tumor	FAM162B	3.3037984	5.46807	0.000953844	0.006052239
plasma_IgA_Tumor	TNFRSF17	21.254763	9.854881	2.98E-100	2.26E-97
plasma_IgA_Tumor	DUSP26	6.0219774	9.293736	1.72E-09	2.40E-08
plasma_IgA_Tumor	DERL3	27.351984	8.92808	1.02E-164	2.97E-161
vsmc_Tumor	SRL	12.050127	7.057829	1.94E-33	1.13E-31
vsmc_Tumor	FHL5	4.9599757	6.5385475	7.05E-07	6.89E-06
vsmc_Tumor	RERGL	7.9282017	6.47885	2.22E-15	5.02E-14
<img width="677" height="1945" alt="image" src="https://github.com/user-attachments/assets/5dcdc4f4-9931-47e8-9f0a-dbc4fd62664b" />

































































## Project Status

🚧 **Work in progress**

The project is being developed incrementally, with each stage of the analysis documented to maintain reproducibility and facilitate understanding of the underlying biological and computational methods.
