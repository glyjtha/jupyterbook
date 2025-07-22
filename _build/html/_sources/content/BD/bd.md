# BD Rhapsody Pipeline Guide

## Resources

- [BD Rhapsody Bioinfo Documentation](https://bd-rhapsody-bioinfo-docs.genomics.bd.com/setup/input/top_input.html)
- [Primary Analysis V2.2 Documentation](https://www.dropbox.com/scl/fi/k2z01gbzoznem9tlxchue/primaryAnalysis_V2.2_USRegion.html?rlkey=11cuyr3f0cqxcr1030onta75c&dl=0)

## Input Requirements

### 1. Fastq files

Example library FASTQ files for WTA assay:
- `WTALibrary1_S1_L001_R1_001.fastq.gz`
- `WTALibrary1_S1_L001_R2_001.fastq.gz`

Example library FASTQ files for ATAC-Seq assay:
- `ATACLibrary_S2_L001_R1_001.fastq.gz`
- `ATACLibrary_S2_L001_R2_001.fastq.gz`
- `ATACLibrary_S2_L001_I2_001.fastq.gz`

### 2. Reference file

**For WTA assays:** Obtain a pre-built reference genome archive file for human or mouse by downloading from the following link:
```
bd-rhapsody-public.s3-website-us-east-1.amazonaws.com/Rhapsody-WTA/
```

**For ATAC-Seq and Multiomic WTA+ATAC-Seq assays:** Obtain a pre-built reference genome archive file for human or mouse by downloading from the following link:
```
http://bd-rhapsody-public.s3-website-us-east-1.amazonaws.com/Rhapsody-WTA-ATAC/
```

**Example download command:**
```bash
wget http://bd-rhapsody-public.s3-website-us-east-1.amazonaws.com/Rhapsody-WTA-ATAC/Pipeline-version2.x_WTA_ATAC_references/RhapRef_Mouse_WTA-ATAC_2023-09.tar.gz
```

### 3. Pipeline parameters

## Key Questions & Answers

1. **Algorithm comparing to other pipelines (e.g. cellranger)? Pros & cons, accuracy, confidence in cell calling and speed.**
   - **Runtime: 6-12 hours usually**

2. **Reference data. What exactly is each ref (mm10? mm39)? How can we add gRNA to this pipeline (into ref genome?)**
   - **mm39**
   - **Make reference or supplemental reference**

3. **Set up pipeline. Docker required?**
   - Tried docker-free pipeline install bundle, worked with test dataset.

4. **Demultiplex to get fastq: bcl2fastq?**
   - **Specify the index length**

5. **How to run multiple channels?**
   - **Align separately**

6. **Sequencing specifications:**
   - Most customers do 25k-40k reads/cell
   - Aviti and NextSeq for sequencing
   - Ask Helen for details