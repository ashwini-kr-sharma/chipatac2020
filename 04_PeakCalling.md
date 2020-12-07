# ChIPseq

# Narrow CTCF peak

```
# Go to your home directory
cd ~/

# Create a folder for your analysis
mkdir -p myanalysis/MACS2

# Docker image for MACS2
docker pull fooliu/macs2:latest

docker run \
-v /vol/volume/HCT116/:/data/ \
-v /home/sharma/myanalysis/:/data/results \
fooliu/macs2:latest callpeak \
--treatment /data/analysis/CTCF/Bowtie2/CTCF_Rep1_ENCFF001HLV_trimmed_bowtie2_sorted_filt_nodup.bam \
--control /data/analysis/CTCF/Bowtie2/CTCF_Control_ENCFF001HME_trimmed_bowtie2_sorted_filt_nodup.bam \
--name CTCF \
--format BAM \
--keep-dup all \
--gsize 2.7e9 \
--qvalue 0.01 \
--bdg \
--outdir /data/results/MACS2
```

# Broad H3K4me1 peak

```
docker run \
-v /vol/volume/HCT116/:/data/ \
-v /home/sharma/myanalysis/:/data/results \
fooliu/macs2:latest callpeak \
--treatment /data/analysis/H3K4me1/Bowtie2/H3K4me1_Rep1_ENCFF000VCI_aligned_filt_sort_nodup.bam \
--control /data/analysis/H3K4me1/Bowtie2/H3K4me1_Control_ENCFF000VCW_aligned_filt_sort_nodup.bam \
--name CTCF \
--format BAM \
--keep-dup all \
--gsize 2.7e9 \
--broad \
--qvalue 0.05 \
--broad-cutoff 0.05 \
--bdg \
--outdir /data/results/MACS2

```

# ATACseq peaks (Paired end)

```
docker run \
-v /vol/volume/HCT116/:/data/ \
-v /home/sharma/myanalysis/:/data/results \
fooliu/macs2:latest callpeak \
--treatment /data/analysis/ATAC/ATAC_REP1_aligned_filt_sort_nodup.bam \
--name ATAC-Rep1 \
--format BAMPE \
--nomodel \
--keep-dup all \
--gsize 2.7e9 \
--qvalue 0.05 \
--bdg \
--outdir /data/results/MACS2


```
