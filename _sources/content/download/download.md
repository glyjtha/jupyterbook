# Bulk downloading kriegstein single-cell data

The downloaded data is on the server: 
```
/gpfs/group/jin/herbert/kriegstein
```

## Option 1: Direct wget download

Download from here:
https://data.nemoarchive.org/biccn/grant/u01_devhu/kriegstein/transcriptome/scell/10x_v2/human/processed/counts

The data is complete here.

Use wget command in the terminal:
```bash
wget -nH --no-parent --cut-dirs=10 -r -p -e robots=off -A "*.tar.gz" --include /biccn/lab/kriegstein/transcriptome/scell/10x_v2/human/processed/counts/CS12*/ http://data.nemoarchive.org/biccn/lab/kriegstein/transcriptome/scell/10x_v2/human/processed/counts/
```

## Option 2: Portal client

Use the data portal client: 
https://github.com/IGS/portal_client

**Note:** Not all the data is available. According to nemo, the rest of the data will be added in a few weeks as of 11/09/2021.

### Steps:

1. Go to https://portal.nemoarchive.org/cart and select your data of interest, and then add to cart. You can filter pretty easily for the sc datasets you want. Then add them to your cart.

2. When you view your cart, click the download button --> file manifest.

3. Install portal_client on your computer. The associated github for installation: https://github.com/IGS/portal_client/blob/master/INSTALL.md

4. Run portal_client using the downloaded manifest: https://github.com/IGS/portal_client