# Demultiplexing Script (demulti.sh)

```bash
#!/bin/bash
#SBATCH --mem=32gb
#SBATCH --nodes=1
#SBATCH --cpus-per-task=8
#SBATCH --time=3:00:00

## Running cellranger analysis of 10X data WITH hashtag libraries
## NB: use different script for cDNA only libraries
## Configured to work with Slurm

cd /gpfs/group/jin/Sequencing/miseq/220914_M01440_0906_000000000-GDB22

mkdir fastq_files

module load bcl2fastq

bcl2fastq -p 12 --barcode-mismatches 1 --output-dir ./fastq_files --no-lane-splitting

#tar -xvf 220413_A01255_0130_AH7TC3DRX2.tar

##LIBRARIESPATH="/gpfs/home/jaroslav/data/raw_fastq/libraries_combined_NS870.csv"
##FEATUREREFPATH="/gpfs/home/jaroslav/data/raw_fastq/featureReferenceHashes_combined_NS870.csv"
```

## Script Parameters

| Parameter | Value | Description |
|-----------|-------|-------------|
| `--mem` | 32gb | Memory allocation |
| `--nodes` | 1 | Number of nodes |
| `--cpus-per-task` | 8 | CPUs per task |
| `--time` | 3:00:00 | Maximum runtime |

## bcl2fastq Parameters

| Parameter | Value | Description |
|-----------|-------|-------------|
| `-p` | 12 | Number of processing threads |
| `--barcode-mismatches` | 1 | Allowed barcode mismatches |
| `--output-dir` | ./fastq_files | Output directory |
| `--no-lane-splitting` | - | Combine lanes in output |

## Usage Instructions

1. Copy this script to your run folder
2. Update the path in the `cd` command to match your run directory:
   ```bash
   cd /gpfs/group/jin/Sequencing/miseq/[YOUR_RUN_NAME]
   ```
3. Submit the job:
   ```bash
   sbatch demulti.sh
   ```
4. Check job status:
   ```bash
   squeue -u your_username
   ```

## Notes

- This script is configured for 10X data with hashtag libraries
- The script creates a `fastq_files` directory for output
- Commented lines show examples of additional configurations for library and feature reference paths