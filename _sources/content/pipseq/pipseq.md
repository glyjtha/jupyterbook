# NextSeq Data Processing and PIPseeker Analysis Guide

## Data Download from Illumina BaseSpace

### BaseSpace Account Setup

For Miseq and Nextseq runs: please set up your own BaseSpace account prior to starting the Illumina run. Use your BaseSpace to log in to the machine, and ensure the data is synchronized with your BaseSpace.

**Xinhe's BaseSpace Account:** xzheng@scripps.edu, Password: Jinlab_xz2022

**Note:** In the unlikely event that you are running Novaseq (typically for RNAseq and complex libraries), BaseSpace will not work for the transfer due to the large size. CCBB will dispatch the data to you a few days after the run – contact Padma if you are waiting on this manual data dispatch.

### Monitoring Run Statistics

You should be able to see the run statistics in real-time from your BaseSpace account.

After the run completes, please pay attention to the cluster PF% and the reads number, which are two key parameters. Log them in the [NGS](https://docs.google.com/spreadsheets/d/1O5EHXKMcctdhHC0H_TK7xsp6DezOLKlIYYGQL5jGUZY/edit?gid=548105201#gid=548105201) sheet.

### Data Transfer to Server

To copy the data to the server, please log in to your HPC account: visit the [HPC Introduction](https://docs.google.com/document/d/1DBdf-nif6GrwMcH08Na2RLk9zGEKR5fw7tKL6O0lOQ4/edit?tab=t.0) to set up the account and cc Xin in that email so that you are associated with the lab.

1. **Initialize BaseSpace CLI:**
   ```bash
   /usr/local/bin/bs
   ```

2. **Authenticate:**
   ```bash
   bs auth
   ```
   (you may want to do `bs auth --force` if this is not the first time you have authenticated with basespace)
   Follow the link and log in to your account on a web browser

3. **Get your Run ID:**
   ```bash
   bs list runs
   ```

4. **Navigate to directory and create folders:**
   ```bash
   cd /gpfs/group/jin/sequencing/nextseq/
   mkdir 221012_M01440_0913_000000000-DHDRP  # generate a folder for your run name
   mkdir {copy the run name}
   mkdir 230419_NB501004_0451_AH5YWWBGX5  # (20230420)
   mkdir 230509_NB501004_0456_AH52VLBGX5  # (20230510)
   ```

5. **Download runs:**
   ```bash
   bs download run -i 245759593 -o ./221012_M01440_0913_000000000-DHDRP
   bs download run -i {this is your Run ID} -o {this is your final destination folder on HPC}
   bs download run -i 257308093 -o ./230419_NB501004_0451_AH5YWWBGX5  # (20230420)
   bs download run -i 258377136 -o ./230509_NB501004_0456_AH52VLBGX5  # (20230510)
   bs download run -i 260702478 -o ./20230615_FS10002641_16_BRR99425-2106  # (20230616)
   bs download run -i 262043837 -o ./230710_NB501004_0470_AH3T3YBGX5  # (20230710)
   ```

### Helpful Resources
- [BaseSpace CLI Overview](https://developer.basespace.illumina.com/docs/content/documentation/cli/cli-overview)
- [BaseSpace CLI Examples](https://developer.basespace.illumina.com/docs/content/documentation/cli/cli-examples#Downloadallrundata)

**Data Storage Location:** All our data should be within `/gpfs/group/jin/sequencing/` (or Nextseq or Novaseq or iSeq or Miseq folder, depending on what machine this was run on)

### File Decompression
If files are compressed (.tar, .gz) you can decompress by running:
```bash
tar xvf YOURFILE.tar
gunzip *.gz
```

## Demultiplexing

1. **Prepare SampleSheet:** You can find a vanilla barcode samplesheet in the dry lab folder if you don't have it already. Remember to save it as 'SampleSheet.csv' and save it under the run folder.

2. **Copy demultiplexing script:** Update and copy the demulti.sh from the miseq/220919 folder to your run folder:
   ```bash
   cp demulti.sh /gpfs/group/jin/sequencing/nextseq/230419_NB501004_0451_AH5YWWBGX5/  # (20230420)
   cp demulti.sh /gpfs/group/jin/sequencing/nextseq/230509_NB501004_0456_AH52VLBGX5   # (20230510)
   cp demulti.sh /gpfs/group/jin/sequencing/nextseq/230710_NB501004_0470_AH3T3YBGX5   # (20230710)
   ```

3. **Run the job:**
   ```bash
   sbatch demulti.sh
   ```

4. **Check status:**
   ```bash
   squeue -u your_username  # (squeue -u zhilinwang)
   ```

### First Time Setup
If this is your first time you will need to edit your bashrc:
```bash
nano ~/.bashrc
```
Add these lines:
- `ulimit -n 4096`
- `umask 007`

Re-log into your server account - the ulimit helps to run the bcl2fastq, and umask makes sure the folder you created can be accessed by other lab members.

## PIPseeker Analysis

### Automatic sbatch submission (preferred method)

**NOTE:** PIPseeker is being updated regularly. Use v3.0.5 as of Jan 03 2024

**One sample at a time** (each fastq folder should be one sample)

**Optional:** You can always request an interactive session to run the job:
```bash
srun --time=24:00:00 --cpus-per-task=16 --mem=16000 --pty bash
```

### PIPseeker v4.0 (Updated by Ellie 2024-11-13)

```bash
bash /gpfs/group/jin/software/PIPseeker/pipseeker_v4_noanno.sh /dir/to/fastqs/ /dir/to/outuput/folder/samplename #chemistry #force-cells
```

**Example:**
```bash
bash /gpfs/group/jin/software/PIPseeker/pipseeker_v4_noanno.sh /gpfs/group/jin/sequencing/nextseq/241111_VH00464_450_AAFG7WYM5/241111_VH00464_450_AAFG7WYM5.bcls/fastq_files/PilotV/. ./3000output/ V 3000
```

### PIPseeker v3.0.5 (Updated by Boli 2024-01-03)

**With annotation:**
```bash
bash /gpfs/group/jin/software/PIPseeker/pipseeker_v3.sh /dir/to/fastqs/ /dir/to/outuput/folder/samplename #chemistry #force-cells #annotation-ref
```

**Example:**
```bash
bash /gpfs/group/jin/software/PIPseeker/pipseeker_v2.sh /gpfs/group/jin/sequencing/nextseq/230309_NB501004_0438_AHJ7VGBGX5/fastq_files/liverpipATAC ./1/ v3 1800 /gpfs/group/jin/software/PIPseeker/mouse-liver.csv
```

**Without annotation:**
```bash
bash /gpfs/group/jin/software/PIPseeker/pipseeker_v3_noanno.sh /dir/to/fastqs/ /dir/to/outuput/folder/samplename #chemistry #force-cells
```

**Examples:**
```bash
bash /gpfs/group/jin/software/PIPseeker/pipseeker_v2_noanno.sh /gpfs/group/jin/sequencing/iseq/20241004_FS10002641_32_BRL95622-1619/fastq_files/Pilot/. ./output/ v4 3000

bash /gpfs/group/jin/software/PIPseeker/pipseeker_v3_noanno.sh /gpfs/group/jin/sequencing/iseq/20241004_FS10002641_32_BRL95622-1619/fastq_files/Pilot/. ./output_pipseekerv3/ v4 2000

bash /gpfs/group/jin/software/PIPseeker/pipseeker_v2_noanno_wth_GFP.sh /gpfs/group/jin/sequencing/nextseq/230602_NB501004_0463_AH57HMBGX5/fastq_files/Jill_GFP/. ./ v4 1500

bash /gpfs/group/jin/software/PIPseeker/pipseeker_v2_noanno.sh /gpfs/group/jin/sequencing/nextseq/230710_NB501004_0470_AH3T3YBGX5/230710_NB501004_0470_AH3T3YBGX5/fastq_files/liverkcpip/. ./liverkc v4 2000
```

**Without force cells:**
```bash
bash /gpfs/group/jin/software/PIPseeker/pipseeker_v2_noanno_noforce.sh /dir/to/fastqs/ /dir/to/outuput/folder/samplename #chemistry
```

**Examples:**
```bash
bash /gpfs/group/jin/software/PIPseeker/pipseeker_v2_noanno_noforce.sh /gpfs/group/jin/sequencing/nextseq/230419_NB501004_0451_AH5YWWBGX5/fastq_files/liver1/. ./1/ v4
```

## CROPseq Analysis

To use PIPseeker with CROPseq:
```bash
bash /gpfs/group/jin/software/PIPseeker/pipseeker_v2_adt.sh /dir/to/fastqs/ /dir/to/outuput/folder/samplename #chemistry #force-cells #dir/to/gRNAfastqs #adt tags #adt position
```
**(This needs 7 inputs)**

### CROPseq Parameters:
- `#dir/to/gRNAfastqs` is dial-out PCR of your CROPseq gRNAs and they've been sequenced into a separate set of FASTQ files
- `#adt tags` is a CSV file containing your gRNA barcode sequences, as in the following example:
  ```
  ACTGTAGCTCATACCTCTG,gRNA_Gene1,gRNA1 description
  CCTGTAGTAGCTCATCGGA,gRNA_Gene2,gRNA2 description
  CACCGTCCTGTAGTCTGCC,gRNA_Gene3,gRNA3 description
  ```

### CROPseq Examples:

**2023-05-28:**
```bash
bash /gpfs/group/jin/software/PIPseeker/pipseeker_v2_adt.sh /gpfs/group/jin/sequencing/nextseq/230509_NB501004_0456_AH52VLBGX5/crop_grna/fastq_files/. ./liverpip_crop/ v4 4000 /gpfs/group/jin/sequencing/nextseq/230509_NB501004_0456_AH52VLBGX5/crop_grna/grna_library/. ./ADTtags.csv 22
```

**2023-06-02:**
```bash
bash /gpfs/group/jin/software/PIPseeker/pipseeker_v2_adt.sh /gpfs/group/jin/sequencing/nextseq/230509_NB501004_0456_AH52VLBGX5/crop_grna/fastq_files/. ./liverpip_crop/ v4 4000 /gpfs/group/jin/sequencing/nextseq/230602_NB501004_0463_AH57HMBGX5/fastq_files/crop_grna/. ./ADTtags.csv 22
```

**2023-06-08:**
```bash
bash /gpfs/group/jin/software/PIPseeker/pipseeker_v2_adt.sh /gpfs/group/jin/sequencing/nextseq/230509_NB501004_0456_AH52VLBGX5/crop_grna/fastq_files/. ./liverpip_crop/ v4 4000 /gpfs/group/jin/sequencing/nextseq/230602_NB501004_0463_AH57HMBGX5/fastq_files/crop_grna/. ./ADTtags.csv 0
```

**Additional CROPseq runs:**
```bash
# 2023-06-12
bash /gpfs/group/jin/software/PIPseeker/pipseeker_v2_adt.sh /gpfs/group/jin/sequencing/nextseq/230509_NB501004_0456_AH52VLBGX5/crop_grna/fastq_files/. ./liverpip_crop/ v4 4000 /gpfs/group/jin/zhilinwang/iseq/20230611_FS10002641_15_BSB09412-1532/fastq_files_10x/. ./ADTtags.csv 22

# 2023-06-14
bash /gpfs/group/jin/software/PIPseeker/pipseeker_v2_adt.sh /gpfs/group/jin/sequencing/nextseq/230509_NB501004_0456_AH52VLBGX5/crop_grna/fastq_files/. ./liverpip_crop1/ v4 4000 /gpfs/group/jin/zhilinwang/iseq/20230611_FS10002641_15_BSB09412-1532/fastq_files_10x/. ./ADTtags.csv 22

# 2023-07-10
bash /gpfs/group/jin/software/PIPseeker/pipseeker_v2_adt.sh /gpfs/group/jin/sequencing/nextseq/230710_NB501004_0470_AH3T3YBGX5/fastq_files/liverkcpip/. /gpfs/group/jin/zhilinwang/pipseq/230710kupfferwholecell/liverpip_kc/ v4 2000 /gpfs/group/jin/zhilinwang/iseq/20230707_FS10002641_17_BRR99425-1410/fastq_files_10x/livr_kc_grna/. ./ADTtags.csv 22

# 2023-07-18
bash /gpfs/group/jin/software/PIPseeker/pipseeker_v2_adt.sh /gpfs/group/jin/sequencing/nextseq/230509_NB501004_0456_AH52VLBGX5/crop_grna/fastq_files/. ./liverpip_crop/ v4 4000 /gpfs/group/jin/zhilinwang/nextseq/230713_NB501004_0472_AHFYJ3AFX5/fastq_files_10x/grna0509library/. ./ADTtags.csv 22

# 2023-07-20
bash /gpfs/group/jin/software/PIPseeker/pipseeker_v2_adt.sh /gpfs/group/jin/sequencing/nextseq/230710_NB501004_0470_AH3T3YBGX5/fastq_files/liverkcpip/. ./liverpip_crop/ v4 4000 /gpfs/group/jin/zhilinwang/nextseq/230713_NB501004_0472_AHFYJ3AFX5/fastq_files_10x/grna0629library/. ./ADTtags.csv 22

# 2023-07-21
bash /gpfs/group/jin/software/PIPseeker/pipseeker_v2_adt.sh /gpfs/group/jin/sequencing/nextseq/230710_NB501004_0470_AH3T3YBGX5/fastq_files/liverkcpip/. ./liverpip_crop/ v4 2000 /gpfs/group/jin/zhilinwang/nextseq/230713_NB501004_0472_AHFYJ3AFX5/fastq_files_10x/grna0629library/. ./ADTtags.csv 22

# 2023-08-09 (position 21)
bash /gpfs/group/jin/software/PIPseeker/pipseeker_v2_adt.sh /gpfs/group/jin/sequencing/nextseq/230710_NB501004_0470_AH3T3YBGX5/fastq_files/liverkcpip/. ./liverpip_crop/ v4 2000 /gpfs/group/jin/zhilinwang/nextseq/230713_NB501004_0472_AHFYJ3AFX5/fastq_files_10x/grna0629library/. ./ADTtags.csv 21

bash /gpfs/group/jin/software/PIPseeker/pipseeker_v2_adt.sh /gpfs/group/jin/sequencing/nextseq/230509_NB501004_0456_AH52VLBGX5/crop_grna/fastq_files/. ./liverpip_crop/ v4 4000 /gpfs/group/jin/zhilinwang/nextseq/230713_NB501004_0472_AHFYJ3AFX5/fastq_files_10x/grna0509library/. ./ADTtags.csv 21
```

## Troubleshooting

### Permission Issues
If you run into permission issue, try:
```bash
chmod 777 XXX
```

### Check Job Status
To check run status:
```bash
squeue -u yourusername
```

### Custom Analysis with Interactive Session

For customizing parameters or reanalyzing in interactive session (srun):

**First time setup:**
```bash
export SINGULARITY_BIND="/gpfs/group/jin:/gpfs/group/jin"
```

**Run initial analysis** (see [here](https://fbs-public.s3.us-east-2.amazonaws.com/public-pipseeker-releases/pipseeker-v3.1.3/PIPseeker-v3.1-User-Guide.pdf) for additional parameters):
```bash
/gpfs/group/jin/software/pipseeker count --input-path /dir/to/fastqs --output-root /dir/to/output/folder --star-index-path /gpfs/group/jin/ref_genomes/mm10
```

**Reanalysis:**
```bash
/gpfs/group/jin/software/pipseeker reanalyze --input-path /dir/to/previous/output --output-root /dir/to/output/folder
```