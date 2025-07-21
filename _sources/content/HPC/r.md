# HPC R Package Setup Guide

To gain HPC access, please review other documents in this folder.

## Reference Documentation

This is an explanation:
- https://services.dartmouth.edu/TDClient/1806/Portal/KB/ArticleDet?ID=141373
- https://docs.rc.fas.harvard.edu/kb/r-packages/

## Setup Steps

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
nano ~/.Renviron
```

Add this below line:
```
R_LIBS_USER=~/apps/R_version
```

### 3. Edit bashrc

```bash
nano ~/.bashrc
```

Add the below line:
```bash
export R_LIBS_USER=$HOME/apps/R_version:$R_LIBS_USER
```

### 4. You may want to request an node before installations:

```bash
srun --cpus-per-task 8 --mem-per-cpu 1500 --time 3:00:00 --pty bash
```

### 5. Enter R and start installing the packages and dependences. Common dependencies are below (in order!):

```r
install.packages("Rcpp", lib="~/R/x86_64-pc-linux-gnu-library/4.3")
install.packages("scCustomize", lib="~/R/x86_64-pc-linux-gnu-library/4.3")
install.packages("vangogh", lib="~/R/x86_64-pc-linux-gnu-library/4.3")
install.packages("dplyr", lib="~/R/x86_64-pc-linux-gnu-library/4.3")
install.packages("cli", lib="~/R/x86_64-pc-linux-gnu-library/4.3")
install.packages("RcppEigen", lib="~/R/x86_64-pc-linux-gnu-library/4.3")
devtools::install_github("satijalab/seurat", ref = "develop", lib="~/R/x86_64-pc-linux-gnu-library/4.3")
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

### 6. Edit the R script from the VS code

### 7. Run the R script using the command line:

```bash
Rscript seurat.R
```

## Important Configuration for R/4.3.0

**Important**: if you are using R/4.3.0, please add the below in your `~/.R/Makevars`

```makefile
## C++ flags 
CXX=g++
CXX11=g++
CXX14=g++
CXX17=g++
CXXFLAGS=-O3 -march=native -Wno-ignored-attributes
CXX11FLAGS=-O3 -march=native -Wno-ignored-attributes
CXX14FLAGS=-O3 -march=native -Wno-ignored-attributes
CXX17FLAGS=-O3 -march=native -Wno-ignored-attributes
CXXPICFLAGS=-fPIC
CXX11PICFLAGS=-fPIC
CXX14PICFLAGS=-fPIC
CXX17PICFLAGS=-fPIC
CXX11STD=-std=c++11
CXX14STD=-std=c++14
CXX17STD=-std=c++17
## C flags
CC=gcc
CFLAGS=-O3 -march=native
## Fortran flags
FC=gfortran
F77=gfortran
FFLAGS=-O3 -march=native
FCFLAGS=-O3 -march=native
```