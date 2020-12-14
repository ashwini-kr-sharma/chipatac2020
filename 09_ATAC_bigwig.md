# 9. ATAC-seq : ATAC signal file

In a final step, we will generate *signal files* in the `bigwig` format, which represent continuous signals along the genome. We will use the function [`bamCoverage`](https://deeptools.readthedocs.io/en/develop/content/tools/bamCoverage.html) from **`deepTools`** to acheive this.

```
cd 

mkdir -p analysis/bigwig/ATAC

bamCoverage \
--bam data/processed/ATACseq/Bowtie2/ATAC_REP1_aligned_filt_sort_nodup.bam \
--outFileName analysis/bigwig/ATAC/ATAC_REP1.bw \
--outFileFormat bigwig \
--normalizeUsing RPKM \
--ignoreDuplicates --centerReads \
--binSize 200 \
--numberOfProcessors 3

```

Look at the extra parameters from the [`bamCoverage`](https://deeptools.readthedocs.io/en/develop/content/tools/bamCoverage.html), that you could possibly incorporate.


## Extra challenge

> Like before, visualize your ATAC `bigwig` file in the [IGV web app](https://igv.org/app/). 
</br>However, try visualizing both ATAC and CTCF `bigwig` together with the corresponding MACS2 peak files. Can you find regions (like near gene promoters) where they overlap? What does this tell you biologically ?
