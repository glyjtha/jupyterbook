# Shareseq Pipeline Guide (Scripps HPC)

*Shareseq package is already installed on Scripps HPC by JC*

### BEFORE YOU START
- Install XQuartz (windows please use [Xming:](https://sourceforge.net/projects/xming/))
- Use ssh -Y xinjin@login00.scripps.edu 

### IMPORTANT:
- module load python/2.7.11 # do not use other version of python! Low yaml version number

### DATA UPDATE:
- Update the YMAL from dropbox
- Update .sh from dropbox
- Identify the Illumina seq run folder and make sure the permission is correct – if not just copy to a new address for analysis permission

```bash
rsync -a  /Users/xinjin/Dropbox\ \(Scripps\ Research\)/Jin\ Lab\ Internal/Xin/scripts/220517shareseq/SampleSheet.csv xinjin@login00.scripps.edu:/gpfs/group/jin/Sequencing/nextseq/copy/220517_VH00464_139_AAC3CG3M5/

rsync xinjin@login00.scripps.edu:/opt/applications/shareseqalign/gnu/1.0/splitProjects_V4_novaseq_V2.py /Users/xinjin/Dropbox\ \(Scripps\ Research\)/Jin\ Lab\ Internal/Xin/scripts/220531shareseq_test/

rsync xinjin@login00.scripps.edu:/gpfs/group/jin/xin/shareseq/220517/testagain.fa /Users/xinjin/Dropbox\ \(Scripps\ Research\)/Jin\ Lab\ Internal/Xin/scripts/220617_shareseq_zhiyi_analysis
```


### Post analysis:
```bash
cp *.pdf /gpfs/group/jin/xin/shareseq/220920shareseqtest/
cp *.txt /gpfs/group/jin/xin/shareseq/220920shareseqtest/
cp *counts.csv* /gpfs/group/jin/xin/shareseq/220920shareseqtest/
cp *RefSeqTSS* /gpfs/group/jin/xin/shareseq/220920shareseqtest/
```

#### KAUSHIK 
```bash
python/2.7.11
```

### Install Conda Environment
```bash
cd /gpfs/home/xinjin/
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh 
source ~/.bashrc
module load python/2.7.11  #can run P2
PYTHON3=~/miniconda3/bin/python #can run P3
Type python2 -> yaml is 5.4.1 check

module purge
```

### Setup for umi_tools 0.5.5 (already installed by JC)
```bash
source ~/.bashrc
conda create --name py2711 python=2.7
source activate py2711
python --version  // confirm version
conda install umi_tools==0.5.5 ## need sudo to do
```

### Install Higher Version Subread
```bash
wget https://sourceforge.net/projects/subread/files/subread-2.0.3/subread-2.0.3-source.tar.gz
tar -xvf xxx
cd folder/src
make -f Makefile.Linux
```

### Use
```bash
~/subread-2.0.3-source/bin/featureCounts 
~/seqtk/seqtk
```

### Run The Script
- Re start the terminal
```bash
srun --x11 --pty bash -i 
bash shareseq.sh 
```

### Download Files
```bash
rsync -r xinjin@login00.scripps.edu:/gpfs/group/jin/xin/shareseq/220517/output/sp.rna/downloadrna ./
rsync -a ./starindexing.sh xinjin@login00.scripps.edu:/gpfs/group/jin/xin/shareseq/
```

### How to use tmux:
```bash
tmux
Control b, then d (to leave)
tmux attach (is to attach back the screen)
```

### Install `mosh` for Stable SSH
[mosh](https://mosh.org/#getting)

### INSTALL CONDA ENV (Python 2.7)
```bash
module load miniconda
conda create --name xinenv python=2.7.11
source activate xinenv
python --version  // confirm version
```

- Then install dependencies:
```bash
conda install numpy
conda install -c conda-forge argparse
conda install -c anaconda more-itertools
conda install pyyaml
pip install pyyaml==5.3.1 #need to be higher than 5.1
conda install -c conda-forge gzip
conda install -c conda-forge regex
conda install -c conda-forge python-levenshtein
conda install -c conda-forge biopython
conda install -c conda-forge r-optparse
conda install -c bioconda pysam
conda install numpy
pip install umi_tools==0.5.5 //only this version is fine w python2
```
- Environment location: `/gpfs/home/xinjin/.conda/envs/xinenv`

### Edit `.bashrc`:
```bash
nano ~/.bashrc
```

### Add:
```bash
#module load miniconda
#source activate xinenv
export PYTHONPATH=’/gpfs/group/home/xinjin/.conda/envs/xinenv/lib/python2.7/site-packages’
```
### Rsync Example Script
```bash
Rsync -a /Users/xinjin/Dropbox\ \(Scripps\ Research\)/Jin\ Lab\ Internal/Xin/scripts/220531shareseq_test/Split_seq_example.sh xinjin@login00.scripps.edu:/gpfs/group/jin/xin/shareseq/test/

chmod +x shareseq.sh      # make it executable
```

### Manual Demultiplexing
```bash
bcl2fastq -p 12 --barcode-mismatches 1 --output-dir ./fastqs --no-lane-splitting --mask-short-adapter-reads 0 --create-fastq-for-index-reads  2>>./Run.log
```

### Example Read Count Using grep
```bash
grep "​​GCAGCTTTCCGGGCGATGCCA" Bcl11b_S1_R1_001.fastq.gz | wc -l
```









grep "​​GCAGCTTTCCGGGCGATGCCA" Bcl11b_S1_R1_001.fastq.gz | wc -l


