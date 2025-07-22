# RNAscope Analysis Guide

For an introduction to using the high performance clusters, look here

## To analyze RNAscope

### 1. Preprocess Images
Analysis is run on maximum projections with each channel saved individually. Using ImageJ/Fiji, run Maximum Projection and Split Channels on each image. Alternatively, the `MaxProjectAndSplit.ijm` macro in the Dropbox (`Jin Lab Internal/Josh/imageanalysis/MaxProjectAndSplit.ijm`) can be used to automatically preprocess images.

### 2. Move Images to Cluster
Images can be uploaded to the Scripps HPC clusters using Cyberduck. See here for more information. 

**Note:** if the pipeline (`.cpproj`) is not on the server already, upload it as well

### 3. Create Batch Files
Using x2go, we can create batch files to run the analysis remotely. At the top left corner of the GUI, open the Terminal. Then, type the commands:
- `module load cellprofiler/4.2.1`
- `cellprofiler` 

to open up CellProfiler GUI. Use the CellProfiler GUI to open your desired pipeline. Make sure your output settings and spreadsheet prefix are correct, then hit analyze images. Make note of what you choose as the prefix in the ExportSpreadsheets module for the next step. Hitting the "Analyze Images" will create `Batch_data.h5` in the output folder.

### 4. Run CellProfiler
Using a CLI, access the HPC. To submit the cp job to the cluster, use the command:

```bash
bash /gpfs/group/jin/software/run_cellprofiler.sh /dir/to/output /dir/to/Bach_data.h5 cp_prefix
```

where `cp_prefix` is the prefix from step 3.

### 5. Downstream Analyses
The pipeline will output raw CellProfiler outputs as spreadsheets starting with the given prefixes. Basic per cell aggregates are calculated, plotted, and saved into the "analyses" subfolder of the output folder. Further analyses can be performed manually using either the raw CellProfiler output or the aggregated data. 

**Obsolete:** Make a copy of `RNAscope_downstream.py` (found in `/gpfs/group/jin/software/RNAscope_downstream.py`) for yourself and modify it to read in your CellProfiler output (change data directory path and prefix) as well as your desired output folder. After loading python (`module load python`) and the appropriate environment (`source /gpfs/group/jin/software/RNAscope/bin/activate`), run:

```bash
python RNAscope_downstream.py
```