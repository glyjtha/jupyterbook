# Scripps Research File Storage Systems Guide

## Introduction

File storage systems made available by Scripps Research fall into categories--daily use file systems and archival file systems. Daily use file systems can be used to store active projects and any past data, analyses, or manuscripts. Archival file systems are used to store raw data ONLY, such as unprocessed images or genomic data. The available archival file systems are StorNext and object storage.

## Daily Use File Systems

### Dropbox
Dropbox should be the main location where all files should be stored. Dropbox syncs all folders automatically, but users can manually select which folders get synced. All members receive unlimited storage through Scripps.

### Broad Resources

#### VPN Access
- **URL**: https://intranet.broadinstitute.org/bits/service-catalog/networking/vpn
- **Login**: Use Broad email login

#### SSH Access
- **URL**: https://intranet.broadinstitute.org/bits/service-catalog/scientific-computing/login-servers
- **Command**: 
  ```bash
  ssh xinjin@login.broadinstitute.org
  ```

#### File Systems
- **Documentation**: https://intranet.broadinstitute.org/bits/service-catalog/compute-resources/file-systems
- **Folder Destination**: `/stanley/levin/jinlab`

### HPC (Garibaldi)

The HPC file system should be used for any data being analyzed using the high performance clusters. The file system can be accessed through SSH after obtaining a HPC account (email hpc@scripps.edu to create one). 

**Alternative Access**: Use Cyberduck (SFTP, login00.scripps.edu, Scripps username/password) to access from your own device.

**Storage Locations**:
- User folder: `~/`
- Group folder: `/gpfs/group/jin`

**Note**: Although there is no quota on our storage, the storage is shared across Scripps, so maintain only what is absolutely necessary on the clusters.

## Data Transfer Commands

### SCP Examples
```bash
# From Broad to local
scp -r -i ~/.ssh/id_rsa xinjin@xfer.broadinstitute.org:/broad/zhanglabdata/Xin/10x/DATA/septum ./

# From Scripps to local (not working)
scp -r -i ~/.ssh/id_rsa xinjin@login01.scripps.edu:/gpfs/group/jin/Xinhe/10Xanalysis/240627_378RNA/240724count_3/outs/web_summary.html ./
```

### wget Example
```bash
wget -r --mirror --user=xjin --password='3isPUpHAphi7' ftp://igm-storage2.ucsd.edu/220927_M04586_0757_000000000-DHFVT/
```

### rsync Examples
```bash
# Broad to Scripps
rsync -r -v ssh xinjin@xfer.broadinstitute.org:~/broad/zhanglabdata/Xin /gpfs/group/jin/broad_xin/

# Scripps to Broad
rsync -av -P /gpfs/group/jin/Xinhe/10Xanalysis/240627_378RNA/240724count_3/outs/web_summary.html xinjin@xfer.broadinstitute.org:/stanley/levin/jinlab

# From Scripps
rsync -r -v ssh xinjin@login02.scripps.edu:/gpfs/group/jin/Xinhe/10Xanalysis/240627_378RNA/240724count_3/outs/web_summary.html

# With SSH key
rsync -r -v "ssh -i ~/.ssh/id_rsa" xinjin@xfer.broadinstitute.org:/broad/zhanglabdata/Xin /gpfs/group/jin/broad_xin
```

### SFTP Example
```bash
sftp xinjin@xfer.broadinstitute.org
get /broad/zhanglabdata/Xin
```

### Screen Commands
```bash
screen -S name     # start
screen -r name     # reattach
screen -d          # detach earlier screen
```

### Automated Transfer with Password
```bash
sshpass -p "kristinalajolla2021!" scp -r xinjin@xfer.broadinstitute.org:/broad/zhanglabdata/Xin/10x/DATA/septum ./
```

## Google Cloud Storage

### Setup and Transfer
```bash
# Set up account
gcloud auth login

# Copy data
gsutil cp -r gs://fc-f59d69ee-f0ea-4c55-bc8b-9685385d9859/191217septum_mm10v300 ./
gsutil cp -r gs://fc-f59d69ee-f0ea-4c55-bc8b-9685385d9859/201007_sepP5_mm10v300 ./
gsutil cp -r gs://fc-f59d69ee-f0ea-4c55-bc8b-9685385d9859/191218fourcore_mm10v300 ./
gsutil cp -r gs://fc-f59d69ee-f0ea-4c55-bc8b-9685385d9859/200125_PS_sep_mm10v300 ./

# Reference data
gsutil cp gs://regev-lab/resources/cellranger/refdata-cellranger-GRCh38-3.0.0.tar.gz ./
```

## Archival File Systems

Archival file systems can be accessed through CyberDuck on Mac or PC (https://cyberduck.io) or through the HPC.

### StorNext

**HPC Access**: Files can be moved from the HPC through the directory `/stornext/snfs1/disk/jin`. You must login through `login03.scripps.edu` to see the StorNext files.

**CyberDuck Access**: StorNext can be accessed through CyberDuck similarly by connecting to `login03.scripps.edu`.

**Microscopy Computer Access**: StorNext can be mounted onto the microscopy computers by connecting to `\\snfs.scripps.edu\jin`

### Object Storage

#### CyberDuck Access
To access object storage through CyberDuck, add/open a connection with:
- **Type**: Amazon S3
- **Access**: `6cb1bf1d6e5d472c895b92ff59d6c838`
- **Secret**: `aaad7af6bda54277adb07aa747986355`

#### HPC Access
To access object storage through HPC, use the `rclone` command with the directory `Jin`

## Broad Data Access

Files are stored on `datashare.scripps.edu` (access through CyberDuck or Putty)

**Credentials**:
- **Username**: `xinjin`
- **Password**: `P@ss4Broad!`