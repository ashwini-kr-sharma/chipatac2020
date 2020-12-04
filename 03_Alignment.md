# Read alignment

Aligning the sequenced reads to the refrence genome is the most crucial task of any NGS analysis. Fast aligners like [Bowtie2](http://bowtie-bio.sourceforge.net/bowtie2/manual.shtml#using-samtoolsbcftools-downstream) and [BWA-MEM](https://github.com/bwa-mem2/bwa-mem2) are widely used for aligning, among others, ChIPSeq and ATACseq data. We will exemplarily show how to align reads using **Bowtie2**

Due to the long computation time and memory constraints, we have precomputed for you all the alignments using **Bowtie2**. You find these data in `/vol/volume/HCT116/analysis/<...>/Bowtie2`

## Pseudocode for alignment

```
# Read alignment with Bowtie2 - Single end

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

# Read alignment with Bowtie2 - Paired end

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

# Read filtering and sorting with samtools

samtools view -h -b -F 1796 -q 30 -@ 10 <aligned.bam> ## For Paired end add SAM Flags -F 1804 -f 2
| samtools sort -n -O BAM -@ 10 - > \
| samtools fixmate -m -@ 10 - - \
| samtools sort -O BAM -@ 10 - > \
| samtools markdup -r -S -@ 10 - \
<aligned_filtered_sorted.bam>

# Indexing

samtools index <aligned_filtered_sorted.bam> <aligned_filtered_sorted.bam.bai>
```

A detailed explaination of the parameters used for alignment can be found [here](http://bowtie-bio.sourceforge.net/bowtie2/manual.shtml#using-samtoolsbcftools-downstream), it is important to notice the difference for paired end and single end sequencing.

After alignment, we also filter out poor quality, unmapped and duplicate reads using [samtools](http://www.htslib.org/doc/samtools.html). The parameters `-F, -f, q` along with `markdup` are used to filter out reads. Using this [tool](https://broadinstitute.github.io/picard/explain-flags.html), one can find the exact meaning of the filtering flags like `1796, 1804, 2, etc`.

# Alignment statistics

**SAMtools** provides some functions like `flagstats, stats and idxstats` to compute overall summary of read alignment statistics. We will use them to compute alignment statistics and compare -

- pre-filtering and post-filtering numbers
- observe how the statistics are computed for paired end and single end data

```
# Go to your home directory
cd ~/

# Create a folder for your analysis
mkdir -p myanalysis/AlignmentStats

# Docker image for SAMtools
docker pull biocontainers/samtools:v1.9-4-deb_cv1

# Flagstat analysis

# Pseudocode: 
# samtools flagstat <aligned.bam> > <aligned.bam.log>
# samtools flagstat <aligned_filtered_sorted.bam> > <aligned_filtered_sorted.bam.log> 

##-- NOTE: change /home/sharma/ below to your home directory --##

docker run \
-v /vol/volume/HCT116/:/data/ \
-v /home/sharma/myanalysis/:/data/results \
biocontainers/fastqc:v0.11.9_cv7 \
samtools flagstat /data/analysis/xxx.bam /data/results/AlignmentStats/xxx.log

```
