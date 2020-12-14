# 7. ATAC-seq : Footprinting analysis using [TOBIAS](https://github.com/loosolab/TOBIAS)

## Tn5 bias correction

![ATAC correct](./atacorrect.png)
*Figure taken from [TOBIAS](https://github.com/loosolab/TOBIAS)*

The function `ATACCorrect` from **TOBIAS** is used for the following reasons -

- During ATACseq libtrary preparation, the Tn5 transposase binds to the open chromatin as a dimer and inserts two adapters separated by 9 bps. This has no major effect in peak calling, however, for footprinting analysis, the + and - strand are shifted by +4 bps and −5 bps repectively see [this](https://dx.doi.org/10.1038%2Fnmeth.2688) and [this](https://www.biostars.org/p/476698/). Doing so helps identify the center of the Tn5 binding.

- Though we expect Tn5 to bind randomly in open regions, in reality, Tn5 has a binding bias towards specific sequence. Thus, within a given open chromatin region, it's more likely to find the Tn5 cutsites over these specific sequences. This inherent bias needs to be corrected to identify the cutsites accurately. The function calculates the difference between the observed vs expected cutsites to correct for this bias. 

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

![Footprinting](./footprinting.png)
*Figure taken from [TOBIAS](https://github.com/loosolab/TOBIAS)*

Using the Tn5 bias corrected signals, this function estimates the footprints (i.e putative TF binding sites) withing peak regions by identifying regions with depleted cutsite signal and flanked by high cutsite signal. Such a signal pattern indicates regions of TF binding.

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

![TF binding](./bindetect.png)
*Figure taken from [TOBIAS](https://github.com/loosolab/TOBIAS)*

Once the footprint regions are identified, one could look at the sequnce at those regions and compare that with the TF binding motif sequence to estimate the TF binding propensity at the footprint regions. TF binding motifs are provided in standard JASPER format.

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
