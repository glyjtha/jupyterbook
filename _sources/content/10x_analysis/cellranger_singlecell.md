# Single Cell Cell-Ranger Guideline


## Before Start

- Read about the cellranger software [here](https://support.10xgenomics.com/single-cell-gene-expression/software/pipelines/latest/what-is-cell-ranger)
- Fill in this **[NGS](https://docs.google.com/spreadsheets/d/1O5EHXKMcctdhHC0H_TK7xsp6DezOLKlIYYGQL5jGUZY/edit?gid=568034138#gid=568034138)** sheet ~10x Tab to keep track of the barcodes and reads

## Step 1: Illumina Run and Data Transfer

Same as the Miseq or iSeq runs (see NGS indel analysis), transfer the data using either the BaseSpace account or a hard drive. Note please save this in appropriate folders located under:

```
/gpfs/group/jin/sequencing
```

**Xinhe's basespace account:**
- Email: xzheng@scripps.edu
- Password: Jinlab_xz2022

## Step 2: Prepare for Cellranger

Download all 3 .csv files from `Dropbox/Jin Lab Internal/10x README/10xsamplesheet/templates`

### 1. Samplesheet.csv
You will need a samplesheet.csv for the sample index.

**Please update:**
- **Lane**: leave as * if all lanes are used
- **Sample**: name of this library, such as 221209XZ_PHPeB. You will need to use this name in future analysis, so choose wisely
- **Index**: the barcode you used. Follow the NGS sheet notes

Check [this page](https://support.10xgenomics.com/single-cell-gene-expression/software/pipelines/latest/using/mkfastq) for any questions.

### 2. Libraries.csv
You will need a libraries.csv to provide all the info about your libraries:

**Please update:**
- **Fastqs**: where the mkfastq output path is. Note you need a full path here
- **Sample**: name of this library, such as 221209XZ_PHPeB. You will need to use this name in future analysis, so choose wisely
- **Library type**: the type of the libraries

Check [this page](https://support.10xgenomics.com/single-cell-gene-expression/software/pipelines/latest/using/count) for questions.

### 3. Featureref.csv (if needed)
If you have feature barcode detection: you will need a featureref.csv file to update all **gRNA** you have in the library. Check the barcode pattern (column D) matches your gRNA backbone!

**Please update** the columns A B E G and H.

Check [this page](https://support.10xgenomics.com/single-cell-gene-expression/software/pipelines/latest/using/feature-bc-analysis) for questions.

### Upload Files to HPC
Upload these in your HPC folder using rsync. Save in your own '10x_sheet' subfolder to keep it organized.

```bash
rsync -avH /location_of_files -e ssh xinjin@login00.scripps.edu:/gpfs/group/jin/xin/10xanalysis/xx
```

**Xin's note:**
```bash
rsync -avH 221209cellranger.sh -e ssh xinjin@login00.scripps.edu:/gpfs/group/jin/xin/10xanalysis/221209
rsync -avH 221209libraries.csv -e ssh xinjin@login00.scripps.edu:/gpfs/group/jin/xin/10xanalysis/221209
rsync -avH 221209samplesheet.csv -e ssh xinjin@login00.scripps.edu:/gpfs/group/jin/xin/10xanalysis/221209
```

## Step 3: Start Cellranger

This is the step when you take the sequencer raw data (BCL) to convert to fastq files for each library.

Edit the below script and update the highlighted parts. You can find this template in Dropbox folder too. Run using sbatch.

**Notes:**
- Feel free to separate them into 2 scripts (mkfastq first, then count). It is easier to keep track.
- **IMPORTANT** - if you have multiple channels/samples, run one script for each sample! (Or run mkfastq together and count separately with different sample sheets). This is not batch processing script yet (someone please set it up and share with the lab).

### Script Template

```bash
#!/bin/bash
#SBATCH --mem=64gb
#SBATCH --nodes=1
#SBATCH --cpus-per-task=8
#SBATCH --time=24:00:00
#SBATCH --partition=highmem

## Running cellranger analysis of 10X data
## NB: use different script for cDNA only libraries
## Configured to work with Slurm

module load cellranger

ID="align221211"
SAMPLESHEETPATH="/gpfs/group/jin/xin/10xanalysis/221209/221209samplesheet.csv"
BCLPATH="/gpfs/group/jin/Sequencing/nextseq/221209_NB501004_0429_AH72THBGX5/Data/Intensities/BaseCalls"

mkdir 221209mkfastq
OUTPUTPATH="/gpfs/group/jin/xin/10xanalysis/221209/221209mkfastq/outs/"

cellranger mkfastq --id=$ID \
                     --run=$BCLPATH \
                     --samplesheet=$SAMPLESHEETPATH \
                     --output-dir=$OUTPUTPATH

LIBRARIESPATH="/gpfs/group/jin/xin/10xanalysis/221209/221209libraries.csv"
FEATUREREFPATH="/gpfs/group/jin/xin/10xanalysis/221209/221209featureref.csv"
TRANSCRIPTOMEDIR="/gpfs/group/jin/ref_genomes/mm10/refdata-gex-mm10-2020-A"

cellranger count --id=$ID \
                        --transcriptome=$TRANSCRIPTOMEDIR \
                        --libraries=$LIBRARIESPATH \
                        --feature-ref=$FEATUREREFPATH \
                        --expect-cells=9000
```