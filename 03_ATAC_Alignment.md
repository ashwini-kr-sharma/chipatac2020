# Read alignment

Aligning the sequenced reads to the reference genome is the most crucial task of any NGS analysis. Fast aligners like [Bowtie2](http://bowtie-bio.sourceforge.net/bowtie2/manual.shtml#using-samtoolsbcftools-downstream) and [BWA-MEM](https://github.com/bwa-mem2/bwa-mem2) are widely used for alignment, among others, ChIPSeq and ATACseq data. We will exemplarily show how to align reads using **Bowtie2**

**Due to the long computation time and memory constraints, we have precomputed for you all the alignments using **Bowtie2**. You find these data in `data/processed/ATACseq/Bowtie2`**

## Pseudocode for alignment

```
#------------------------------------------
# Read alignment with Bowtie2 - Paired end
#------------------------------------------

bowtie2 \
--phred33 \
--mm \
--maxins 2000 \
--very-sensitive \
--threads 10 \
-x <hg38 genome index> \
-1 <A_R1.fastq.gz> \
-2 <A_R2.fastq.gz> \
2> \
<bowtie2.log> \
| samtools view -h -b - > <aligned.bam>

#--------------------------------------------
```

## Pseudocode for filtering

After the alignment, some read have a low mapping quality (possibly with many mismatches); this mapping quality is indicated by the [**MAPQ score**](https://genome.sph.umich.edu/wiki/Mapping_Quality_Scores). This score takes into account the number of mismatches, but also the quality of the sequenced bases, as indicated by the Phred score.

Here, we will:

1. filter out unmapped reads
2. remove reads which are aligned, but with an alignment quality below MAPQ=30

```
#----------------------------
# Low quality read filtering
#----------------------------

# For paired end
samtools view -h -b -F 1804 -f 2 -q 30 -@ 10 -o <aligned_filtered.bam> <aligned.bam> 

#------------------------------------------------------------
# NOTE:[Only for ATACseq] - Filtering Mitochondrial reads 
# NOTE: With the new ATACseq protocol - OMNI-ATAC, 
# mitochondrial contamination should no longer be a problem
#------------------------------------------------------------

samtools view -h -@ 15 <aligned_filtered.bam> | grep -v "chrM" | samtools view -h -b -@ 15 - > <aligned_filt_noMT.bam>
```

## Pseudocode for duplicate removal

A typical problem in libraries sequenced using a PCR amplification is the presence of PCA duplicates among the reads. 
These should be filtered out, as they do not bring any additional insights. Usually, these duplicates can be identifiied when many reads have the exact same coordinates. We will delete these duplicates from the bam file using the `samtools markdup` tool.

```
#---------------------------------------
# Sorting, Fixmate and Duplicate removal
#---------------------------------------

# NOTE: fixmate needs bam files sorted by read name but
# the downstream processes need co-ordinate sorted bam files, hence the two sorting steps
# Fixmate is needed to add mate tags (-m), which is used for proper duplicate identification by markdup

samtools sort -n -O BAM -@ 10 <aligned_filt_noMT.bam>  \
| samtools fixmate -m -@ 10 - - \
| samtools sort -O BAM -@ 10 - > \
| samtools markdup -r -S -@ 10 - <aligned_filtered_sorted_duprmv.bam>
```

## Pseudocode for duplicate removal

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
mkdir -p analysis/AlignmentStats

# Flagstat analysis

# Pseudocode: 
# samtools flagstat <aligned.bam> > <aligned.bam.log>
# samtools flagstat <aligned_filtered_sorted_duprmv.bam> > <aligned_filtered_sorted_duprmv.bam.log> 

samtools flagstat data/processed/ATAC/Bowtie2/ATAC/ATAC_REP1_aligned_nofilt.bam > \
analysis/AlignmentStats/ATAC_REP1_aligned_nofilt.bam.log

```

 > Can you modify the code above to run flagstat also on the corresponding **filtered and unfiltered** `.bam` and compare the numbers from the two files. What do you learn ?

In the next section, we will perform **peak calling** using these aligned `.bam` files.
