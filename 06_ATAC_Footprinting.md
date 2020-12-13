# ATAC footprinting TOBIAS

## Tn5 bias correction

```
cd

mkdir -p myanalysis/Footprint

TOBIAS ATACorrect \
--bam /vol/volume/HCT116/analysis/ATACseq/Bowtie2/ATAC_REP1_aligned_filt_sort_nodup.bam  \
--genome /vol/volume/HCT116/data/genome.fa \
--peaks myanalysis/MACS2/ATAC/ATAC-Rep1_peaks.narrowPeak \
--cores 26 \
--outdir myanalysis/Footprint/

```
## Footprinting scores

```
cd

TOBIAS ScoreBigWig \
--signal myanalysis/Footprint/ATAC_REP1_aligned_filt_sort_nodup_corrected.bw \
--regions myanalysis/MACS2/ATAC/ATAC-Rep1_peaks.narrowPeak \
--cores 26 \
--output myanalysis/Footprint/ATAC_footprints.bw 

```

## Transcription factor binding prediction

```
cd

mkdir -p myanalysis/Footprint/BINDdetect

TOBIAS BINDetect \
--motifs /vol/volume/HCT116/data/motifs.jaspar \
--signals myanalysis/Footprint/ATAC_footprints.bw  \
--genome /vol/volume/HCT116/data/genome.fa \
--peaks myanalysis/MACS2/ATAC/ATAC-Rep1_peaks.narrowPeak \
--cores 26 \
--outdir myanalysis/Footprint/BINDdetect

```


