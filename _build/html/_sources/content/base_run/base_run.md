# Basespace Run Creation Guide

**For all genomic core runs after December 2024**

If set up properly, your sequencing results will be uploaded directly from the sequencer to Basespace. It will also run BCL Convert, which is Illumina's bcl2fastq to demultiplex your samples. The outs are compatible with Cell Ranger and other pipelines. The BCL files will also be available for users to demultiplex using other tools if needed.

## Important Notes

- We have a basic account with only **1TB of storage.**
- BaseSpace Sequence Hub may **automatically delete data** if storage exceeds 1 TB for more than 30 days.
- Please immediately copy your data to the server and delete it off Basespace when your run is complete.
- The Basespace web portal does not allow run creation without demultiplexing to my knowledge. Only local creation of a run (on the machine), can do this. If this is an issue, contact the core to figure out a workaround.

## Setup Steps

Please follow these steps to set up a run:

1. Email the core to set up a slot for your run and fill out an Elan request. You do not need to send them a sample sheet.

2. Log into basespace. Login is `jinoffice@scripps.edu`. PW is `Lajolla2021!`

3. Click "Run" -> "New Run" -> "Run Planning".

4. Enter a **unique** run name (`Runnumber_Boli_VeryGood_SequencingRun`).
   > John will assign a unique run number, e.g. `ca_ns_0495_20250225`

5. Choose your sequencing instrument (NextSeq 1000/2000 usually).

6. Choose "Basespace" for secondary analysis.
![Screenshot1](../image/image1.png)


7. Click "Next".

8. On the configuration page, choose "Dragen BCL Convert 3.10.4". If you are unsure about this, **please check with the core to see what version of BCL Convert they have installed on their machine.**

9. Choose the appropriate library prep kit and indexes. It is **VERY** important that this information is correct, as changing this after your run is complete is extremely difficult.

10. (Optional): Custom configurations can be set up by clicking on Xin Jin (top) -> Resources -> New Library Prep Kit
![Screenshot2](../image/image2.png)

11. Click "Next".

12. If you chose a predefined library type, the Read Lengths on the next page should be filled in automatically.

13. (Optional): If using a custom library, the Override Cycles needs to be filled in as well. The 10X 5' Dual Index for example has the following values as a guide:
    - Read 1: U26 - cell barcode and UMI, indicated by "U"
    - Index 1: I10 - "I" for index
    - Index 2: I10 - "I" for index
    - Read 2: Y90 - RNA read, indicated by "Y"

14. Add your sample information. If you have a predefined adapter kit (like 10X TT dual indexes), just input your sample name and what well you chose, it will fill in the rest.

15. For predefined libraries, you can leave the adapter trimming blank, the software already has this.

16. Change the barcode mismatch section to 1 (this is the default, but just to be sure).
![Screenshot3](../image/image3.png)

17. Click "Next" to review your run and then "Save as Planned".
![Screenshot4](../image/image4.png)


18. Email the core and give them the EXACT Run Name and Cycles so they can find it.

19. Drop off your samples/kit according to their instructions. Your data will appear in Basespace when the run is completed.
![Screenshot5](../image/image5.png)
