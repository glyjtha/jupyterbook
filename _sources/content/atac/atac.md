# Bulk ATAC-seq Analysis

## Experimental Protocol
See Dropbox/10x Genomics/bulkATAC for the Corces lab protocol

## ATAC Quick QC
- **Reference**: https://bioconductor.org/packages/release/bioc/html/ATACseqQC.html
- Check TSS
- Check dup rate (> 50% is considered saturation)

## Running PEPATAC

### 1. Create sample.csv
See or copy `/gpfs/group/jin/Joshua/pepatac/230408Jill/sample.csv` for an example

```csv
sample_name,protocol,organism,read1,read2,read_type
A50k_S2,ATAC,mouse,R1,R2,paired
AB50k_S3,ATAC,mouse,R1,R2,paired
AB5k_1_S4,ATAC,mouse,R1,R2,paired
```

### 2. Create looper config file (XXX.yaml)
Ensure the bolded fields are updated appropriately.
See or copy `/gpfs/group/jin/Joshua/pepatac/230408Jill/230408Jill.yaml` for an example

```yaml
name: ProjectName

pep_version: 2.0.0
sample_table: /path/to/samples.csv

looper:
  output_dir: "/path/to/output"
  pipeline_interfaces: ["/gpfs/group/jin/software/pepatac/project_pipeline_interface.yaml"]

sample_modifiers:
  append:
    pipeline_interfaces: ["/gpfs/group/jin/software/pepatac/sample_pipeline_interface.yaml"]
  derive:
    attributes: [read1, read2]
    sources:
      R1: "/path/to/fastq_files/{sample_name}_R1_001.fastq.gz"
      R2: "/path/to/fastq_files/{sample_name}_R2_001.fastq.gz"
  imply:
    - if:
        organism: ["mouse", "Mus musculus", "Mouse", "Mus musculus"]
      then:
        genome: mm10
        #prealignment_names: []
        deduplicator: samblaster # Default. [options: picard]
        trimmer: skewer          # Default. [options: pyadapt, trimmomatic]
        peak_type: fixed         # Default. [options: variable]
        extend: "250"            # Default. For fixed-width peaks, extend this distance up- and down-stream.
        frip_ref_peaks: None     # Default. Use an external reference set of peaks instead of the peaks called from this run
```

### 3. Reference Genome Setup
Ensure appropriate refgenie references have been downloaded at `/gpfs/group/jin/ref_genomes/pepatac` (hg38 and mm10 references are already available)

**Note**: Do not have to do this if using already downloaded reference!

```bash
conda activate /gpfs/group/jin/software/pepatac_env
refgenie pull XYZ
```

### 4. Run Pipeline

```bash
# Make screen session
screen -S name_of_session

# Request a node first (appropriate resources are below)
cd /path/to/your_config.yaml
srun --time=24:00:00 --cpus-per-task=16 --mem=16000 --pty bash
conda activate /gpfs/group/jin/software/pepatac_env
export REFGENIE=/gpfs/group/jin/ref_genomes/pepatac/genome_config.yaml
looper run XXX.yaml
looper runp XXX.yaml
looper report XXX.yaml
```

### Resources Chart
For sample runs only (one at a time, most cases for us right now). For parallel processing see documentation.

| Max File Size | CPU-per-task | Memory | Time     |
|--------------|--------------|--------|----------|
| 0.05         | 4            | 8000   | 3:00:00  |
| 0.5          | 8            | 10000  | 6:00:00  |
| 1            | 12           | 12000  | 8:00:00  |
| 10           | 24           | 16000  | 16:00:00 |
| NaN          | 32           | 32000  | 24:00:00 |

## Alternative Analytical Pipelines

### ENCODE Pipeline
- **GitHub**: https://github.com/ENCODE-DCC/atac-seq-pipeline
- **Documentation**: https://www.encodeproject.org/atac-seq/
- Install caper (using pip)
- Correct the conf file: account=jin, partition=shared
- Git clone the pipeline
- Define JSON for your sample input
- Path: `/usr/bin/singularity`

### PEPATAC Singularity Approach
- **Documentation**: https://pepatac.databio.org/en/latest/tutorial/

## QC Results Tables

### First Batch Results
| Sample      | Dup Rate | TSS | Notes           |
|-------------|----------|-----|-----------------|
| M1_Veh_A_S1 | 2.23     | 2.1 |                 |
| M1_Veh_B_S2 | 1.9      | 2.3 |                 |
| M1_Veh_C_S3 | -        | -   | Pipeline crashes|
| M2_Veh_A_S4 | 2        | 1.9 |                 |
| M2_Veh_B_S5 | 2.2      | 3.6 |                 |
| M2_Veh_C_S6 | 2.6      | 5   |                 |
| F3_Veh_A_S7 | -        | 3.3 |                 |
| F3_Veh_B_S8 | 2.2      | 2.1 |                 |
| M4_E2_A_S9  | 2.2      | 4.6 |                 |
| M4_E2_B_S10 | 2.1      | 3.4 |                 |
| M4_E2_C_S11 | 1.8      | 2.5 |                 |
| F5_E2_A_S12 | 0.8      | 2.6 |                 |
| F5_E2_B_S13 | 2.2      | 2.7 |                 |
| F5_E2_C_S14 | 2.4      | 2.1 |                 |
| F6_E2_A_S15 | 2.1      | 5   |                 |
| F6_E2_B_S16 | 2.5      | 4.5 |                 |
| F6_E2_C_S17 | 3.2      | 3.5 |                 |

### Second Batch Results
| Sample     | Dup Rate | TSS |
|------------|----------|-----|
| T50k_S1    | -        | -   |
| A50k_S2    | -        | -   |
| AB50k_S3   | -        | -   |
| AB5k_1_S4  | -        | -   |
| AB5k_2_S5  | -        | -   |
| AB5k_3_S6  | -        | -   |

## Manual Singularity Command Example

```bash
SAMPLE="T50k_S1"
cd /gpfs/group/jin/xin/atac/230409Jill
singularity exec --bind "/gpfs/group/jin:/gpfs/group/jin" \
    /gpfs/group/jin/software/pepatac_singularity \
    /gpfs/group/jin/software/pepatac/pipelines/pepatac.py \
    --single-or-paired paired\
    --genome mm10 \
    --genome-index /gpfs/group/jin/ref_genomes/pepatac/data/0f10d83b1050c08dd53189986f60970b92a315aa7a16a6f1/bowtie2_index/default/0f10d83b1050c08dd53189986f60970b92a315aa7a16a6f1 \
    --chrom-sizes /gpfs/group/jin/ref_genomes/pepatac/alias/mm10/fasta/default/mm10.chrom.sizes \
    --TSS-name /gpfs/group/jin/ref_genomes/pepatac/alias/mm10/ensembl_gtf/default/mm10_ensembl_TSS.bed \
    --sample-name $SAMPLE \
    --input "/gpfs/group/jin/sequencing/iseq/20230408_FS10002641_13_BRR99425-2309/fastq_files/${SAMPLE}_R1_001.fastq.gz" \
    --input2 "/gpfs/group/jin/sequencing/iseq/20230408_FS10002641_13_BRR99425-2309/fastq_files/${SAMPLE}_R2_001.fastq.gz" \
    -O /gpfs/group/jin/xin/atac/230409Jill/ > /gpfs/group/jin/xin/atac/230409Jill/output/new_$SAMPLE.out 2> /gpfs/group/jin/xin/atac/230317Jill/output/new_$SAMPLE.err
```

## File Organization Commands

```bash
# Copy features file
find . -name DONE -execdir cp /Users/percyli/Desktop/features\[30,55\].R . \;

# Organize TSS files
mkdir TSS
rsync -amv --include '*/' --include '*/QC_mm10/*enrichment.pdf' --exclude '*' ./ ./TSS
cd TSS
mv -v ./*/*/* ./

# Organize duplication metrics
rsync -amv --include '*/' --include '*/aligned_mm10/*dedup_metrics_log*' --exclude '*' ./ ./dup
mv -v ./*/*/* ./

# Organize FastQC files
mkdir fastqc
rsync -amv --include '*/' --include '*/fastqc/*fastqc.html' --exclude '*' ./ ./fastqc
cd fastqc
mv -v ./*/*/* ./
```

## Error Handling / Manual Bypass

```bash
conda activate /gpfs/group/jin/software/pepatac_env/
cd /gpfs/group/jin/xin/atac/230409Jill/
Rscript /gpfs/group/jin/software/pepatac/tools/PEPATAC.R reduce -i /gpfs/group/jin/xin/atac/230409Jill/AB5k_3_S6/peak_calling_mm10/AB5k_3_S6_peaks_fixedWidth.narrowPeak -s AB5k_3_S6 -c /gpfs/group/jin/ref_genomes/pepatac/alias/mm10/fasta/default/mm10.chrom.sizes --normalize
```

## DiffBind Analysis

```bash
# Start interactive session and run R DiffBind
srun --time=15:00:00 --cpus-per-task=16 --mem=16000 --pty bash
```

Use DiffBind, see R script in `xin/atatc/` folder

**Reference**: https://ucdavis-bioinformatics-training.github.io/2020-Epigenetics_Workshop/data_analysis/chip_atac/atac_R

## Manual Pipeline

**Guideline from Harvard RC**: https://informatics.fas.harvard.edu/atac-seq-guidelines.html

### Conda Environment Setup

For conda users on HPC, switch to conda-forge:

```bash
conda config --add channels conda-forge
conda config --set channel_priority strict
conda config --add channels bioconda
conda config --set channel_priority strict
```

Make sure your `./condarc` looks like this:

```yaml
auto_activate_base: false
channels:
  - bioconda
  - conda-forge
channel_priority: strict
auto_update_conda: false
```

### Software Installation

```bash
# Activate existing environment
conda activate xinenv

# Install cutadapt
conda install cutadapt

# Cutadapt command example
cutadapt -a CTGTCTCTTATACACATCT -q 30 --minimum-length 36 --paired-output -o output.fastq.gz input.fastq.gz

# Bowtie2 setup
module load bowtie2
bowtie2-build <genome.fa> <genomeIndexName>
```

### Known Dependency Issues

```
ERROR: pip's dependency resolver does not currently take into account all the packages that are installed. This behaviour is the source of the following dependency conflicts.
yte 1.2.2 requires pyyaml<7.0,>=6.0, but you have pyyaml 5.4.1 which is incompatible.
schema-salad 7.1.20210316164414 requires CacheControl<0.12,>=0.11.7, but you have cachecontrol 0.12.10 which is incompatible.
hail 0.2.94 requires requests==2.25.1, but you have requests 2.28.2 which is incompatible.
dxpy 0.326.1 requires requests<=2.27.1,>=2.8.0, but you have requests 2.28.2 which is incompatible.
```

## Processing Summary

### ATAC-seq Processing Steps:
1. **Adapter Trimming**: Adapter sequences were trimmed from ATAC reads (cutadapt v1.3 -a CTGTCTCTTATACACATCT -q 30 --minimum-length 36 --paired-output)
2. **Alignment**: Trimmed reads were aligned to the mm10 genome (BOWTIE2 v2.1.0 -t -X2000 --no-mixed --no-discordant)
3. **Deduplication**: Redundant reads were removed (picard MarkDuplicates)
4. **Fragment Adjustment**: Fragment ends were offset by 4nt towards the center of each fragment
5. **Peak Calling**: Peaks were called with HOMER (findPeaks -region -size 500 -minDist 50 -o auto -tbp 0) using sub-nucleosomal (<100bp) fragments, and overlapping peaks from the 2 replicates were merged (BEDTOOLS merge) to yield a peak set for each cell type
6. **Visualization Track Generation**: 
   - Visualization tracks were generated by counting genome-wide coverage of all fragments (BEDTOOLS v2.15.0 genomeCoverageBed -bg), scaling to 10M total alignments (custom perl script), and converting to bigWig format (UCSC wigToBigWig)
   - Visualization tracks were also generated by counting genome-wide coverage of sub-nucleosomal (<100bp) fragments (BEDTOOLS v2.15.0 genomeCoverageBed -bg), scaling to 10M total alignments (custom perl script), and converting to bigWig format (UCS