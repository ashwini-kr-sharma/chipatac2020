# 7. ChIP-seq: Quality control

We can run a number of quality controls for the ChIP-seq dataset; these different quality controls measure several properties of the datasets

* presence of PCR duplicates?
* proportion of the reads contained inside the peaks
* footprinting: enrichment of signal using a Lorez curve


We have previously seen other quality controls related to the quality of the sequencing: read / mapping quality, proportion of duplicates, etc...

We will check some of the metrics which have been developed by the ENCODE consortium; you can check here some of the 
[ENCODE quality standards for transcription factor ChIP-seq](https://www.encodeproject.org/chip-seq/transcription_factor/#standards)

## Presence of PCR duplicates

The **PCR bottleneck coefficient (PBC)** measures the proportion of the genomic position in which a **unique** read starts, compared to the number of positions on which *at least* one read aligns.

We will use the R library `encodeChIPqc`, which implements some of these metrics; this library is available on [this GitHub repository](https://github.com/hdsu-bioquant/encodeChIPqc)

```
> library(encodeChIPqc)
> PCB("data/processed/CTCF/Bowtie2/CTCF_Rep1_ENCFF001HLV_trimmed_aligned_nofilt.bam")
```

> What does the value mean? Is the data of good quality?

Now run

```
> PCB("data/processed/CTCF/Bowtie2/CTCF_Rep1_ENCFF001HLV_trimmed_aligned_filt_sort_nodup.bam")
```

> Do you understand the result?

## Fraction of  reads in  peaks

The **Fraction of reads in  peaks (FRiP)** determines, as the name says, the proportion of reads that fall within the peak regions. The higher this proportion, the more the signal-to-noise  ratio of high, indicating that the signal is concentrated in specific enriched regions. However, this quality metrics depends on the called peaks, hence it might be as well a quality metrics for how well the peaks were called!

We first read the peaks called using MACS2:

```
> peaks =  read.table('analysis/MACS2/CTCF/CTCF_peaks.narrowPeak')
> colnames(peaks) = c('chr','start','end','name','score','strand','signal','pval','qval','peak')
> peaks.gr = makeGRangesFromDataFrame(peaks,keep.extra.columns=TRUE)
> peaks.gr
```

Now we compute the FRiP value:

```
> bam.file = "data/processed/CTCF/Bowtie2/CTCF_Rep1_ENCFF001HLV_trimmed_aligned_filt_sort_nodup.bam"
> frip(bam.file,peaks.gr)
```

> What does this mean? What would be the maximum value?

## Fingerprint

The **fingerprint plot** displays how unevenly the signal is distributed over the genome. A good IP should concentrate a lot of signal in a few enriched regions, a little signal in the background. This distribution is displayed using a *Lorenz curve*. We will use the functions of the package DeepTools. *Beware! this tool is not an R package! So we need to go back to the standard console!

If you are still in the R console (check if you see a `>` as the prompt), you first need to quit R:

```
> quit()
$ 
```

Make sure that you see a `$` sign as a prompt: you are now back in the bash console!

We now run the `plotFingerprint` function from the `DeepTools` package; to limit computational time, we run it only on Chromosome 10:
```
cd
plotFingerprint -b data/processed/CTCF/Bowtie2/CTCF_Rep1_ENCFF001HLV_trimmed_aligned_filt_sort_nodup.bam data/processed/CTCF/Bowtie2/CTCF_Control_ENCFF001HME_trimmed_aligned_filt_sort_nodup.bam -l CTCF Control -o CTCF_fingerprint.png -r chr10
```


## Number of reads and non-redundant reads

The simplest quality control is to check the number of reads, in the unfiltered and the filtered bam files.

```
## Number of reads in the unfiltered bam file
samtools view -c data/processed/CTCF/Bowtie2/CTCF_Rep1_ENCFF001HLV_trimmed_aligned_nofilt.bam

## Number of reads in the filtered bam file (remember how we filtered the file? [check again here](./03_Alignment.md))
samtools view -c data/processed/CTCF/Bowtie2/CTCF_Rep2_ENCFF001HLW_trimmed_aligned_filt_sort_nodup.bam
```

If you take the ratio of these 2 numbers, you will get something like the [non-redundant franction (NFR)](https://www.encodeproject.org/data-standards/terms/#library).


## Exercices
> We have a second replicate of CTCF in the folder `data/processed/CTCF/Bowtie2/`; determine the different quality metrisc for this second replicate; which replicate seems to be better?
