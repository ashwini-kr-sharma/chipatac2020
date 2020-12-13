# 6. ATAC-seq : Peak Annotation

You performed peak annotation yesterday for ChIPseq data using [ChIPseeker](http://www.bioconductor.org/packages/release/bioc/vignettes/ChIPseeker/inst/doc/ChIPseeker.html).

> Can you repeat the same analysis using ATACseq peaks ?


## Extra challenge

Often you want to perform integrative analysis - ATACseq + ChIPseq or ATACseq + RNAseq etc. Let try a simple integration approach - 

Similar to how you plotted the ATACseq peak signal around transcription start site (TSS), can you plot the ATACseq peak signal over CTCF peaks, using the CTCF peak results that you have generated.
c.  
<details>
  <summary>Peek in only if you have to!</summary>
```
cd
  
mkdir -p analysis/PeakAnnotation/ATAC

/usr/bin/R

library(ChIPseeker)



## CTCF peak regions
peaks.CTCF =  read.table('analysis/MACS2/CTCF/CTCF_peaks.narrowPeak')
colnames(peaks.CTC) = c('chr','start','end','name','score','strand','signal','pval','qval','peak')
peaks.CTCF = makeGRangesFromDataFrame(peaks.CTCF, keep.extra.columns=TRUE)
peaks.CTCF

## get the reads around the TSS regions
tagMatrix = getTagMatrix(peaks.gr, windows=promoter)

## plot this as density heatmap
pdf('analysis/PeakAnnotation/ChIP/CTCF_heatmap.pdf')
tagHeatmap(tagMatrix, xlim=c(-3000, 3000), color="red")
dev.off()

  
```
</details>
