# 7. ATAC-seq : Footprinting analysis using [TOBIAS](https://github.com/loosolab/TOBIAS)

## Tn5 bias correction

```
cd

mkdir -p analysis/Footprint

TOBIAS ATACorrect \
--bam data/processed/ATACseq/Bowtie2/ATAC_REP1_aligned_filt_sort_nodup.bam  \
--genome data/ext_data/genome.fa \
--peaks analysis/MACS2/ATAC/ATAC-Rep1_peaks.narrowPeak \
--cores 3 \
--outdir analysis/Footprint/

```
## Footprinting scores

```
cd

TOBIAS ScoreBigWig \
--signal analysis/Footprint/ATAC_REP1_aligned_filt_sort_nodup_corrected.bw \
--regions analysis/MACS2/ATAC/ATAC-Rep1_peaks.narrowPeak \
--cores 3 \
--output analysis/Footprint/ATAC_footprints.bw 

## delete some files
rm *corrected.bw *expected.bw *bias.bw
```

## Transcription factor binding prediction

```
cd

mkdir -p analysis/Footprint/BINDdetect

TOBIAS BINDetect \
--motifs data/ext_data/motifs.jaspar \
--signals analysis/Footprint/ATAC_footprints.bw  \
--genome data/ext_data/genome.fa \
--peaks analysis/MACS2/ATAC/ATAC-Rep1_peaks.narrowPeak \
--cores 3 \
--outdir analysis/Footprint/BINDdetect

```
