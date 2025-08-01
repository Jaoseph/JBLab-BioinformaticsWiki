# Quick and dirty fundamental of running scRNASeq analysis (Seurat)

##### _Funny quote coming for something not funny_

--- 

## `Pre-requisties`

Make sure that you have a conda environment set-up for specifically for scRNASeq workflows and analyses. If you do not, please check out "Conda- A Recipe for Reproducible Research"

The quick and dirty way is to run:

```bash
conda create -n scrnaseq -c conda-forge -c bioconda r-base r-seurat r-patchwork r-dplyr r-ggplot2

```

---

## `Raw FASTQ data`

The sequencing data for your scRNA (even from Genomics Core) often comes in the format of a FASTQ, a text-based format which stores read sequences.

In short: 
A sequence starts with @ followed by a ReadID, then the READ sequence, a separator (+ sign) and sequencing quality including but not limited to: Per base sequence quality, Per base sequence GC content, Overrepresented Sequences, etc.

```bash
@ReadID 
READ SEQUENCE 
+
SEQUENCING QUALITY SCORES
```
A basic command to show the first three reads (given that each read is 12 lines) of a compressed FASTQ file (fastq.gz) is as follows:

```bash
zcat filename.fastq.gz | head -n 12
```

or (if uncompressed FASTQ files)

```bash
head -n 12 filename.fastq
```

---

## `CellRanger Set-up`

The first step of the scRNASeq analysis is to **align the sequenced reads** against the genomic reference (*Human reference genome*) and transcriptome annotations to generate read counts for each feature (*often genes*).

> Note: CellRanger is VERY computationally intensive. Please run on a high performance computing (HPC) cluster.

### Installation

To install CellRanger, please follow the [10x website](https://support.10xgenomics.com/single-cell-gene-expression/software/pipelines/latest/installation)'s instructions

--- 

### Download Reference Genome

For standard genome/transcriptomes of human and/or mouse, head to the [10x website](https://www.10xgenomics.com/support/software/cell-ranger/downloads) to download pre-built references.

If you require a reference genome with custom transcripts to gene annotations, you need to build your own index. Please ask for help. 

For standard human reference genome/transcriptome, the genome/transcriptome should be both downloaded from the [10x website](https://www.10xgenomics.com/support/software/cell-ranger/downloads) named **Homo_sapiens.GRCh38.dna.toplevel.fa.gz**

To index our genome, run:

```bash
cellranger mkref \
  --fasta=Homo_sapiens.GRCh38.dna.toplevel.fa \
  --genes=gencode.v41.primary_assembly.annotation.chr21.gtf \
  --genome=GRCh38_index \
  --nthreads=7
```

> Note: cellrange mkref does not work with gzip (.gz) files, make sure to decompress the files by using:
```bash
gunzip file.gz
```
---

## `The following demonstration will be done on Dr.Middleton's WILDseq single cell run, sample SITTC1 which is a control CIOV6 cell line with no Rucaparib treatment`

Although most of you will not be doing WILDSeq barcoding experiments, the demonstration will not show the WILDSeq barcode specific codes but a generalized common workflow for all scRNASeq applicable to most(probably all) datasets

Also, what is demonstrated is a single sample, most of you will likely have multiple replicates or patients or tissue sites from a singular patient or mice (samples) and this should for all of them individually or automated. While the QC filtering is likely on a sample-by-sample basis, please note that most arguments post QC filter in theory should be the same as to allow for the samples to be comparable (subjected to expert input aka yourself. **think for yourself**)

---

## `Cellranger-based Single Cell Feature Counts Generation (Individual Library)`

### Cellranger count Input Nomenclature

All the FASTQ files to be processed should be placed in a single directory, and CellRanger will run all the samples separately. However, you must provide CellRanger FASTQ files which matches a specific convention for identification:

```bash
<SampleName>_S<SampleNumber>_L00<Lane>_<Read>_001.fastq.gz
```

- **\<SampleName\>**: Sample Identifier letting CellRanger know which FASTQ files to combine as a single sample.
- **\<SampleNumber\>**: Not important really, just set all samples as S1 for ease or set each condition as different samples. 
- **\<Lane\>**: Lane number for if sequencing a single sample with different land numbers resulting in multiple FASTQ files.
- **\<Read\>**: **R1** for Read1, **R2** for Read2, **I1** for Index Read1 and **I2** for Index Read2

##### Example:

```bash
SITTC1_S1_L004_R1_001.fastq.gz
```

### Running Cellranger count

```bash
# run cellranger count (maximum CPUs 8; maximum RAM 24GB)
cellranger count \
  --id=SITTC1 \
  --transcriptome=Data/references/GRCh38_index \
  --fastqs=Data/fastq/ \
  --sample=SITTC1 \
  --localcores=8 \
  --localmem=24 \
  --output-dir=results/cellranger_counts/
```
The minimum information required to run `cellranger count` is 
- --id: The SampleID used for naming outputs
- --transcriptome: Directory containing your previously indexed Cellranger references
- --fastqs: Directory containing your FASTQ files

For more information go to ?cellranger count

### `Cellranger count outputs`

cellranger count will generate a single results folder for each sample names according to the --id option.
The outputs will be inside the sub-directory called `outs` with the contents including:

```bash
├── analysis
├── cloupe.cloupe
├── filtered_feature_bc_matrix
├── filtered_feature_bc_matrix.h5
├── metrics_summary.csv
├── molecule_info.h5
├── possorted_genome_bam.bam
├── possorted_genome_bam.bam.bai
├── raw_feature_bc_matrix
├── raw_feature_bc_matrix.h5
└── web_summary.html
```

The ```filtered_feature_bc_matrix``` directory will contain the filtered gene expression count matrix, used as the input for downstream Seurat 10x analysis.

```bash
filtered_feature_bc_matrix/ 
├── barcodes.tsv.gz # List of filtered cell barcodes 
├── features.tsv.gz # List of genes or features (e.g. gene ID, gene name, type) 
└── matrix.mtx.gz # Sparse matrix of gene expression counts (features × cells)
```
---

## `Seurat Standard Pre-processing steps`

Here, we will learn how to `create a seurat object`, `run quality control and filtering cells`, and `normalizing the scRNASeq data`.

```R
library(Seurat)
library(tidyverse)
```

Using the `Read10x` command, we will read into R the 10X CellRanger filtered gene expression count matrix files by providing the directory `filtered_feature_bc_matrix/` as follows:

```R
# Load SITTC1 sample dataset
seurat <- Read10X(data.dir = "results/cellranger_counts/SITTC1/outs/filtered_feature_bc_matrix")
# Create Seurat Object with the raw, un-normalized data
seurat <- CreateSeuratObject(counts = seurat)
```

### `Seurat Object Explanation`
A **Seurat Object** is a special data container used in R by Seurat to store and analyze single-cell RNA-seq data. Importantly, the Seurat is smart and efficient as it stores all your raw and processed data, analysis results and metadata all in one organized structured location. 

As you will see further down, all your analysis within Seurat will be placed back into the Object without overwriting previous raw/processeed data, analyses, metadata or any results.

### `Quality Control`

While there are many metrics out there for QC (up to your own research), here I will only describe cell quality control most commonly used and will be sufficient for most analyses.

Three specific QC metric principles used for filtering is as follows:

- **Library size:** Which is defined as the total sum of UMI counts across all genes. Cells with small library size are considered low quality as RNA has not efficiently been captured.
- **Number of expressed genes in each cell:** Which is defined as the number of genes which have a non-zero for the cell respectively. Cells with very few expressed genes are considered low quality as the trasncript population captured is unlikely to be diverse enough to mimic normal biology.
- **Proportion of UMIs (transcripts) mapped to mitochondrial genes:** High proportions of reads(UMIs/transcripts) suggest extensive mitochondrial contamination and are indicative of poor-quality cells

Important things to note when you are doing QC filtering for your samples:

- The QC metrics are performed under the assumption that they are not correlated with biology and hence are "noise". This may not be true as some cell types for specific datasets may have low RNA transcript levels in general or perhaps you are looking at cell types with high mitochondrial transcripts and hence are "good-quality cells". **please think for yourself in regards to your own sample and conditions**

- Depending on your data, the sample you are using, you can use a different cut-off threshold for both **precent.mt** and **nFeature_RNA**. While I believe that this can be automated to perhaps remove the top 95th percentile and bottom 5th percentile for every sample (given that subsequent negative binomial model fitting will regress out the effects), it is still useful if you want to manually go through each sample and manually curate the QC threshold at a sample-per-sample basis (of course given that you do not have hundreds or thousands...)

- There is a bit of subjectivity as well as bias/human errors which are introduced as a result of arbitrarily setting the QC thresholds. **Again, please think for yourself in regards to your own sample and conditions**

#### `Example Demonstration:`

#### Number of genes detected filtering

```R
FeatureScatter(seurat, feature1 = "nCount_RNA", feature2 = "nFeature_RNA", pt.size=0.1, cols = scales::alpha("black",0.5)) + 
    theme(legend.position="none") +
    labs(title="Library size (nCount_RNA) vs Number of genes detected (nFeature_RNA)")
```
![lib_size_vs_nfeature_SITTC1_scatterplot](../../plots/seurat/SITTC1/lib_size_vs_nfeature_SITTC1_scatterplot.png)

In the example sample SITTC1, the plot above is a scatterplot across all the single cells sequenced with the **library size (nCount_RNA) plotted against number of genes detected (non-zero expression; nFeature_RNA)**. 

- **Upper nFeature_RNA thresholding**: Cells with unusually high number of genes detected may be doublets where two or more cells are captured together leading to artifically high feature counts
- **Lower nFeature_RNA thresholding**: Cells with very low number of detected features(genes) represent empty droplets rather than a true cell.

Here, we filter away cells with unique features (genes) over 5382 and less than 2000 as the top 95th percentile and bottom 5th percentile respectively. There is no "right" threshold, you could lower or increase the thresholds, so long as you can back the selected thresholds with reasoning and logic.

```R
FeatureScatter(seurat,feature1 = "nCount_RNA", feature2 = "nFeature_RNA", pt.size=0.1, cols = scales::alpha("black",0.5)) + 
    theme(legend.position="none") +
    labs(title="SITTC1 Library size (nCount_RNA) vs Number of genes detected (nFeature_RNA)") +
    geom_hline(yintercept = quantile(seurat$nFeature_RNA, 0.05), linetype = "dashed", color = "red") +
    geom_hline(yintercept = quantile(seurat$nFeature_RNA, 0.95), linetype = "dashed", color = "red")
```

![lib_size_vs_nfeature_SITTC1_scatterplot_thresholded](../../plots/seurat/SITTC1/lib_size_vs_nfeature_SITTC1_scatterplot_with_thresholds.png)

A biologist may be more inclinced to be less stringent with these QCs thresholds as to choose a more biologically tailored threshold whereas bioinformaticians often like "cool" sounding numbers like 95th percentile and 5th percentile.

#### Percentage of Mitochondrial Transcripts Present

```R
FeatureScatter(seurat, feature1 = "nCount_RNA", feature2 = "percent.mt", pt.size=0.1, cols = scales::alpha("black",0.5)) + 
    theme(legend.position="none") +
    labs(title="Library size (nCount_RNA) vs percentage of Mitochondrial transcripts (percent.mt)")
```
![lib_size_vs_percentmt_SITTC1_scatterplot_thresholded](../../plots/seurat/SITTC1/lib_size_vs_percent_mt_SITTC1_scatterplot.png)

In the example sample SITTC1, the plot above is a scatterplot across all the single cells sequenced with the **library size (nCount_RNA) plotted against the percentage of transcripts belonging to mitochondrial genes (percent.mt)**.

```R
FeatureScatter(seurat, feature1 = "nCount_RNA", feature2 = "percent.mt", pt.size=0.1, cols = scales::alpha("black",0.5)) + 
    theme(legend.position="none") +
    labs(title="SITTC1 Library size (nCount_RNA) vs percentage of Mitochondrial transcripts (percent.mt)") +
    geom_hline(yintercept = quantile(seurat$percent.mt, 0.95), linetype = "dashed", color = "red")
```

![lib_size_vs_percentmt_SITTC1_scatterplot_thresholded](../../plots/seurat/SITTC1/lib_size_vs_percent_mt_SITTC1_scatterplot_with_thresholds.png)

#### Apply Filtering

Again, we want to remove cells that have high mitochondrial transcipt percentage which often encompasses dying cells undergoing apoptosis and their cytoplasmic mRNA often degrades introducing more noise than we expect. Here again, we use the top 95th percentile which is 5.18% respectively.

```R
# Apply mitochondria and nfeature (UMI) thresholds (Mitochondria and nFEATURERNA quality control pre-processing)
seurat <- subset(seurat, subset = nFeature_RNA > 2030 & nFeature_RNA < 5382 & percent.mt < 5)
```

### `Data Normalization`

#### Library size as a confounding variable

In unnormalized single-cell RNA-seq data, raw counts often exhibit a strong correlation between library size (total UMi counts per cell) and the number of genes detected. This means that cells with higher sequencing depth appear to exhibit more genes as a result of technical variation. Downstream analyses will capture this library size effects where the signals and results obtained from the counts will be predominantly reflect a sequencing technical artefact.

![sctransform_libsiz_genedetected](../../plots/seurat/SITTC1/sctransform_libsiz_genedetected.png)

Prior to normalization, we can see that library size is highly correlated with number of genes detected, i.e. library size increases, the number of genes increases (left). Following normalization (right), we can observe that library size and number of genes detected show a weaker correlation as a result of modelling and removing the effect of library size effect. This means that the expressions will reflect biological variability rather than sequencing depth artefacts.

#### Mean-Variance Relationship

![sctransform_meanvariance](../../plots/seurat/SITTC1/sctransform_meanvariance.png)

Raw counts also exhibit a strong correlation between mean and variance (variability), as genes with higher mean expression tend to have higher variance (shows more varability across the single cells), also known as **Heteroskedasticity** (left). This makes it difficult to tell whether a gene is truly variable between the cells when the variance is high between cells or the result of sequencing depth and noise as anything which increases or decreases the mean expression will result in the subsequent respective directional change in variance. Often times this violates many statistical techniques assumptions and when uncorrected, downstream analyses will be correlated with library size again, and hence the effect of library size will predominate your analyses and the results you obtain may be the strongly influenced by library size, a technical artefact rather than biological signals. Following normalization (right), we can observe that the variance no longer scales with mean expression (i.e. as mean expression increases, variance or also known as variability increases)

#### Cell Cycle Heterogeneity

Gene expression across different cell cycles will differ significantly within any given cell, hence we should mitigate the effects by incorporating it in the model. To adjust for cell cycle heterogeneity confounding the expression, we can calculate the cell cycle phase scores based on canonical markers, and try and account for them.
```R
# Feature counts for each cell are divided by the total counts for that cell and multiplied by the scale.factor
seurat <- Seurat::NormalizeData(seurat)
# Extract genes active during S phase
s.genes <- Seurat::cc.genes$s.genes
# Extract genes active during G2/m phase
g2m.genes <- Seurat::cc.genes$g2m.genes
# Calculate cell cycle phase scores
seurat <- Seurat::CellCycleScoring(seurat, s.features = s.genes, g2m.features = g2m.genes, set.ident = TRUE)
```
### SCTransform

In Seurat, there is a built in function known as **sctransform** which addresses the issues I've just mentioned by regressing (accounting for) them, briefly, using a negative binomial model, and providing normalized expression values (known as residuals) which have their variance-stabilized, allowing you to focus on genuine gene expression patterns.

```R
# Normalize raw counts using a Negative Binominal Model and then correct for Cell cycle, precent.mt, nFeature_RNA
seurat <- Seurat::SCTransform(seurat, vars.to.regress = c("S.Score", "G2M.Score", "percent.mt","nFeature_RNA"), 
```

> Note: you will notice we adjusted the model for cell cycle confounders (S.Score and G2M.score) as well as the previously examined variables (percent.mt and nFeature_RNA). 

To learn more about sctransform, head to the sctransform [vignette](https://htmlpreview.github.io/?https://github.com/satijalab/sctransform/blob/supp_html/supplement/variance_stabilizing_transformation.html)

### Batch effects and SCTransform-based integration worfklow

Heterogeneity in single-cell RNA seq data can be driven by multiple sources including technical variation introduced during experimental processing. These variations may be different across different sequencing runs and samples, known as **batch effects** which should be accounted for when doing downstream differential expression comparisons. 

To achieve this, follow as previously described and SCTransform each sample (each seurat object) separately. To integrate the different samples (usually different conditions), we will use the function **SelectIntegrationFeatures** to find a list of shared informative features across the conditions for  **PrepSCTIntegration** prepare the list of seurat objects of interest for residual (normalized expression) integration.

```R
sample_list <- list(control = seurat_SITTC1, drug_treated = seurat_SITTD4)
features <- SelectIntegrationFeatures(object.list = sample_list, nfeatures = 3000) 
sample_list <- PrepSCTIntegration(object.list = sample_list, anchor.features = features)
```
Now to integrate the datasets, we can use **FindIntegrationAnchors** which takes the list of Seurat objects and uses the features from **SelectIntegrationFeatures** as anchors to integrate the datasets using **IntegrateData**

```R
immune.anchors <- FindIntegrationAnchors(object.list = sample_list, normalization.method = "SCT", anchor.features = features)
seurat <- IntegrateData(anchorset = immune.anchors, normalization.method = "SCT")
```

Now you can either view the samples as individual using **subset()** or analyse the samples as an integrated dataset. 

**Note if you are running only a single sample with no integraton please run: seurat <- FindVariableFeatures(seurat, selection.method = "vst, verbose = FALSE)**
**Most variable features(genes) should be identified during the integration process when analysing integrated datasets.**

## `FUN PART (Transriptional analyses)` 

The neat trick of Seurat is that even after integration, you can still analyze each indivudal sample separately. To view samples indiviudally, you can use **subset** function. Here for simplicity, I will be showing just SITTC1 sample analysis but applicable to the integrated data as well.

```R
# Extract SITTC1 Seurat Object
seurat <- subset(seurat.integrated, subset = orig.ident == "control")
# Set default assay to SCT
DefaultAssay(subset_obj) <- "SCT"
```
### Principal component analysis (PCA)

Briefly, PCA is a dimensionality reduction technique that transforms high-dimensional data (like gene expression) into a smaller set of variables called **principal components** which linearly capture the most variation in the data. Imagine 17000 protein coding gene, to compare how each gene correlates against all other genes, you would have 289 million pairwise comparisons which is unintepretable in visualization as well as almost impossible. For example, a scatterplot may be able to incorporate three dimensions, each dimension corresponding to a singular feature/gene. Any more than four, you would not be able to visualize it as a human. Hence, PCA is used to simplify the complex gene expression profiles AND reduce noise across the 17000 genes by seeing which genes whose expression group together (i.e. they are correlated). Each principal component represents a direction of maximum variance calculated as a linear combination of gene expression values that are correlated (co-expressed.)

```R
seurat <- RunPCA(seurat, verbose = FALSE)
```

There are several useful ways to visualize how features contribute to each principal component and how cells are distributed along them. 

#### Visualizing the amount of variance explained by each PCs

##### Screeplot and Cummulative Variance of each PCs

Ask Jao for **function_helpers.R** script which contains some helper scripts including the function **visualize_principal_components** using a screeplot.

```R
# Visualize PCs using Screeplot
visualize_principal_components(seurat, pca_method = "Seurat", y_variable_to_plot = "cumvar", num_PCs = 50)
```

![pca_screeplot](../../plots/seurat/SITTC1/pca_screeplot.png)

The plot shows the cummulative variance explaineed starting from PC1 to PC50, which is sorted from highest variance PC respectively. Each point on the x-axis represents a principal component and the y-axis shows you the cummulative experience.

##### Loadings of features (their influence) in each PCs 

```R
# Print the top five features positively and negatively correlated with the the top five PCs
print(seurat[["pca"]], dims = 1:5, nfeatures = 5)
# PC_ 1 
# Positive:  FHOD3, LRMDA, EXT1, DOCK4, RIMS2 
# Negative:  MT2A, FTH1, SLPI, RPL8, CXCL1 
# PC_ 2 
# Positive:  MT2A, FTH1, CXCL1, SLPI, TIMP1 
# Negative:  EEF1A1, RPLP1, VIM, RPL10, RPS3 
# PC_ 3 
# Positive:  TNFRSF11B, FTH1, CCL20, SAA1, TPM1 
# Negative:  LAMA3, HTR1F, HMGA2, HSP90B1, CDH6 
# PC_ 4 
# Positive:  CCL20, IL1B, ALDH1A3, TFPI2, TNFRSF11B 
# Negative:  MT2A, FST, VIM, NRG1, ANKRD1 
# PC_ 5 
# Positive:  MT2A, VIM, TIMP1, G0S2, TMSB10 
# Negative:  CXCL6, FTH1, FST, EEF1A1, S100A9 
```
These are genes which have the strongest **loadings**, meaning that their expression patterns most influence the variation captured by the PC.

```R
VizDimLoadings(seurat, dims = 1:2, reduction = "pca")
```
![VizDimLoadings](../../plots/seurat/SITTC1/VizDimLoadings.png)

Another way is by plotting it. 

#### PCA plot

```R
Idents(seurat) <- "all"
DimPlot(seurat, reduction = "pca", group.by = NULL)
```

![DimPlot_PCA](../../plots/seurat/SITTC1/DimPlot_PCA.png)

Although PCA is useful for reducing dimensionality and visualizing large dataset, it is a linear method that maximizes variance which do not necessarily be separate biological subpopulations and clusters (e.g. two different population can share similar sets of specific say signalling pathways which is captured by a specific PC). Hence, as we continue we will use methods like **UMAP** or **t-SNE** designed for visualizing distinct groupings of cells to reveal potential underlying clusters of cells.

### Clustering

You can understand more about the clustering algorithm by visiting Seurat's website, but here briefly, Seurat applies a graph-based clustering approach. 

This can be achieved by running the functions **FindNeighbours** and **FindClusters**

```R
seurat <- FindNeighbors(seurat, dims = 1:30, verbose = FALSE)
seurat <- FindClusters(seurat, resolution = 0.5, verbose = FALSE)
```

There are two options here that are important to discuss.

- **dims** in the function **FindNeighbors**: When overcoming the "curse of high dimensionality" we often use PCA to reduce the dimensions down while retaining most of if not all the same information into lower numbers of dimensions. However, how does one select how many components? Using the screeplot, one could in theoryselect the principal components by the elbow point where the increase in variance drastically drops, meaning that any more PCs do not contribute significantly anymore and may be introducing noise. Identifying the number of **dims** (which is the number of PCs you would like to use) is often challenging and there is no right or wrong answer as long as you can justify why. A ballpark could be the number of PCs required to explain 70-80% of the cummulative variance. But how did we get 70% or 80%? Arbitrary. The only issue would be selecting too low number of PCs (e.g. 4 PCs) as increasing PCs often do not dramatically change the results as you will observe. Try all of them. Why not.

> Sometimes bioinformaticians just select top 50 and call it a day.

- **resolution** in the function **FindClusters**: Briefly, higher values generate finer resolution resulting in more but smaller groups (clusters) whereas lower values generate fewer but larger groups (coarser resolution). The Seurat authors find the best results for datasets with around 3K cells to be around 0.4-1.2. Again play with it and try various different values!

### Non-linear dimensionality reduction 

Seurat offers several non-linear dimensionality reduction techniques (tSNE and UMAP) to visualize and explore the datasets. The goal is to learn the underlying structure in the dataset to place similar cells together in a lower dimensional space (2D), hence cells which are clustered by the PCs above should co-localize on the dimension reduction plots.

Importantly, 2D visualization techniques (tSNE and UMAP) while are valuable in exploring the datasets, they all have limitations and will not be able to fully represent the complexity of the undertlying data. The authors and I would suggest **avoiding drawing biological conclusions SOLELY on the basis of visualization techniques**.

As input to UMAP or tSNE, use the same number of PCs (dims) as the clustering above.

```R
# Run dimensionality reduction
seurat <- RunUMAP(seurat, dims = 1:30, n.neighbors = 15, min.dist = 0.01, seed.use = 241202, verbose = FALSE) 
# Visualize the clustering on the UMAP embeddings
DimPlot(seurat, reduction = "umap")
```

![DimPlotUmapClustering](plots/seurat/SITTC1/DimPlotUmapClustering.png)