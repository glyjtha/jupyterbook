# HPC RStudio Server Setup Guide

To gain HPC access, please review other documents in this folder.

Refer to https://github.com/asunboi/scripps_hpc_rstudio for any changes in the codebase.

The below steps will install a RStudio server with **R/4.3.1 as the base R version**. Please let me know if you need a different version of R, and I will set that up as well.

**No packages (such as Seurat) will be pre-installed, as each user has different library requirements and immutable packages lead to dependency issues.**

## Installation Steps

1. Log into the HPC by opening a terminal and entering `ssh youremail@login00.scripps.edu`. Other login nodes can also be used, read HPC FAQ

2. Enter `git clone https://github.com/asunboi/scripps_hpc_rstudio`. This will create a folder named `scripps_hpc_rstudio` in your home directory.

3. Create an online account with Sylabs, and generate a personal token. (Usually good for a month)

4. In HPC, run `singularity remote login`, and enter your username and personal token.
   * If FATAL error run:
     * `singularity remote add sylabs-cloud cloud.sylabs.io`
     * `singularity remote use sylabs-cloud`
     * `singularity remote login`

5. Run the command `cd ~/scripps_hpc_rstudio` to enter the folder generated in step 2.

6. Run the command `singularity build rstudio-hpc-v3.sif rstudio-hpc.def` to build image (Re-run this command if modified .def)

7. `sbatch start_rstudio.sh` to submit the job to a computing node.
   * Note, modify the #SBATCH tags to request different number of CPUs and RAMs.

8. Check the log.txt file using `less log.txt` for tunnel access information and the username/password.

9. Copy and paste the SSH tunnel access command to new terminal

10. Open a browser and go to the specified localhost address

11. Login with the password given from log.txt

## Setting Up R Packages

To install new packages into a local R directory, refer to Set up R on HPC, as Rstudio will use that folder as your local directory. I've copied the instructions below for your convenience.

### 1. Set up R.lib path: create a folder called userLibrary

```bash
cd ~
mkdir -pv R/x86_64-pc-linux-gnu-library/4.3
```

Go to R (4.3.0) and use this below line to modify path (within R environment):

```r
.libPaths()
.libPaths(.libPaths()[2])
.libPaths( c( .libPaths(), "~/R/x86_64-pc-linux-gnu-library/4.3") )
```

### 2. Next, you need to set up R environment to specify this path:

```bash
vim ~/.Renviron
```

Add this below line:
```
R_LIBS_USER=~/R/%p-library/%v
```

### 3. You may want to request an node before installations:

```bash
srun --cpus-per-task 8 --mem-per-cpu 1500 --time 3:00:00 --pty bash
```

### 4. Open RStudio and start installing the packages and dependences. Common dependencies are below (in order!):

```r
install.packages("https://cran.r-project.org/src/contrib/Archive/Matrix/Matrix_1.6-5.tar.gz", repos = NULL, type = "source")
install.packages("Rcpp", lib="~/R/x86_64-pc-linux-gnu-library/4.3")
install.packages("scCustomize", lib="~/R/x86_64-pc-linux-gnu-library/4.3")
install.packages("vangogh", lib="~/R/x86_64-pc-linux-gnu-library/4.3")
install.packages("dplyr", lib="~/R/x86_64-pc-linux-gnu-library/4.3")
install.packages("cli", lib="~/R/x86_64-pc-linux-gnu-library/4.3")
install.packages("RcppEigen", lib="~/R/x86_64-pc-linux-gnu-library/4.3")
install.packages("Seurat")
if (!requireNamespace("BiocManager", quietly = TRUE)) install.packages("BiocManager")
BiocManager::install("glmGamPoi", lib="~/R/x86_64-pc-linux-gnu-library/4.3")
BiocManager::install("dittoSeq",lib="~/R/x86_64-pc-linux-gnu-library/4.3")
remotes::install_github("snandi/RFunctionsSN",lib="~/R/x86_64-pc-linux-gnu-library/4.3")
devtools::install_github("snandi/RFunctionsSN",lib="~/R/x86_64-pc-linux-gnu-library/4.3")
devtools::install_github("databio/pepatac", subdir="PEPATACr",lib="~/R/x86_64-pc-linux-gnu-library/4.3")
if (!require("BiocManager", quietly = TRUE))
    install.packages("BiocManager",lib="~/R/x86_64-pc-linux-gnu-library/4.3")
BiocManager::install(version = "3.17", lib="~/R/x86_64-pc-linux-gnu-library/4.3")
```

**IMPORTANT: sometimes, the dependency app is not being installed automatically. Read the error messages and install them manually (like Rcpp, cli).**