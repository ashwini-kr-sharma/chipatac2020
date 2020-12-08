# ChIPseq

## Narrow CTCF peak

```
# Go to your home directory
cd 

# Create a folder for your analysis
mkdir -p myanalysis/MACS2


# Launch the MACS2 peak calling
macs2 callpeak \
--treatment /vol/volume/HCT116/analysis/CTCF/Bowtie2/CTCF_Rep1_ENCFF001HLV_trimmed_aligned_filt_sort_nodup.bam \
--control /vol/volume/HCT116/analysis/CTCF/Bowtie2/CTCF_Control_ENCFF001HME_trimmed_aligned_filt_sort_nodup.bam \
--name CTCF \
--format BAM \
--keep-dup all \
--gsize 2.7e9 \
--qvalue 0.01 \
--outdir myanalysis/MACS2/CTCF
```

## Broad H3K4me3 peak

```

macs2 callpeak \
--treatment /vol/volume/HCT116/analysis/H3K4me3/Bowtie2/H3K4me3_Rep1_ENCFF000VCI_aligned_filt_sort_nodup.bam \
--control /vol/volume/HCT116/analysis/H3K4me3/Bowtie2/H3K4me3_Control_ENCFF000VCW_aligned_filt_sort_nodup.bam \
--name H3K4me3 \
--format BAM \
--keep-dup all \
--gsize 2.7e9 \
--broad \
--qvalue 0.05 \
--broad-cutoff 0.05 \
--outdir myanalysis/MACS2/H3K4me3

```

# ATACseq peaks (Paired end)

For the ATAC-seq dataset, there are two major differences to the ChIP-seq datasets:

1. we do not have a control dataset such as the input for ChIP-seq.
2. the data comes from paired-end read sequencing.

Regarding the absence of control, we will use the `--nomodel` option in MACS2, which means that MACS2 will build a control from the input BAM file.
Regarding the fact that the data is paired-end, we will use the `--format BAMPE` option to indicate that the BAM file comes from paired-end alignment.

```
macs2 callpeak \
--treatment /vol/volume/HCT116/analysis/ATAC/ATAC_REP1_aligned_filt_sort_nodup.bam \
--name ATAC-Rep1 \
--format BAMPE \
--nomodel \
--keep-dup all \
--gsize 2.7e9 \
--qvalue 0.05 \
--outdir myanalysis/MACS2/ATAC

```
