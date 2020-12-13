# 4. ChIP-seq : Read alignment

Aligning the sequenced reads to the reference genome is the most crucial task of any NGS analysis. Fast aligners like [Bowtie2](http://bowtie-bio.sourceforge.net/bowtie2/manual.shtml#using-samtoolsbcftools-downstream) and [BWA-MEM](https://github.com/bwa-mem2/bwa-mem2) are widely used for aligning, among others, ChIPSeq and ATACseq data. We will exemplarily show how to align your sequencing reads using **Bowtie2**

Due to the long computation time and memory constraints, we have precomputed for you all the alignments using **Bowtie2**. You find these data in `/vol/volume/HCT116/analysis/<CTCF/H3K4me3>/Bowtie2`

## Pseudocode for alignment

```
#------------------------------------------
# Read alignment with Bowtie2 - Single end
#------------------------------------------

bowtie2 \
--phred33 \
--mm \
--very-sensitive \
--threads 10 \
-x <hg38 genome index> \
-U <input fastq> \
2> \
<bowtie2.log> \
| samtools view -h -b - > <aligned.bam>

#--------------------------------------------
```

## Pseudocode for filtering poor quality and unmapped reads

After the alignment, some read have a low mapping quality (possibly with many mismatches); this mapping quality is indicated by the [**MAPQ score**](https://genome.sph.umich.edu/wiki/Mapping_Quality_Scores). This score takes into account the number of mismatches, but also the quality of the sequenced bases, as indicated by the Phred score.

Here, we will:

1. filter out unmapped reads
2. remove reads which are aligned, but with an alignment quality below MAPQ=30

```
#----------------------------
# Low quality read filtering
#----------------------------

# For Single end
samtools view -h -b -F 4 -q 30 -@ 10 -o <aligned_filtered.bam> <aligned.bam> 

```

## Pseudocode for duplicate removal

A typical problem in libraries sequenced using a PCR amplification is the presence of PCR duplicates among the reads. 
These should be filtered out, as they do not bring any additional insights. Usually, these duplicates can be identifiied when many reads have the exact same coordinates. We will delete these duplicates from the bam file using the `samtools markdup` tool.

```
#---------------------------------------
# Sorting, Fixmate and Duplicate removal
#---------------------------------------

# NOTE: fixmate needs bam files sorted by read name but
# the downstream processes need co-ordinate sorted bam files, hence the two sorting steps
# Fixmate is needed to add mate tags (-m), which is used for proper duplicate identification by markdup

samtools sort -n -O BAM -@ 10 <aligned_filtered.bam>  \
| samtools fixmate -m -@ 10 - - \
| samtools sort -O BAM -@ 10 - > \
| samtools markdup -r -S -@ 10 - <aligned_filtered_sorted_duprmv.bam>
```

## Pseudocode for indexing

Finally, now that we have generated a new bam file by filtering out reads, we need to re-index the bam file. Indexing helps in quickly accessing the reads.

```
#----------
# Indexing
#----------

samtools index <aligned_filtered_sorted_duprmv.bam> <aligned_filtered_sorted_duprmv.bam.bam.bai>

```

A detailed explaination of the parameters used for alignment can be found [here](http://bowtie-bio.sourceforge.net/bowtie2/manual.shtml#using-samtoolsbcftools-downstream), it is important to notice the difference for paired end and single end sequencing.

After alignment, we also filter out poor quality, unmapped and duplicate reads using [samtools](http://www.htslib.org/doc/samtools.html). The parameters `-F, -f, q` along with `markdup` are used to filter out reads. 

> Using this [tool](https://broadinstitute.github.io/picard/explain-flags.html), one can find the exact meaning of the filtering flags like `1796, 1804, 2, etc`.

# Alignment statistics

**SAMtools** provides some functions like `flagstat, stats and idxstats` to compute overall summary of read alignment statistics. You can find more information on the **flagstat** [here](http://www.htslib.org/doc/samtools-flagstat.html)

```
# Go to your home directory
cd 

# Create a folder for your analysis
mkdir -p myanalysis/AlignmentStats

# Flagstat analysis

# Pseudocode: 
# samtools flagstat <aligned.bam> > <aligned.bam.log>
# samtools flagstat <aligned_filtered_sorted_duprmv.bam> > <aligned_filtered_sorted_duprmv.bam.log> 

samtools flagstat /vol/volume/HCT116/analysis/CTCF/Bowtie2/CTCF_Rep2_ENCFF001HLW_trimmed_aligned_nofilt.bam > \
myanalysis/AlignmentStats/Bowtie2/CTCF_Rep2_ENCFF001HLW_trimmed_aligned_nofilt.bam.flagstat.log

```

 > Can you modify the code above to run flagstat also on the corresponding **filtered and unfiltered** `.bam` and compare the numbers from the two files. What do you learn ?
 
<details>
  <summary> Peek only if you have to ! </summary>
 
```
samtools flagstat /vol/volume/HCT116/analysis/CTCF/Bowtie2/CTCF_Rep2_ENCFF001HLW_trimmed_aligned_filt_sort_nodup.bam > \ 
myanalysis/AlignmentStats/CTCF_Rep2_filter.flagstat.log

```
 
</details>
 
 > Look at the duplicates field in both files. What does it say? This might be MISLEADING !! see next section why ?
 
 
 # Counting duplicates
 
 The aligned `.bam` files output of `Bowtie2` does not have duplicate reads marked by any Flags recognized by `Samtools`. Thus, the `flasgstat` output might give an impression that there are no duplicates in your data and that you don't need to perform duplicate removal. However, this is highly misleading !!.
 
 These aligned files has to be -
 
 1. Sorted by read names  (because the Fixmate step only recognizes this form of sorting)
 2. Mate pairing identified by fixmate
 3. Re-sorted by co-ordinates (because most downstream methods only work on co-ordinated sorted alignment files)
 4. Identify duplicated using `markdup`
 
 We will use one such unfiltered alignment file output from `Bowtie2`, perform the steps above and using `flagstat` again count the number of duplicates in our data.
 
 ```
 cd
 
 samtools sort -O BAM -n -@ 3 /vol/volume/HCT116/analysis/CTCF/Bowtie2/CTCF_Rep2_ENCFF001HLW_trimmed_aligned_nofilt.bam \
| samtools fixmate -m -@ 3 - - \
| samtools sort -O BAM -@ 3 \
| samtools markdup -@ 3 - - \
| samtools flagstat - > myanalysis/AlignmentStats/CTCF_Rep2_nofilter_dupmarked_flagstat.log

 ```

> Compare this `flagstat` output with the alignment statistis identified in the previous section. Do you notice a difference

In the next section, we will perform **peak calling** using these aligned `.bam` files.
