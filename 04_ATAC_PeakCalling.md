# 5. ATAC-seq : Peak Calling

For the ATAC-seq dataset, there are two major differences to the ChIP-seq datasets:

1. we do not have a **control** dataset such as the input for ChIP-seq.
2. the data comes from **paired-end** read sequencing.

> Do keep in mind, most ChIP seq data are single end but of late more paired end data are being generated. ATAC-seq is mostly done in paired-end mode but sometimes you might also encounter single-end ATAC-seq data. 

> Since paired-end data provides an accurate estimation of the fragment length, which in turn allows more accurate peak calling, in principle, paired-end sequencing is the better option.

Regarding the absence of control, we will use the `--nomodel` option in MACS2, which means that MACS2 will build a control from the input BAM file.
Regarding the fact that the data is paired-end, we will use the `--format BAMPE` option to indicate that the BAM file comes from paired-end alignment.

```
# Go to your home directory
cd 

# Create a folder for your analysis
mkdir -p myanalysis/MACS2/ATAC

# Check out all the available parameters in MACS2
# Do note, when in doubt, its often good practice to use default settings
# Most options are optional and set to default, focus on the essential parameters that has to be changed

macs2 --help

macs2 callpeak \
--treatment /vol/volume/HCT116/analysis/ATACseq/ATAC_REP1_aligned_filt_sort_nodup.bam \
--name ATAC-Rep1 \
--format BAMPE \
--nomodel \
--keep-dup all \
--gsize 2.7e9 \
--qvalue 0.05 \
--outdir myanalysis/MACS2/ATAC

```

> Analyze the outputs from MACS2, download the peaks `.xls` files using Cyberduck and check its contents
