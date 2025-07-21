
# ATAC Cell-Ranger Guideline

---

*XZ 231010*

## Before Start

Fill in this **[NGS](https://docs.google.com/spreadsheets/d/1O5EHXKMcctdhHC0H_TK7xsp6DezOLKlIYYGQL5jGUZY/edit?gid=568034138#gid=568034138)** sheet ~Nextseq/UCSD Tab to keep track of the barcodes, samples and reads.

## Step 1: Illumina Run and Data Transfer

Same as the Miseq or iSeq runs (see NGS indel analysis), transfer the data using either the BaseSpace account or a hard drive. Note please save this in appropriate folders located under:

```
/gpfs/group/jin/sequencing
```

**Xinhe's basespace account:**
- Email: xzheng@scripps.edu
- Password: Jinlab_xz2022

## Step 2: Prepare for Cellranger

Different from transcriptome, we only need a sample sheet from `Dropbox/Jin Lab Internal/10x README/10xsamplesheet/templates` (sample sheet is the same as transcriptome)

**Please update:**
- **Lane**: leave as * if all lanes are used
- **Sample**: name of this library, such as 231003_JL_P4_Esr1_V1. You will need to use this name in future analysis, so choose wisely
- **Index**: the barcode you used. Follow the NGS sheet notes

Upload this sheet in your HPC folder (using rsync or anything works for you). Save in your own '10x_sheet' subfolder to keep it organized.

## Step 3: mkfastq

This step is to convert BCL into fastq files, please note ATAC uses a different module:

```bash
module load cellranger-atac
```

### Fill in your own ID, SAMPLESHEETPATH, BCLPATH and OUTPUTPATH

```bash
ID="231010align_ATAC"
SAMPLESHEETPATH="/gpfs/group/jin/Xinhe/10Xanalysis/231003_ATAC/231003samplesheet.csv"
BCLPATH="/gpfs/group/jin/sequencing/nextseq/231009_NB501004_0480_AHFWTGAFX5/"
mkdir 231010mkfastq
OUTPUTPATH="/gpfs/group/jin/Xinhe/10Xanalysis/231003_ATAC/231010mkfastq/"

cellranger-atac mkfastq --id=$ID \
--run=$BCLPATH \
--samplesheet=$SAMPLESHEETPATH \
--output-dir=$OUTPUTPATH
```

## Step 4: Counter

This step is from the fastq files to the cell matrices. You will need to have 1 script for each sample. SAMPLENAME needs to be the same as sample sheet.

```bash
module load cellranger
```

### Fill in your own ID, FASTQPATH and SAMPLENAME

```bash
ID="231010count_ATAC_E1"
FASTQPATH="/gpfs/group/jin/Xinhe/10Xanalysis/231003_ATAC/231010mkfastq"
REFERENCEPATH="/gpfs/group/jin/ref_genomes/mm10_atac/refdata-cellranger-arc-mm10-2020-A-2.0.0"
SAMPLENAME="231003_JL_P4_Esr1_E1"

cellranger-atac count --id=$ID \
--reference=$REFERENCEPATH \
--fastqs=$FASTQPATH \
--sample=$SAMPLENAME \
--force-cells=9000
```

## In Server

```bash
srun --exclusive --pty bash
module load R/4.3.0
R
```

### If cannot load cellranger-atac:

```bash
export PATH=/gpfs/group/jin/software/cellranger-atac-2.1.0:$PATH
```