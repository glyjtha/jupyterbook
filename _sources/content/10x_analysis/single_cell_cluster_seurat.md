# Single Cell Cluster Seurat Guide

## Before This

Please review the following documents:
- **Single cell cellranger** - take Nextseq run into aligned gene x cell matrix
- **Set up R on HPC** - learn how to set up running R on HPC

**Note:** Check `.libPaths()` and you want to make sure the custom path is included by:

```r
.libPaths(c(.libPaths(), "~/R/x86_64-pc-linux-gnu-library/4.0"))
```

## Getting Started

To start, please request an interactive session, example:

```bash
srun --cpus-per-task 8 --mem-per-cpu 1500 --time 3:00:00 --pty bash
```

1. Set up your own analysis folder (name the date, type of data)
2. Copy an example R script from previous analysis to your new analysis folder:
   ```
   /gpfs/group/jin/xin/10xanalysis/230416newAAV/230329_normal.R
   ```
3. You need to update the data input (line 20-22). Run this basic pipeline and modify if needed.

Seurat is a powerful toolkit and please learn the latest versions and vignettes from: https://satijalab.org/seurat/

## Typical Output Figures to Generate

### 1. Merge Multiple Datasets
If you have multiple datasets, merge them appropriately.

### 2. Generate Cell Type Clustering
Create tSNE or UMAP plots for visualization.

### 3. Check Cell Types Using Known Markers
Generate heatmap or violin plots:
- **For developing brain cell types:** Reference marker list can be found in *Di Bella Nature 2021*
- **For adult neurons:** Reference list is Allen atlas papers
- Often you need to do `FindAllMarkers` to generate marker list of your own datasets and check. **Rule of thumb:** need > 3-4 markers to ensure cell type identity!

### 4. Generate QC Plots
Include nGene, nUMI, mito% for each cluster.

**Cautionary story:** https://www.biorxiv.org/content/10.1101/2023.04.05.535689v1

### 5. Check Cell Type Distribution Changes
Compare WT vs Mut conditions using bargraphs.

### 6. Color Coordination
We like low saturation colors for UMAP cell types! You can find a list of these colors in the R script above. Feel free to use:
- Color brewer
- Van Gogh color palette: https://cran.r-project.org/web/packages/vangogh/vignettes/vangogh.html

### 7. Perturbation/Barcode Identity
If you have any perturbation/barcode identity: plot the pert/barcode identity percentage across all cell types.

### 8. Additional Analyses
Depending on what you are looking for:
- Differential gene expression analysis
- Gene module analysis
- Perturbation-based analysis
- Other relevant analyses will be helpful