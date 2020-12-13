# 6. ATAC-seq : Peak Annotation

You performed peak annotation yesterday for ChIPseq data using [ChIPseeker](http://www.bioconductor.org/packages/release/bioc/vignettes/ChIPseeker/inst/doc/ChIPseeker.html).

> Can you repeat the same analysis using ATACseq peaks ?


## Extra challenge

Often you want to perform integrative analysis - ATACseq + ChIPseq or ATACseq + RNAseq etc. Let try a simple integration approach - 

> Can you find the % overlap between the ATACseq and CTCF peak regions ?

**Hint**
- Read the narrow peak file for CTCF and ATAC in R
- Subset to only standard chromosomes
- Use `length(peak1)` function to count the number of peaks in each
- Use the function `sum(Peaks1 %over% Peaks2)` to count total overlaps between the two peak files
- Use simple arithmetic operations to calculate % overlap


<details>
  <summary>Peek in only if you have to!</summary>
  
```
cd
  
mkdir -p analysis/PeakAnnotation/ATAC

/usr/bin/R

library(ChIPseeker)

## CTCF peak regions
peaks.CTCF =  read.table('analysis/MACS2/CTCF/CTCF_peaks.narrowPeak')
colnames(peaks.CTCF) = c('chr','start','end','name','score','strand','signal','pval','qval','peak')

peaks.CTCF = makeGRangesFromDataFrame(peaks.CTCF, keep.extra.columns=TRUE)
peaks.CTCF = keepStandardChromosomes(peaks.CTCF, pruning.mode="coarse")

peaks.CTCF

## ATAC peak regions
#peaks.ATAC =  read.table('analysis/MACS2/ATAC/CTCF_peaks.narrowPeak')
peaks.ATAC =  read.table('/home/user22/data/processed/ATACseq/MACS2/ATAC-Rep1_peaks.narrowPeak')
colnames(peaks.ATAC) = c('chr','start','end','name','score','strand','signal','pval','qval','peak')

peaks.ATAC = makeGRangesFromDataFrame(peaks.ATAC, keep.extra.columns=TRUE)
peaks.ATAC = keepStandardChromosomes(peaks.ATAC, pruning.mode="coarse")

peaks.ATAC

## Find overlaps

atac_peak_count = length(peaks.ATAC)
overlap_count = sum(peaks.ATAC %over% peaks.CTCF)

overlap_count/atac_peak_count * 100

```

</details>
