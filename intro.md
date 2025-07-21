---
title: Introduction
---

# Jin Lab Dry Lab Handbook

This handbook collects all of our commonly used pipelines, cheat‑sheets, and guides for Dry Lab analyses at JinLab. It’s designed to be a “one‑stop” reference for:

- **10x Genomics workflows** – Quickstarts, cheat sheets, and tips for Cell Ranger, ATAC, Multiome, etc.  
- **HPC set‑ups** – How to install and run R, RStudio, and other tools on our cluster.  
- **NGS utilities** – Shell scripts, SampleSheet templates, and alignment pipelines.  
- **Data transfer & storage** – Best practices for moving data between Scripps Core, GPFS, and local machines.  

---

## How to use this book

1. **Browse the sidebar** or the Contents below to jump to a section.  
2. Each page is a standalone guide—feel free to print or view it on your laptop when you’re on the bench.  
3. Want to contribute? Fork this repository, add your `.md` under `content/`, then send us a pull‑request!

```{toctree}
:maxdepth: 2
:caption: Contents

10x_analysis/Cell_Ranger_ATAC
10x_analysis/Cell Ranger_Singel_Cell
10x_analysis/Single_Cell_Cluster_Seurat

HPC/Set_up_R_on_HPC
HPC/Set_up_RStudio_on_HPC

demulti
NGS_indel_analysis
PIPseq
SampleSheet_MiSeq
File_Storage_System
Shareseq_alignment_and_analysis
STAR_Alignment
Transfer_data_from_Scripps_Genomic_Core
