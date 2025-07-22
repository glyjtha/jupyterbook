# Shareseq Pipeline Guide
*Shareseq package is already installed on Scripps HPC by JC*

## Before You Start

- Install XQuartz (Windows users please use [Xming](https://sourceforge.net/projects/xming/))
- Use SSH with X11 forwarding:
  ```bash
  ssh -Y xinjin@login00.scripps.edu
  ```

## Important Notes

- **Critical:** Use only the specified Python version:
  ```bash
  module load python/2.7.11  # do not use other version of python! Low yaml version number
  ```

## Data Update

- Update the YAML from dropbox
- Update .sh from dropbox  
- Identify the Illumina seq run folder and make sure the permission is correct – if not just copy to a new address for analysis permission

### Example Rsync Commands

```bash
# Copy SampleSheet
rsync -a /Users/xinjin/Dropbox\ \(Scripps\ Research\)/Jin\ Lab\ Internal/Xin/scripts/220517shareseq/SampleSheet.csv xinjin@login00.scripps.edu:/gpfs/group/jin/Sequencing/nextseq/copy/220517_VH00464_139_AAC3CG3M5/

# Download Python script
rsync xinjin@login00.scripps.edu:/opt/applications/shareseqalign/gnu/1.0/splitProjects_V4_novaseq_V2.py /Users/xinjin/Dropbox\ \(Scripps\ Research\)/Jin\ Lab\ Internal/Xin/scripts/220531shareseq_test/

# Download FASTA file
rsync xinjin@login00.scripps.edu:/gpfs/group/jin/xin/shareseq/220517/testagain.fa /Users/xinjin/Dropbox\ \(Scripps\ Research\)/Jin\ Lab\ Internal/Xin/scripts/220617_shareseq_zhiyi_analysis
```

## Post Analysis

Copy output files to analysis directory:

```bash
cp *.pdf /gpfs/group/jin/xin/shareseq/220920shareseqtest/
cp *.txt /gpfs/group/jin/xin/shareseq/220920shareseqtest/
cp *counts.csv* /gpfs/group/jin/xin/shareseq/220920shareseqtest/
cp *RefSeqTSS* /gpfs/group/jin/xin/shareseq/220920shareseqtest/
```

## Environment Setup

### Install Conda Environment

```bash
cd /gpfs/home/xinjin/
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh 
source ~/.bashrc
module load python/2.7.11  # can run P2
PYTHON3=~/miniconda3/bin/python  # can run P3
# Type python2 -> yaml is 5.4.1 check
module purge
```

### Setup for umi_tools 0.5.5 (already installed by JC)

```bash
source ~/.bashrc
conda create --name py2711 python=2.7
source activate py2711
python --version  # confirm version
conda install umi_tools==0.5.5  # need sudo to do
```

### Install Higher Version Subread

```bash
wget https://sourceforge.net/projects/subread/files/subread-2.0.3/subread-2.0.3-source.tar.gz
tar -xvf subread-2.0.3-source.tar.gz
cd subread-2.0.3-source/src
make -f Makefile.Linux
```

### Usage

```bash
~/subread-2.0.3-source/bin/featureCounts 
~/seqtk/seqtk
```

## Running the Script

1. **Restart the terminal**
2. **Run with X11 forwarding:**
   ```bash
   srun --x11 --pty bash -i 
   bash shareseq.sh 
   ```

## Download Files

```bash
# Download RNA output
rsync -r xinjin@login00.scripps.edu:/gpfs/group/jin/xin/shareseq/220517/output/sp.rna/downloadrna ./

# Upload indexing script
rsync -a ./starindexing.sh xinjin@login00.scripps.edu:/gpfs/group/jin/xin/shareseq/
```

## Using tmux for Session Management

```bash
tmux                    # start tmux session
# Control + b, then d   # detach from session
tmux attach             # reattach to session
```

## Install mosh for Stable SSH

For more stable SSH connections, install [mosh](https://mosh.org/#getting).

## Install Conda Environment (Python 2.7)

### Create Environment

```bash
module load miniconda
conda create --name xinenv python=2.7.11
source activate xinenv
python --version  # confirm version
```

### Install Dependencies

```bash
conda install numpy
conda install -c conda-forge argparse
conda install -c anaconda more-itertools
conda install pyyaml
pip install pyyaml==5.3.1  # need to be higher than 5.1
conda install -c conda-forge gzip
conda install -c conda-forge regex
conda install -c conda-forge python-levenshtein
conda install -c conda-forge biopython
conda install -c conda-forge r-optparse
conda install -c bioconda pysam
conda install numpy
pip install umi_tools==0.5.5  # only this version is fine with python2
```

**Environment location:** `/gpfs/home/xinjin/.conda/envs/xinenv`

### Edit .bashrc

```bash
nano ~/.bashrc
```

### Add to .bashrc

```bash
# module load miniconda
# source activate xinenv
export PYTHONPATH='/gpfs/group/home/xinjin/.conda/envs/xinenv/lib/python2.7/site-packages'
```

## Script Management

### Example Rsync Script Upload

```bash
rsync -a /Users/xinjin/Dropbox\ \(Scripps\ Research\)/Jin\ Lab\ Internal/Xin/scripts/220531shareseq_test/Split_seq_example.sh xinjin@login00.scripps.edu:/gpfs/group/jin/xin/shareseq/test/

chmod +x shareseq.sh  # make it executable
```

## Manual Demultiplexing

```bash
bcl2fastq -p 12 \
    --barcode-mismatches 1 \
    --output-dir ./fastqs \
    --no-lane-splitting \
    --mask-short-adapter-reads 0 \
    --create-fastq-for-index-reads \
    2>>./Run.log
```

## Example Read Count Using grep

```bash
grep "GCAGCTTTCCGGGCGATGCCA" Bcl11b_S1_R1_001.fastq.gz | wc -l
```