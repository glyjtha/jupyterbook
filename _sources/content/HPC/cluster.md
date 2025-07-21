# Use the HPC Cluster

## Account Setup

Complete the forms at:
- https://intranet.scripps.edu/its/unix-linux/request.html  
  To request an account on the cluster, select HPC account and bash.

- https://intranet.scripps.edu/its/remoteaccess/vpn/request_ca.html  
  for VPN accounts

## User Guide

https://intranet.scripps.edu/its/highperformancecomputing/user_guide.html

## Cluster Commands

### Log in on the cluster:
```bash
ssh your_user_name@login00.scripps.edu
# type in your_password
```

### Copy files from local terminal to the cluster (run on local)
```bash
rsync -a /Users/zhengwu/Dropbox/Private/Research/Organoid/single_cell_analysis/scripts/test01.sh hwu@login00.scripps.edu:/gpfs/home/hwu/
```

### Copy files from the cluster to local (run on local)
```bash
rsync hwu@login00.scripps.edu:/gpfs/home/hwu/scripts/test03.sh /Users/zhengwu/Dropbox/Private/Research/Organoid/single_cell_analysis/scripts/

rsync -r hwu@login00.scripps.edu:/gpfs/home/hwu/test_run/run_count_1kpbmcs/outs /Users/zhengwu/Dropbox/Private/Research/Organoid/single_cell_analysis/alignment/test_run/

rsync -r xinjin@login00.scripps.edu:/gpfs/group/jin/herbert/scripts /Users/xinjin/Desktop

rsync -a  /Users/xinjin/Dropbox\ \(Scripps\ Research\)/Jin\ Lab\ Internal/Xin/scripts/tar.sh xinjin@login00.scripps.edu:/gpfs/group/jin/Sequencing/Novaseq
```

**Reference:**  
https://linuxize.com/post/how-to-transfer-files-with-rsync-over-ssh/

## 10x Genomics Data Analysis Pipeline

https://support.10xgenomics.com/single-cell-gene-expression/software/pipelines/latest/using/count

### Library CSV file
https://support.10xgenomics.com/single-cell-gene-expression/software/pipelines/latest/using/using/feature-bc-analysis#feature-ref

## Example Script

Example script to run the publicly available pbmc file:

Follow this tutorial here:  
https://support.10xgenomics.com/single-cell-gene-expression/software/pipelines/latest/using/tutorial_ct

Save the following script as a .sh file:

```bash
#!/bin/bash
#SBATCH --mem=64gb
#SBATCH --nodes=1
#SBATCH --cpus-per-task=8
#SBATCH --time=24:00:00

## Running cellranger analysis of 10X data WITH hashtag libraries
## NB: use different script for cDNA only libraries
## Configured to work with Slurm

ID="run_count_1kpbmcs"
SAMPLE1="pbmc_1k_v3"
##LIBRARIESPATH="/gpfs/home/jaroslav/data/raw_fastq/libraries_combined_NS870.csv"
##FEATUREREFPATH="/gpfs/home/jaroslav/data/raw_fastq/featureReferenceHashes_combined_NS870.csv"
TRANSCRIPTOMEDIR="/gpfs/home/hwu/ref_genome/refdata-gex-GRCh38-2020-A"
FASTQDIR="/gpfs/home/hwu/test_run/pbmc_1k_v3_fastqs"

/gpfs/home/hwu/software/cellranger-6.1.1/cellranger count --id=$ID \
                        --transcriptome=$TRANSCRIPTOMEDIR \
                                --fastqs=$FASTQDIR \
                        --expect-cells=1200
```