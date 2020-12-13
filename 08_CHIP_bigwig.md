# 8. ChIP-seq : Generating signal files

So far, we have worked with the following files

* `fastq` files, which represent raw reads;
* `bam` files, which represent aligned reads;
* `bed` or  `narrowPeak` files, which represent discrete  genomic regions containing signal.

In a final step, we will generate *signal files* in the `bigwig` format, which represent continuous signals along the genome. We will use the function [`bamCoverage`](https://deeptools.readthedocs.io/en/develop/content/tools/bamCoverage.html) from **`deepTools`** to acheive this, the files can then be loaded into a genomic browser in order to explore the signal and the peak regions.

For ChIP-seq, we have the **IP** file representing the signal, and the **control** file (generally input), which represents the level of background noise. When generating the signal file, we can (1) either generate separate bigwig files for IP and control, or (2) generate a single bigwig file, in which we substract the background noise from the IP.

## Generating separate bigwig

We will use a tool form the `bedtools` suite, namely `genomecov`, to build the bigwig files.

```
cd

## create a directory
mkdir -p analysis/bigwig/ChIP

bamCoverage \
--bam data/processed/CTCF/Bowtie2/CTCF_Rep1_ENCFF001HLV_trimmed_aligned_filt_sort_nodup.bam \
--outFileName analysis/bigwig/ChIP/CTCF_Rep1.bw \
--outFileFormat bigwig \
--normalizeUsing RPKM \
--ignoreDuplicates --centerReads \
--binSize 200 \
--numberOfProcessors 4

```

### Exercice

> Create the bigwig file for the control `data/processed/CTCF/Bowtie2/CTCF_Control_ENCFF001HME_trimmed_aligned_filt_sort_nodup.bam`<br></br> MAKE SURE TO GIVE A DIFFERENT NAME TO THE OUTPUT FILES!!

## Generating a merged bigwig file

We can also create a single bigwig file by subtracting the Control from the IP; scaling Control and IP is highly non-trivial, a good method is the **SES** method implemented in DeepTools

```
bamCompare -b1 data/processed/CTCF/Bowtie2/CTCF_Rep1_ENCFF001HLV_trimmed_aligned_filt_sort_nodup.bam \
 -b2 data/processed/CTCF/Bowtie2/CTCF_Control_ENCFF001HME_trimmed_aligned_filt_sort_nodup.bam \
 --scaleFactorsMethod SES -p 3 \
 --operation subtract \
  -o analysis/bigwig/ChIP/CTCF_ses_subtract.bw
```

## Loading the files into the IGV app       

We can now use the [IGV web app](https://igv.org/app/) to visualize the different datasets into a genomic browser; here, we will load
* the individual bigwig files for IP and control
* the combined bigwig file
* the peak files in narrowPeak format

**Procedure**

1. go to to the [IGV web app](https://igv.org/app/)
2. in the top left menu *Genome*, select the `hg 38` genome version
3. using Cyberduck, download the following files onto your local computer
   * `CTCF_ses_subtract.bw`
   * `CTCF_Rep1.bw`
   * `CTCF_peak.narrowPeak` (should be in the `analysis/MACS2/CTCF` folder!)
4. in the IGV app, go to `Tracks > local files...` and load the bigwig and narrowPeak file
5. you can now select a specific chromosome in the drop-down menu, or type in a gene name to see this gene locus.


> Do the called peaks correspond to regions of higher signal in the bigwig tracks?

> Choose two peaks in the bigwig file which seem to have different height; click on the corresponding peak in the narrowPeak track (blue rectangle) to see the P-values and Q-values; do you see a difference?
