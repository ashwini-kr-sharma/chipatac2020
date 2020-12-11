# ATAC footprinting TOBIAS


```
cd

mkdir -p myanalysis/Footprint

TOBIAS ATACorrect \
--bam /vol/volume/HCT116/analysis/ATACseq/Bowtie2/ATAC_REP1_aligned_filt_sort_nodup.bam \
--genome /vol/volume/HCT116/data/genome.fa \
--peaks myanalysis/MACS2/ATAC/ATAC-Rep1_summits.bed \
--cores 8 \
--outdir myanalysis/Footprint/

```

```
TOBIAS FootprintScores \
--signal test_data/Bcell_corrected.bw \
--regions test_data/merged_peaks.bed \
--cores 8 \
--output Bcell_footprints.bw 

```

```
TOBIAS BINDetect \
--motifs test_data/motifs.jaspar \
--signals test_data/Bcell_footprints.bw \
--genome /vol/volume/HCT116/data/ATACseq/genome.fa \
--peaks test_data/merged_peaks_annotated.bed \
--peak_header test_data/merged_peaks_annotated_header.txt \
--cond_names ATAC \
--cores 8 \
--outdir BINDetect_single_output 
```
