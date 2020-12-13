# 9. ATAC-seq : ATAC signal file

In a final step, we will generate *signal files* in the `bigwig` format, which represent continuous signals along the genome. Another format to do this is the `bedgraph` format. For both formats, the files can then be loaded into a genomic browser in order to explore the signal and the peak regions.

```
cd 

mkdir -p analysis/bigwig

bamCoverage \
--bam data/processed/ATACseq/Bowtie2/ATAC_REP1_aligned_filt_sort_nodup.bam \
--outFileName analysis/bigwig/ATAC_REP1.bw \
--outFileFormat bigwig \
--normalizeUsing RPKM \
--ignoreDuplicates --centerReads \
--binSize 200 \
--numberOfProcessors 8

```

# Extra challenge

Like before, visualize your ATAC `bigwig` file in the [IGV web app](https://igv.org/app/). However, try visualizing both ATAC and CTCF `bigwig` together with the corresponding MACS2 peak files. Can you find regions (like near gene promoters) where they overlap? What does this tell you biologically ?
