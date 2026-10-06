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

## Celltype - CD4-NAIVE (NAIVE T-CELLS)

<img width="1066" height="2281" alt="image" src="https://github.com/user-attachments/assets/02363514-1c95-4a2a-82b0-917c4657458d" />

<img width="1107" height="748" alt="image" src="https://github.com/user-attachments/assets/4a93bf98-c392-40b7-a852-bde8dc8e9b29" />

CD4-naive T cells showed Tumor-associated enrichment of pathways related to T-cell signaling and cellular interactions, including second-messenger generation, RAC1 and CDC42 GTPase cycling, phosphorylation of CD3 and TCR zeta chains, and immunoregulatory interactions between lymphoid and non-lymphoid cells. PI3K-related signaling was also enriched toward Tumor. Together, these pathways suggest altered T-cell signaling and cell-interaction programs within the breast tumor microenvironment. As naive CD4 T cells can undergo activation and differentiate into different effector or regulatory states, these changes may reflect an altered signaling environment associated with subsequent T-cell functional responses, although a specific differentiation state cannot be inferred from these pathways alone. In contrast, the Normal-associated profile included broader enrichment of interleukin signaling, cellular stress responses, immune-related processes, cell-cycle-associated pathways, and developmental programs. Pathways related to cellular responses to hypoxia and regulation of PD-L1 were also enriched toward Normal. Strong enrichment of mammary gland developmental, keratinization, and cornified-envelope pathways was observed in the Normal state; because these pathways are unexpected for CD4-naive T cells, they should be interpreted cautiously and verified using the underlying leading-edge genes.    
Overall, CD4-naive cells exhibited Tumor-associated enrichment of T-cell signaling and interaction programs, while Normal cells showed a broader set of immune, stress-response, developmental, and regulatory pathways.    

## Celltype - CD4-TEM = CD4 Effector Memory T Cells

<img width="1066" height="97" alt="image" src="https://github.com/user-attachments/assets/1363aeed-ecfc-488b-99ce-84cbacb34e75" />

<img width="1097" height="737" alt="image" src="https://github.com/user-attachments/assets/bbde8526-a896-4b8e-bca8-e37aa8d7fc5c" />

CD4-Tem cells showed Tumor-associated enrichment of the Cell Cycle pathway (NES = 1.68, adjusted P = 0.040), suggesting increased cell-cycle-related activity in the Tumor state. In contrast, Interleukin-4 and Interleukin-13 signaling (NES = −1.94, adjusted P = 0.045) and Interleukin-10 signaling (NES = −2.13, adjusted P = 0.017) were enriched toward Normal. These pathways indicate differences in cytokine-associated signaling within the CD4-Tem population between Normal and Tumor breast tissue.    
Overall, CD4-Tem cells showed a relatively limited functional shift, characterized by Tumor-associated cell-cycle activity and Normal-associated IL-4/IL-13 and IL-10 signaling.

## Celltype - CD4-TH LIKE = CD4 T-helper-like cells

<img width="1170" height="241" alt="image" src="https://github.com/user-attachments/assets/0c257d34-8b70-423c-b5cc-40469439482e" />

<img width="1097" height="738" alt="image" src="https://github.com/user-attachments/assets/cb75a001-4f38-4b8d-ba88-6036d718b584" />

CD4-Th-like cells showed exclusively Normal-associated enrichment, with pathways related to mammary gland developmental lineages, extracellular matrix organization, RND3 GTPase cycling, developmental cell lineages, and post-translational protein phosphorylation. Strong enrichment was also observed for keratinization and cornified-envelope formation, together with regulation of IGF transport and uptake by IGF-binding proteins. The enrichment of mammary developmental, keratinization, and cornified-envelope pathways is notable but unexpected for a CD4-Th-like population and should therefore be interpreted cautiously. These pathways may warrant examination of the underlying leading-edge genes before assigning a specific biological meaning.    
Overall, the CD4-Th-like population showed a predominantly Normal-associated functional profile, with no significant Tumor-associated pathways detected at the adjusted P < 0.05 threshold.    

## Celltype - FIBRO-MAJOR = Major Fibroblast Population

<img width="1066" height="1921" alt="image" src="https://github.com/user-attachments/assets/eaf2ac3f-9bf2-4a2a-af60-bbe6da0d124a" />

<img width="1105" height="746" alt="image" src="https://github.com/user-attachments/assets/2f5c1002-654c-4838-8a7f-eb5cebc59773" />

Fibro-major cells showed a strong Tumor-associated extracellular matrix remodeling program, with prominent enrichment of collagen fibril assembly, collagen formation, collagen biosynthesis and modification, collagen degradation, extracellular matrix organization, ECM proteoglycans, fibronectin matrix formation, and elastic fibre formation. Pathways involving integrin and non-integrin ECM interactions, MET signaling, and cell motility were also enriched toward Tumor, indicating extensive changes in ECM organization, remodeling, and cell–matrix interactions within the breast tumor microenvironment. Additional enrichment of PDGF signaling and IGF-related regulation further supports altered stromal signaling in the Tumor state.    
In contrast, Fibro-major cells showed extensive Normal-associated enrichment of immune and cytokine signaling, including interleukin signaling, cytokine signaling in the immune system, IL-1 signaling, IL-4/IL-13 signaling, and pathways involving NF-κB and type I interferon responses. Several pathways related to RNA processing, translation, ribosome function, and cellular stress responses were also enriched toward Normal.    
Overall, Fibro-major cells displayed a pronounced Tumor-associated ECM and stromal remodeling phenotype, characterized by coordinated collagen, proteoglycan, fibronectin, and cell–ECM interaction pathways, whereas the Normal state showed greater enrichment of immune signaling and broad cellular biosynthetic programs. The simultaneous enrichment of collagen synthesis and degradation pathways in Tumor suggests active ECM turnover and remodeling rather than simply increased collagen production.    

## Celltype - LUMM HR MAJOR = Major Luminal Hormone-Receptor–positive epithelial population

<img width="1066" height="2929" alt="image" src="https://github.com/user-attachments/assets/a6331e61-e923-4edd-ba61-8f6fa02b1b9b" />

<img width="1097" height="736" alt="image" src="https://github.com/user-attachments/assets/3dd5a855-e170-4b7f-8a26-11abea69dd3b" />

LummHR-major cells showed Tumor-associated enrichment of pathways related primarily to mitochondrial function and cellular biosynthetic activity. These included mitochondrial translation, mitochondrial protein import, mitochondrial ribosome-associated quality control, respiratory electron transport, and Complex I biogenesis. Additional enrichment of mRNA splicing, tRNA processing, translation, and cholesterol biosynthesis suggests increased mitochondrial and protein-production activity in the Tumor state.
In contrast, the Normal state showed broader enrichment of cell signaling, immune-related pathways, extracellular matrix interactions, and epithelial developmental programs. These included cytokine and interleukin signaling, Rho GTPase and MAPK signaling, TGF-β signaling, receptor tyrosine kinase signaling, and pathways involving integrins and ECM organization. Mammary gland developmental lineages, keratinization, and cornified-envelope formation were also enriched toward Normal, which is particularly relevant to the differentiated epithelial characteristics of normal breast tissue. Overall, LummHR-major cells displayed Tumor-associated mitochondrial and biosynthetic activity, whereas the Normal state showed stronger enrichment of signaling, immune, ECM-interaction, and mammary epithelial developmental programs.    

## Celltype - LUMM HR SCGB = Luminal Hormone-Receptor–positive SCGB-expressing epithelial population

<img width="1066" height="1609" alt="image" src="https://github.com/user-attachments/assets/c968acf5-c7dc-4078-9e2f-71dbc85174ea" />

<img width="1101" height="742" alt="image" src="https://github.com/user-attachments/assets/263fd53e-1983-457b-a963-7c65823e97e3" />

LummHR-SCGB cells showed Tumor-associated enrichment of pathways related predominantly to mitochondrial function and energy metabolism, including respiratory electron transport, mitochondrial translation, mitochondrial protein import, Complex I biogenesis, and aerobic respiration. Translation and protein localization pathways were also enriched toward Tumor, suggesting increased mitochondrial and biosynthetic activity in this population.
In contrast, the Normal state showed broader enrichment of cell signaling, immune-related pathways, extracellular matrix interactions, and epithelial developmental programs. These included cytokine and interleukin signaling, receptor tyrosine kinase and GPCR signaling, TGF-β-related pathways, and several ECM-associated processes such as collagen formation, ECM organization, integrin interactions, and ECM proteoglycans. Mammary gland developmental pathways, keratinization, and cornified-envelope formation were also enriched toward Normal.
Overall, LummHR-SCGB cells displayed Tumor-associated mitochondrial and respiratory activity, whereas the Normal state showed stronger enrichment of signaling, immune, ECM-related, and mammary epithelial developmental programs.    

## Celltype - LUMMSEC BASAL = Luminal Secretory–Basal epithelial population

<img width="1066" height="2401" alt="image" src="https://github.com/user-attachments/assets/0fbae02d-55c4-416f-ae35-3429a6a98bff" />

<img width="1100" height="737" alt="image" src="https://github.com/user-attachments/assets/6d286412-eeae-468f-a6e2-27446dceee22" />

Lumsec-basal cells showed strong Tumor-associated enrichment of RNA processing, translation, and mitochondrial activity. Major pathways included mRNA splicing, mRNA processing, translation, rRNA processing, mitochondrial translation, mitochondrial protein import, Complex I/IV assembly, and respiratory electron transport. Keratinization and cornified-envelope formation were also enriched toward Tumor, suggesting altered epithelial differentiation programs. In contrast, the Normal state showed stronger ECM and cell–microenvironment signaling, including extracellular matrix organization, collagen formation and degradation, ECM proteoglycans, laminin/integrin interactions, cytokine and interleukin signaling, and receptor-mediated pathways such as GPCR, RTK, PDGF, insulin, and IGF signaling.    
Overall, Lumsec-basal cells displayed Tumor-associated biosynthetic and mitochondrial activity, whereas Normal cells showed stronger ECM organization, signaling, and tissue–microenvironment interaction programs.    

## Celltype - Luminal Secretory KIT-expressing epithelial population

<img width="1066" height="121" alt="image" src="https://github.com/user-attachments/assets/74e24bc0-f58d-4742-809a-b794035bc55e" />

<img width="1102" height="740" alt="image" src="https://github.com/user-attachments/assets/b2f39577-90e1-4462-93ae-ee14cce3c102" />

Lumsec-KIT cells showed exclusively Normal-associated enrichment, with pathways related to the immune system, innate immune responses, post-translational protein modification, and vesicle-mediated transport. This suggests stronger immune-related and cellular transport activity in Lumsec-KIT cells from Normal breast tissue compared with Tumor. Overall, Lumsec-KIT displayed a limited Normal-associated functional profile, with no significant Tumor-associated pathways detected at the adjusted P < 0.05 threshold.    

## Celltype - Lipid-associated Macrophage population

<img width="1066" height="409" alt="image" src="https://github.com/user-attachments/assets/164a34ff-2467-4e94-af5f-586cf6fbb6eb" />

<img width="1101" height="740" alt="image" src="https://github.com/user-attachments/assets/1900763a-bf90-4bca-9200-68e29274dcf0" />

Macro-lipo cells showed Tumor-associated enrichment of cholesterol-related signaling, particularly NR1H2/NR1H3-mediated regulation of cholesterol transport and efflux. In contrast, the Normal state showed enrichment of ribosome quality-control, ECM interaction, transport, and developmental pathways. Notably, mammary gland luminal epithelial developmental pathways were strongly enriched toward Normal, together with keratinization and other tissue-development programs. These epithelial-associated pathways are unexpected for a macrophage population and should therefore be interpreted cautiously. Overall, Macro-lipo showed a Tumor-associated cholesterol-regulatory program, whereas Normal cells displayed broader developmental and cellular maintenance programs.    

## Celltype - M2-like CXCL-expressing Macrophage population (m2 = M2-like / alternatively activated macrophage phenotype, CXCL = Chemokine (C-X-C motif) ligand–associated) 

<img width="1066" height="97" alt="image" src="https://github.com/user-attachments/assets/02e61ea3-9113-477d-84ae-c90c4e1d76f5" />

<img width="1097" height="738" alt="image" src="https://github.com/user-attachments/assets/9d0726a2-e21e-4805-9a8e-164e9e95e885" />

Macro-m2-CXCL cells showed Tumor-associated enrichment of lipid and nuclear receptor-related programs, including plasma lipoprotein assembly, remodeling and clearance, and nuclear receptor transcriptional signaling. Regulation of TP53 activity was also enriched toward Tumor, suggesting altered cellular stress and regulatory signaling. Overall, Macro-m2-CXCL cells displayed a Tumor-associated lipid-handling and transcriptional regulatory phenotype, with no significant Normal-associated pathways detected at the adjusted P < 0.05 threshold.    


 
