# Jin Lab Cut&Run Analysis Guideline
*XJ 240705*

## Step 1 (Option 1): Illumina Run and Data Transfer

### BaseSpace Setup
For MiSeq, iSeq and NextSeq runs: please set up your own BaseSpace account prior to starting the Illumina run. Use your BaseSpace to log in to the machine, and ensure the data is synchronized with your BaseSpace.

**Xinhe's BaseSpace account**: 
- Email: xzheng@scripps.edu  
- Password: Jinlab_xz2022

### NovaSeq Runs
In the unlikely event that you are running NovaSeq (typically for RNA-seq and complex libraries), BaseSpace will not work for the transfer due to the large size. CCBB will dispatch the data to you a few days after the run – contact Padma if you are waiting on this manual data dispatch.

### Post-Run Monitoring
You should be able to see the run statistics in real-time from your BaseSpace account.

After the run completes, please pay attention to the cluster PF% and the reads number, which are two key parameters. Log them in the NGS sheet.

### Data Transfer to Server
To copy the data to the server, please log in to your HPC account: visit the {HPC Introduction}`../HPC/introduction.md` to set up the account and cc Xin in that email so that you are associated with the lab.

Ideally request an interactive session first (adjust the highlighted content to your need):
```bash
srun --cpus-per-task 8 --mem-per-cpu 1500 --time 3:00:00 --pty bash
```

#### Install BaseSpace CLI
```bash
# Try this first
/usr/local/bin/bs

# If this line does not work, install bs to your own directory:
wget "https://launch.basespace.illumina.com/CLI/latest/amd64-linux/bs" -O $HOME/bs
chmod u+x $HOME/bs

# And you can use:
~/bs
```

#### Authenticate with BaseSpace
If this is your first time, authenticate by:
```bash
~/bs auth
# (you may want to do bs auth --force if this is not the first time you have authenticated with basespace)
```
Follow the link and log in to your account on a web browser

#### Download Data
```bash
# To get your Run ID:
~/bs list runs

# To download:
cd /gpfs/group/jin/sequencing/iseq/
mkdir 221012_M01440_0913_000000000-DHDRP  # generate a folder for your run name
~/bs download run -i 245759593 -o ./221012_M01440_0913_000000000-DHDRP
```

## Step 1 (Option 2): Novogene Data Transfer
If you use Novogene, follow their download guideline.

## Step 2: Demultiplex the FASTQ for Each Sample

### Setup SampleSheet
Use the SampleSheet.csv to run demultiplex (samplesheet.csv see Dropbox/Protocol/Cut&Run folder)

You can find a vanilla barcode samplesheet in this dry lab folder if you don't have it already. Remember to save it as 'SampleSheet.csv' and save it under the run folder.

### Run Demultiplexing
1. Update and copy the `demulti.sh` from the `miseq/220919` folder to your run folder
2. Run the job by:
   ```bash
   sbatch demulti.sh
   ```
3. You can check the status by:
   ```bash
   squeue -u your_username
   ```

### First Time Setup
**Note**: If this is your first time you will need to edit your bashrc:
```bash
nano ~/.bashrc
```
Add these lines:
```bash
ulimit -n 4096
umask 007
```
and re-login to your server account - the `ulimit` helps to run the bcl2fastq, and `umask` makes sure the folder you created can be accessed by other lab members.

### Unzip Files
```bash
gunzip *.gz
```

## Galaxy Analysis Pipeline

For Cut&Run data analysis, use the Galaxy:
https://training.galaxyproject.org/training-material/topics/epigenetics/tutorials/cut_and_run/tutorial.html#step-4-peak-calling

### ERE Sequences
- **Human ERE**: 5′-GGTCAnnnTGACC-3′
- **Mouse lactoferrin sequence mouse ERE**: 5'-GGTCAAGGTaACC-3'

### Galaxy Analysis Versions

#### Galaxy v2 Rerun:
- mapQ: threshold set to 10 instead of 20
- Do not remove the duplicates - see more peaks. But concerns remain

#### Galaxy v3 Rerun:
- Add control IgG sample 1 as the control

### Alternative Pipeline
Also tried: https://nf-co.re/cutandrun/dev/docs/usage/

**SEACR is more sensitive**: 
https://epigeneticsandchromatin.biomedcentral.com/articles/10.1186/s13072-019-0287-4

## Analysis Notes and Troubleshooting

### Quick Notes (240705):
Compared to H3K4, the Esr peaks (sample 3-5) seems to be all over the places? Not enough tight binding? Crosslink needed?

Read Jessica paper more: Check protocol - she did not crosslink. Uses different nuclei numbers (but sorted GFP+). And 1h at 4C instead of 2h

Ask Brian about crosslinking or not? Data diagnostics? Shorten incubation time?

### Quality Control Observations:
- Checked Jessica's paper: Veh samples also have poor peaks? Compare with ours now
- Worth doing NeuN sort to enrich neurons? GFP too hard, save for snATAC
- **MarkDuplicate**: S3 and S4 are high (11-13%), no need to seq deeper
- **Insert size histogram**: a little broader than expected? No ab control is important? Shorten cut time - how long is it now? Library do not see 150 300 etc peaks
- **Fingerprint**: no enrichment

Reference: https://training.galaxyproject.org/training-material/topics/epigenetics/images/cut_and_run/cut_and_run_yield.png

### Protocol Comparison Questions:
- Jiwen's version nuclei douncing?
- How much primary ERα antibody (0.5 μg)? At 1μg/μL? Jessica 1:100.
- Is the 4°C really 4°C?
- Check PCR 14 cycles
- Would the NeuN FACS interfere with later Cut&Run?

### Experimental Plan:
- Ctx+E2 NeuN 500k - ERα
- Ctx+E2 NeuN 500k - IgG
- Hypo+E2 NeuN 500k - ERα
- Hypo+E2 NeuN 500k - light crosslinking - ERα
- Hypo+E2 NeuN 500k - IgG (?)
- Cell line - with and without crosslinking

## Step 3: QC of Positive and Negative Control Samples

1. Copy the `.sh` file to the `fastq_files` folder
2. Update your sample name (`.fastq`) in the bash file
3. Run it by: `bash *.sh`
4. Copy the values from the terminal to the Excel sheet per sample

You can check the specificity of your reaction/antibody this way as a QC. You expect the positive control (H3K4) to reach 100% and negative control (IgG) has less than 20% off targets.

## Step 4: Analysis

### Environment Setup
Follow this page for installation (conda create):
https://github.com/mbassalbioinformatics/CnRAP

```bash
conda create -n cnrap_env
# Check the env creation is successful

conda activate cnrap_env
conda install -c bioconda trimmomatic bwa picard samtools deeptools seacr homer meme ucsc-bedgraphtobigwig bioconductor-biocinstaller bioconductor-chipseeker
```

### Install Required Genome for Homer
Update the highlighted bit by your path:
```bash
perl ~/anaconda3/pkgs/homer-<v#>/share/homer/configureHomer.pl -install <req_genome>
```

**Xin uses**:
```bash
perl ~/miniconda3/pkgs/homer-4.11-pl5262h7d875b9_5/share/homer/configureHomer.pl -install mm10
```

### Manual Download Stampy
```bash
git clone https://github.com/uwb-linux/stampy.git
cd stampy
make -j 8
```

Add the below path to your `~/.bashrc`:
```bash
export PATH=$PATH:/gpfs/home/xinjin/apps/stampy
```

Download kseq and gcc compile it

### Build Genome Prep

**Can use existing**:
```
/gpfs/group/jin/ref_genomes/mm10_piggybacinsert/bwa_index
```

**Or build new**:
```bash
bwa index -p <index_name> <masked_genome_fasta>.fa
stampy.py --species=<species> --assembly=<assembly_name> -G <index_name> <masked_genome_fasta>.fa
stampy.py -g <index_name> -H <index_name>
```

**Example for human**:
```bash
bwa index -p human_bwa_index_hg38_masked hg38_masked.fa
stampy.py --species=human --assembly=hg38_masked -G human_bwa_index_hg38_masked ./hg38_masked.fa
stampy.py -g human_bwa_index_hg38_masked -H human_bwa_index_hg38_masked
```