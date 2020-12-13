# ATAC footprinting TOBIAS


```
cd

mkdir -p myanalysis/Footprint

TOBIAS ATACorrect \
--bam /vol/volume/HCT116/analysis/ATACseq/Bowtie2/ATAC_REP1_aligned_filt_sort_nodup.bam \
--genome /vol/volume/HCT116/data/genome.fa \
--peaks myanalysis/MACS2/ATAC/ATAC-Rep1_summits.bed \
--cores 3 \
--outdir myanalysis/Footprint

```

```
cd

TOBIAS FootprintScores \
--signal myanalysis/Footprint/ATAC_REP1_aligned_filt_sort_nodup_corrected.bw \
--regions myanalysis/MACS2/ATAC/ATAC-Rep1_summits.bed \
--cores 3 \
--output myanalysis/Footprint/ATAC_footprints.bw 

```

```
cd

mkdir -p myanalysis/Footprint/BINDdetect

TOBIAS BINDetect \
--motifs /vol/volume/HCT116/data/MA0139.1.jaspar \
--signals myanalysis/Footprint/ATAC_footprints.bw  \
--genome /vol/volume/HCT116/data/genome.fa \
--peaks myanalysis/MACS2/ATAC/ATAC-Rep1_summits.bed \
--cores 26 \
--outdir myanalysis/Footprint/BINDdetect

```
