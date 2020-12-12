# ATAC QC

## Library complexity

The library complexity metrics used for ChIPseq like  Non-Redundant Fraction and PCR Bottlenecking Coefficients are also relavant for ATACseq. 

> Can you repeat these analysis by adapting the code used for ChIPseq yesterday ? Evaluate your results based on the [`ENCODE recommendations`](https://www.encodeproject.org/atac-seq/) for ATACseq experiments. 

## Mitochondrial content

Since the Mitochondria DNA (MT) is free of chromatin packaging, it will be more accessible to the Tn5 transposoon, as a result of which a lot of the reads will be from the MT (in some cell types > 50%). A critical step in ATAseq analysis is to remove MT mapping reads and to estimate what fraction of your reads map to MT.

Howeve, do note that newer ATACseq protocols like [Omni-ATAC](https://www.nature.com/articles/nmeth.4396) solves this problems considerably and will very likely become the standard. MT contatmination should no longer be an issue in newer datasets following these protocols.

We will use the **samtools** `idxstats` function to count the number of reads mapped to each chromosome. Based on these values we will calculate %MT contamination. Ideally we would want to see this value as low as possible.

According to [Samtools](http://www.htslib.org/doc/samtools-idxstats.html), the `idxstats` output are -

 - Coulumn 1 - reference sequence name
 - Column 2 - sequence length
 - Coulumn 3 - Number of mapped read-segments 
 - Column 4 - Number of unmapped read-segments

```
cd

# See the outputs
samtools idxstats /vol/volume/HCT116/analysis/ATACseq/Bowtie2/ATAC_REP1_aligned_nofilt.bam

# Extract number of mapped read-segments for MT
samtools idxstats /vol/volume/HCT116/analysis/ATACseq/Bowtie2/ATAC_REP1_aligned_nofilt.bam | grep 'chrM' | cut -f 3

# Sum up number of mapped read-segments for all chromosomes
samtools idxstats /vol/volume/HCT116/analysis/ATACseq/Bowtie2/ATAC_REP1_aligned_nofilt.bam | awk '{SUM += $3} END {print SUM}'

# Compute the %MT content
calc 5087398/77784521*100

```

# Nucleosome positioning

We know that chromatin consists of repeated wrapping of 146bp of DNA around histone octamer (nucleosome) followed by 80bp of linker open/free DNA. The Tn5 transposoon will insert adapters in these linker DNA region. Thus, genome wide we will have increased probability of observing fragments of ~80bp, ~200bp, ~400bp, ~600bp representing nucleosome free regions, mono-, di-, tri- nucleosomes.

Observing such periodicity from the fragment lenth distribution is a good indicator of a successful experiment. In the next step we will perform this analysis to assess the quality of the ATACseq data.

```

cd

/usr/bin/R

# Now you are inside the R console !!

library(ATACseqQC)

fragSize <- fragSizeDist(bamFiles = "/vol/volume/HCT116/analysis/ATACseq/Bowtie2/ATAC_REP1_aligned_filt_sort_nodup.bam", 
index = "/vol/volume/HCT116/analysis/ATACseq/Bowtie2/ATAC_REP1_aligned_filt_sort_nodup.bam",         
bamFiles.labels = "ATAC")

q()

# type N

```

There will be a `.pdf` file nameed `Rplots.pdf` in your home directory - type `cd` in your terminal, open this file using Cyberduck and analyse the results.
