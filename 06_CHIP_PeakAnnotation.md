# 6. ChIP-seq: Peak annotation

One we have determined the peaks for the ChIP-seq and ATAC-seq data, we can use them to

1. perform some quality controls: in particular, we can determine the **fraction of reads in peaks**, i.e. the proportion of all reads that fall into peak regions. The higher this proportion, the higher the signal-to-noise ration is.
2. annotate the peaks to neighboring genes and genomics regions (such as promoters / introns / intergenic / ...)

In order to do so, we will use a package in R called [ChIPseeker](http://www.bioconductor.org/packages/release/bioc/vignettes/ChIPseeker/inst/doc/ChIPseeker.html); this package can perform a number of annotation tasks and return a list of annotated peaks.



## Running ChIPseeker annotation

In the console, open an R session by simply typing 

```
cd

mkdir -p analysis/PeakAnnotation/ChIP

/usr/bin/R
```

This will open an R session, which you can recognize seing the `>` prompt at the left.

We will load the required packages:

```
library(ChIPseeker)
library(TxDb.Hsapiens.UCSC.hg38.knownGene)
txdb = TxDb.Hsapiens.UCSC.hg38.knownGene
```

Now, let's read in the list of peaks for the CTCF dataset

```
peaks =  read.table('analysis/MACS2/CTCF/CTCF_peaks.narrowPeak')
colnames(peaks) = c('chr','start','end','name','score','strand','signal','pval','qval','peak')
peaks.gr = makeGRangesFromDataFrame(peaks,keep.extra.columns=TRUE)
peaks.gr
```

We can briefly check how many peaks map to each chromosome:

```
table(as.character(seqnames(peaks.gr)))
```

As you see, some peaks map to incomplete chromosomes (like `chr1_KI270711v1_random`); we will remove these few peaks, and keep only those mapping to the 23 chromosomes:

```
peaks.gr = keepStandardChromosomes(peaks.gr, pruning.mode="coarse")
```

> Rerun the command with `table` to check that we only have peaks on the 23 chromosomes.

## Genomic coverage of peaks

Let us see how the peaks are distributed over the genome:

```
pdf('analysis/PeakAnnotation/ChIP/CTCF_coverage.pdf')
covplot(peaks.gr,weightCol='signal')
dev.off()
```

Using CyberDuck, you can browse your home folder on the VM; you should see the file `CTCF_coverage.pdf`; open the file and look at the genomic distribution of the peaks.

## Tag density around TSS

As a quality control, we can check the read density on the transcription start site of the genes. Let's do this for the CTCF ChIP-seq:

```
## define promoter regions as +/- 3kb
promoter = getPromoters(TxDb=txdb, upstream=3000, downstream=3000)

## get the reads around the TSS regions
tagMatrix = getTagMatrix(peaks.gr, windows=promoter)

## plot this as density heatmap
pdf('analysis/PeakAnnotation/ChIP/CTCF_heatmap.pdf')
tagHeatmap(tagMatrix, xlim=c(-3000, 3000), color="red")
dev.off()
```

Check the figure `CTCF_heatmap.pdf` in your  home folder using CyberDuck, and open this file.

## Annotating peaks to genes

An important analysis is to assign the peaks to the nearest gene(s).

```
peakAnno  = annotatePeak(peaks.gr,tssRegion=c(-3000,3000), TxDb=txdb, annoDb="org.Hs.eg.db")
peak.anno = as.data.frame(peakAnno)
head(peak.anno)
```




The `peak.anno` object is a data table with multiple columns; each row represents a peak, and the subsequent columns represent different annotations:

* `annotation`: indicates the genomic loci in which the peak is located
* `distanceToTSS`: distance to the closest TSS
* `SYMBOL` : symbol of the closest gene


We can write the file to your folder:

```r
write.table(peak.anno,file='analysis/PeakAnnotation/ChIP/CTCF_peaks.anno.csv',row.names=FALSE,quote=FALSE,sep='\t')
```

We can check the distribution of the peak location:

```
pdf('analysis/PeakAnnotation/ChIP/CTCF_distribution.pdf')
plotAnnoPie(peakAnno)
dev.off()
```

Check the corresponding figure!

Finally, we can use the genes which have been associated to the peaks to compute the enrichment of certain pathways or biological functions:

```
library(ReactomePA)
library(ggplot2)

## get all genes which have a peak within +/- 1 kb around TSS
gene = seq2gene(peaks.gr, tssRegion = c(-1000, 1000), flankDistance = 3000, TxDb=txdb)
pathway = enrichPathway(gene)
dp = dotplot(pathway)
ggsave(filename = "analysis/PeakAnnotation/ChIP/CTCF_pathways.pdf", plot = dp)
```

Again, you can check the plot, and see which pathways seem to be enriched among the genes which have a proximal CTCF peak.


> Try to redo the full peak annotation using the H3K4me3 peaks!
