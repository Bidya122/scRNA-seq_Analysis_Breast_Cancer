# Tumor Microenvironment Functional State Analysis
This section presents the cell-type-specific functional characterization of the breast tumor microenvironment using Tumor versus Normal Reactome GSEA results. Following the identification of cellular composition, GSEA was used to determine the biological processes and pathway-level changes associated with each cell population. Each cell type is interpreted independently based on its significant enrichment patterns, with both Tumor-associated and Normal-associated pathways considered. This approach provides a functional view of how individual cellular populations within the TME differ between Tumor and Normal tissue.     
So, after developing the workbook I analysed each and every cell type individually.     

**GSEA Filtering Criteria** - For interpretation, only pathways with an adjusted *P*-value < 0.05 were considered statistically significant. The normalized enrichment score (NES) was used to determine the direction of enrichment: positive NES values indicate enrichment toward the Tumor state, whereas negative NES values indicate enrichment toward the Normal state. Both directions were retained to capture the complete cell-type-specific functional landscape.

## Celltype - BASAL

<img width="1066" height="697" alt="image" src="https://github.com/user-attachments/assets/c5a9f7fa-1d14-444b-8ddb-24583b42174b" /> 

<img width="1396" height="937" alt="image" src="https://github.com/user-attachments/assets/ce5c8869-f494-49d9-88f3-fecac0b0e1fb" /> 

Basal epithelial cells showed Tumor-associated enrichment of syndecan interactions and ECM proteoglycan pathways, suggesting altered extracellular matrix–associated interactions within the breast tumor environment. Several pathways related to mRNA processing, splicing, ribosomal RNA processing, and translation were also enriched toward the Tumor state, indicating broader changes in RNA and protein biosynthetic activity. In contrast, Normal-associated enrichment included mammary gland developmental pathways related to luminal epithelial and alveolar lineages, together with pathways involving cytokine signaling, neutrophil degranulation, CSF3/G-CSF signaling, antigen processing and cross-presentation, and IL-10 signaling. The enrichment of mammary developmental pathways toward Normal is particularly relevant in the context of normal breast epithelial biology. Overall, the basal population displays Tumor-associated ECM/proteoglycan and biosynthetic programs alongside reduced enrichment of mammary developmental programs and selected immune-associated pathways relative to Normal breast tissue.     

## Celltype - BMEM_SWITCHED = Switched Memory B Cells
Switched → Class-switched, meaning they have undergone immunoglobulin class switching (e.g., from IgM/IgD toward IgG, IgA, or IgE).

<img width="1066" height="49" alt="image" src="https://github.com/user-attachments/assets/346957b0-061f-4c19-bac6-2af8f350c7f0" />

<img width="1097" height="731" alt="image" src="https://github.com/user-attachments/assets/7b9cabd7-5f34-4f43-a8ee-f3d7f52ef9fc" />

The bmem_switched population showed a single significant Tumor-associated pathway, phosphorylation of CD3 and TCR zeta chains (NES = 2.03, adjusted P = 0.0185), a pathway associated with T-cell receptor signaling. As this population was annotated as switched memory B cells, this isolated enrichment should be interpreted cautiously and is not sufficient on its own to define a broader functional state. No additional significant pathways were identified at the applied adjusted P < 0.05 threshold.    







