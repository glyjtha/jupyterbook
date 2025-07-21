# HPC Guide for Scripps Research

## Introduction

Scripps research has high performance clusters available for use at no cost. To gain access to the cluster, email helpdesk@scripps.edu to set up an account. The clusters uses SLURM to manage and schedule jobs.

## 1. Command Line Interface

The standard way of accessing and using the HPC is through the command line interface. To connect the clusters, use the ssh command on Terminal for Mac, or Putty for Windows:

```bash
ssh username@login00.scripps.edu
```

## 2. Submitting Jobs

Scripts can be submitted to the clusters for scheduling using the command `sbatch`. Jobs can be checked using the command `squeue`. For example scripts, UF has a helpful wiki.

**Examples:**
```bash
sbatch script_name.sh
squeue -u $USER
```

### First Time Setup

If this is your first time you will need to edit your bashrc:

```bash
nano ~/.bashrc
```

Add these lines:
- `ulimit -n 4096`
- `umask 007`

## 3. Interactive Sessions

Users can also run interactive sessions to test/write scripts. Interactive sessions are created using the command `srun` with the following tags:
- `--time=HH:MM:SS`
- `--cpus-per-task=16`
- `--mem=64000` (in kB)
- `--pty bash` (put last)

**Example:**
```bash
srun --time=15:00:00 --cpus-per-task=16 --mem=16000 --pty bash
```

Use `srun --help` for more options. Use `exit` to end the interactive session. 

**Notes:**
- If your interactive session is running out of time, email hpc@scripps.edu to extend it
- If you need more than 180 Gb, add the line `--partition=highmem` when calling `srun` (up to 250 Gb)

## 4. Graphic User Interface

The HPC can be accessed through a graphic user interface (e.g. desktop, etc.) using the application x2go (https://wiki.x2go.org/doku.php).

**Settings:**
- **Host:** login00.scripps.edu
- **Session Type:** MATE
- **Under Media tab:** 
  - Deselect enable sound
  - Deselect client side printing

## 5. Software

The HPC has many packages/software installed for general use. A full list can be found on the intranet or using the command `module av` on the HPC. To load a package, use the command `module load MODULE_NAME`.

**Example:**
```bash
module load python/3.8.3
```

Install any new software into your own home directory. If you believe the software will be widely useful, you can email hpc@scripps.edu to have them installed on the clusters.