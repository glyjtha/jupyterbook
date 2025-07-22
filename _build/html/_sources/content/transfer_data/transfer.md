# Scripps Sequencing Data Processing Protocol

## Initial Data Notification

After your sequencing run at Scripps Genomic Core, you will receive an email from Steven Head:

**Example notification:**
```
This is related to your sequencing:
project - ca_ns_jin_20231219_0_Libseq_custom_GC
run - ca_ns_0366_20231219_jin_28x69_P2

Internal Data Dispatch:
Instructions to access the data dispatched:
[here](https://github.com/ScrippsCCBB/CCBBwebsite/blob/main/FileZilla_Instructions_CCBB.pdf)

Please download your sequencing data (BCL) from the following link:
[link1](ftp://jinlab:jinlab@shulik.scripps.edu/bcldata/231219_VH00464_366_AAFG2L7M5.bcls.tar)
[link2](ftp://jinlab:jinlab@shulik.scripps.edu/bcldata/231219_VH00464_366_AAFG2L7M5.bcls.tar.md5)
```

**Note:** No need to follow the CCBB instruction, please follow the below instructions in your terminal (change username and folder directory accordingly).

## Alternative Data Location

Sometimes Genomic Core/CCBB already transfer the data into our folder on server, and they will send us the directory. In this case, go to Step 3 (skip 1 and 2).

**Example alternative notification:**
```
This is related to your sequencing project:
project – ca_ns_jin_20240716_0_libseq_mouse_XZ
Run number-421

Internal Data Dispatch:
Raw BCL data has been uploaded to the following location on Garibaldi:
/gpfs/group/jin/Sequencing/240716_VH00464_421_AAFG2VYM5.bcls.tar

md5sum:
/gpfs/group/jin/Sequencing/240716_VH00464_421_AAFG2VYM5.bcls.tar.md5
```

## Step 1: Log into Scripps HPC

Log in to the Scripps HPC and navigate to the sequencing folder:

```bash
ssh username@login02.scripps.edu
cd /gpfs/group/jin/sequencing/nextseq/
```

**If you don't have an HPC account yet:** Register at [HPC Account](https://scrippsresearch.sharepoint.com/sites/its/SitePages/HPC-AccountRequest.aspx)

## Step 2: Download Data

Download the BCL files and MD5 checksum:

```bash
wget ftp://jinlab:jinlab@shulik.scripps.edu/bcldata/231219_VH00464_366_AAFG2L7M5.bcls.tar
wget ftp://jinlab:jinlab@shulik.scripps.edu/bcldata/231219_VH00464_366_AAFG2L7M5.bcls.tar.md5
```

## Step 3: Decompress BCL Files

Extract your BCL files:

```bash
tar -xvf 231219_VH00464_366_AAFG2L7M5.bcls.tar
```

This will generate a new folder: `231219_VH00464_366_AAFG2L7M5.bcls`. You can delete the two compressed files after extraction.

## Step 4: Check Sequencing Quality Metrics

The sequencing quality metrics can be found in the primary analysis metrics file. Please log Q30 and PF into NGS sheet:

```bash
cat 231219_VH00464_366_AAFG2L7M5.bcls/PrimaryAnalysisMetrics/PrimaryAnalysisMetrics.csv
```

**Example output:**
```
Metric, Unit, Value
Average %Q30, %, 94.41
Total Yield, Gbp, 60.63
Total Reads PF, M, 536.64
% Loading Concentration, %, 97.74
```

## Browsing All Sequencing Results (Optional)

If you would like to browse all sequencing results in genomic core:

1. Connect to FTP:
   ```bash
   ftp shulik.scripps.edu
   ```

2. Login credentials:
   - **Username:** jinlab
   - **Password:** jinlab

3. Browse directories:
   ```
   Connected to shulik.scripps.edu (172.29.48.50).
   220 Welcome to TSRI CCBB FTP service
   Name (shulik.scripps.edu:xzheng): jinlab
   331 Please specify the password.
   Password:
   230 Login successful.
   Remote system type is UNIX.
   Using binary mode to transfer files.
   
   ftp> ls
   227 Entering Passive Mode (172,29,48,50,190,222).
   150 Here comes the directory listing.
   drwxrwxr-x    3 1003     20006           5 Dec 20 13:46 bcldata
   drwxr-xr-x    3 1045     20006           3 Mar 22  2022 processed
   drwxr-xr-x    2 1045     20006           3 Mar 22  2022 qc
   drwxr-xr-x    3 1045     20006           3 Mar 22  2022 seqdata
   226 Directory send OK.
   ```

## Demultiplexing

### Prerequisites

Make sure the run folder is ready, with `demulti.sh` and `SampleSheet.csv`.

**Note:** You will need to copy over a `SampleSheet.csv` if you want to demultiplex using `demulti.sh`.

### Demulti.sh Script

The `demulti.sh` file can be found in almost all sequencing run files:

```bash
#!/bin/bash
#SBATCH --mem=12gb
#SBATCH --nodes=1
#SBATCH --cpus-per-task=8
#SBATCH --time=1:00:00
#SBATCH --output=myoutput.out
#SBATCH --error=myerror.err

## Running cellranger analysis of 10X data WITH hashtag libraries
## NB: use different script for cDNA only libraries
## Configured to work with Slurm

mkdir fastq_files

module load bcl2fastq

bcl2fastq -p 12 --barcode-mismatches 1 --output-dir ./fastq_files --no-lane-splitting
```

### Running Demultiplexing

Submit the demultiplexing job:

```bash
sbatch demulti.sh
```

**Example output:**
```
Submitted batch job 32990279
```

### Checking Job Progress

To check the job progress:

```bash
squeue -u xzheng
```

**Example output:**
```
JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
32990279    shared demulti.   xzheng  R       0:32      1 (nodea1418)
```

### Final Output

When demultiplexing is finished, there will be a `fastq_files` folder in your run folder. Move on with the `fastq.gz` files that start with index names you used. Use `ls -l` to browse detailed information of all files.