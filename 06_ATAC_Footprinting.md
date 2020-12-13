# ATAC footprinting TOBIAS

## Tn5 bias correction

```
cd

mkdir -p analysis/Footprint

TOBIAS ATACorrect \
--bam data/processed/ATACseq/Bowtie2/ATAC_REP1_aligned_filt_sort_nodup.bam  \
--genome /vol/volume/HCT116/data/genome.fa \
--peaks analysis/MACS2/ATAC/ATAC-Rep1_peaks.narrowPeak \
--cores 26 \
--outdir analysis/Footprint/

```
## Footprinting scores

```
cd

TOBIAS ScoreBigWig \
--signal analysis/Footprint/ATAC_REP1_aligned_filt_sort_nodup_corrected.bw \
--regions analysis/MACS2/ATAC/ATAC-Rep1_peaks.narrowPeak \
--cores 26 \
--output analysis/Footprint/ATAC_footprints.bw 

```

## Transcription factor binding prediction

```
cd

mkdir -p analysis/Footprint/BINDdetect

TOBIAS BINDetect \
--motifs /vol/volume/HCT116/data/motifs.jaspar \
--signals analysis/Footprint/ATAC_footprints.bw  \
--genome /vol/volume/HCT116/data/genome.fa \
--peaks analysis/MACS2/ATAC/ATAC-Rep1_peaks.narrowPeak \
--cores 26 \
--outdir analysis/Footprint/BINDdetect

```


