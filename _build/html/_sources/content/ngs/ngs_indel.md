# NGS Indel Analysis Guideline
*Boli Wu - September 23, 2022*

## Before Start

- Discuss with Grace to coordinate the Miseq run date and who to pool the sample with
- Fill in the [NGS](https://docs.google.com/spreadsheets/d/1O5EHXKMcctdhHC0H_TK7xsp6DezOLKlIYYGQL5jGUZY/edit?gid=548105201#gid=548105201) sheet to keep track of your data and barcodes

## Step 1: Illumina Run and Data Transfer

### BaseSpace Account Setup

For Miseq, iSeq and Nextseq runs: please set up your own BaseSpace account prior to starting the Illumina run. Use your BaseSpace to log in to the machine, and ensure the data is synchronized with your BaseSpace.

**Lab BaseSpace Accounts:**
- Lab account: jinoffice@scripps.edu, Password: Lajolla2021!
- Xinhe's account: xzheng@scripps.edu, Password: Jinlab_xz2022

**Note:** In the unlikely event that you are running Novaseq (typically for RNAseq and complex libraries), BaseSpace will not work for the transfer due to the large size. CCBB will dispatch the data to you a few days after the run – contact Padma if you are waiting on this manual data dispatch.

### Monitoring Run Statistics

You should be able to see the run statistics in real-time from your BaseSpace account. After the run completes, please pay attention to the cluster PF% and the reads number, which are two key parameters. Log them in the [NGS](https://docs.google.com/spreadsheets/d/1O5EHXKMcctdhHC0H_TK7xsp6DezOLKlIYYGQL5jGUZY/edit?gid=548105201#gid=548105201) sheet.

### Data Transfer to Server

To copy the data to the server, please log in to your HPC account: visit the {HPC Introduction}`../HPC/introduction.md`to set up the account and cc Xin in that email so that you are associated with the lab.

1. **Request an interactive session first:**
   ```bash
   srun --cpus-per-task 8 --mem-per-cpu 1500 --time 3:00:00 --partition highmem --pty bash
   ```

2. **Install BaseSpace CLI:**
   ```bash
   /usr/local/bin/bs
   ```
   If this line does not work, install bs to your own directory:
   ```bash
   wget "https://launch.basespace.illumina.com/CLI/latest/amd64-linux/bs" -O $HOME/bs
   chmod u+x $HOME/bs
   ```
   And you can use: `~/bs`

3. **Authenticate (first time only):**
   ```bash
   ~/bs auth
   ```
   (you may want to do `bs auth --force` if this is not the first time you have authenticated with basespace)
   Follow the link and log in to your account on a web browser

4. **Get your Run ID:**
   ```bash
   bs list runs
   ```

5. **Download data:**
   ```bash
   cd /gpfs/group/jin/sequencing/iseq/
   mkdir 221012_M01440_0913_000000000-DHDRP  # generate a folder for your run name
   ~/bs download run -i 245759593 -o ./221012_M01440_0913_000000000-DHDRP
   ```
   Replace with your specific Run ID and destination folder:
   ```bash
   ~/bs download run -i {this is your Run ID} -o {this is your final destination folder on HPC}
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

## Step 2: Run the Demultiplexing

Bcl2fastq and bcl convert use different sample sheets.

### Option 1: bcl2fastq (for old version samplesheet)

1. Find a vanilla barcode samplesheet in the dry lab folder if you don't have it already
2. Remember to save it as 'SampleSheet.csv' and save it under the run folder
3. Update and copy the demulti.sh from the miseq/220919 folder to your run folder
4. Run the job by: `sbatch demulti.sh`
5. Check the status by: `squeue -u your_username`

**First time setup:** If this is your first time you will need to edit your bashrc:
```bash
nano ~/.bashrc
```
Add these lines:
- `ulimit -n 4096`
- `umask 007`

Re-log into your server account. The ulimit helps to run the bcl2fastq, and umask makes sure the folder you created can be accessed by other lab members.

### Option 2: bcl convert (for new samplesheet or planned a run on Basespace)

Reference: [BCL Convert Software Guide](https://support.illumina.com/content/dam/illumina-support/documents/documentation/software_documentation/bcl_convert/bcl-convert-v3-7-5-software-guide-1000000163594-00.pdf)

If you planned your run on BaseSpace, SampleSheet.csv should be ready in the run folder. You can add sample index if missing. Update and copy a script from previous folder with bcl convert command: `/gpfs/group/jin/sequencing/nextseq/241225_VH00464_464_AAG7KLTM5/bclconvert.sh`

## Step 3: Preparation

### Download Sequence Data

Download your sequence data from the server using the SFTP portal. Visit the [File Storage System](https://docs.google.com/document/d/1mv4WWubSQXafMsq15fHH2YFdrRjDTDcFgpegaH9zvno/edit?tab=t.0) page, you can use the Cyberduck app on the Lab Workstation computer to log in.

**Connection Details:**
- Server: login00.scripps.edu
- Username: {your username}

**Alternative Direct Download to Personal Computer:**
```bash
# Upload
rsync -a YOURLOCALFILE xinjin@login00.scripps.edu:/gpfs/group/jin/…DESTINATION

# Download  
rsync xinjin@login00.scripps.edu:/FILE_TO_DOWNLOAD LOCALADDRESS
```

### Prepare Sample Information

1. Make sure all .fastq files are unzipped and under the same folder (referred to as 'working folder')
2. Prepare a .xlsx spreadsheet listing the information of all your samples
3. Each row describes one sample using 4 columns of data:
   - Number of the forward NGS2 primer
   - Number of the reverse NGS2 primer
   - Amplicon sequence (excluding the common handles for NGS)
   - gRNA (targeting sites for the nuclease)
   See example below:
   [image](../image/image6.png)
4. Column or row title should not be included
5. Rename the spreadsheet as "processlist.xlsx" and put it under your working folder

## Step 4: Indel Analysis

### MATLAB Analysis

1. Open MATLAB
2. Set the working directory to your working folder
3. Directly run the main.m function (the directory of these codes should be included in the MATLAB already)
4. The program will give outputs of the analysis status for each sample (each row in the "processlist.xlsx")
5. Pay attention to the errors

### Output Analysis

After the program is finished (usually in a few minutes), you will have a "_indelcounts.xlsx" file in your working folder with the following columns:

- **nInframe:** the number of reads that have in-frame indels
- **nFrameshift:** the number of reads that have frameshift indels
- **nReadsMapped:** the number of reads that have the given barcode and successfully mapped to your amplicon
- **indelpct:** the percentage of reads with indels that mapped to your amplicon
- **nReadsScanned:** the number of reads that have the given barcode
[image6](../image/image7.png)

Since the indelpct contains both in-frame and frameshift indels, you need to add another column to calculate the percentage of frameshift indels only (nFrameshift/nReadsMapped). This percentage corresponds to your gRNA targeting efficiency.

**MATLAB codes location:** D:/Miseq analysis (for reference)

### Alternative: Outknocker Analysis

Another way to quickly check a few .fastq files at a time is to use [Outknocker](http://www.outknocker.org/outknocker2.htm):

1. **Step 1:** Specify the species, experiment type (select "monoclonal" for indel analysis), nuclease type, gene name, and gRNA sequence as needed. Click "ANALYZE DATA (proceed to step 2)"

2. **Step 2:** Click "Select FASTQ files" to upload the .fastq files corresponding to the gRNA seq (can do multiple at a time). Click "START ANALYSIS (proceed to step 3)"

3. **Step 3:** The website will generate a summary of the indels (percentages and locations) found in your .fastq files

## Step 5: Data Summary and Presentation

Now you have the data, please organize them in a presentable way. Heatmap and dotplot/average bar graphs are the usual options. Don't stop at the raw data! Finalize it to publishable quality.