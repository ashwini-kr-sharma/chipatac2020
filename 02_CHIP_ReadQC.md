# 2. ChIP-seq : Raw read quality control - FastQC

## `fastq` file format
Raw sequencing reads are stored in text files containing the sequence of nucleotides and its associated quality scores. These are called `fastq` text files and are usually in compressed formats like `XYZ.fastq.gz`. Each read is described by **4 lines**, let's look at the first four lines of an example `fastq` file -

```
cd

zcat data/fastqdata/ChIPseq/H3K4me3/H3K4me3_Rep1_ENCFF001FIS.fastq.gz | head -n 4

@SOLEXA-1GA-2_0051_FC62478:3:1:1371:1211#0/1
ACAATAATAGGTTAGGTGGATTCCCAGGNNNNNNNN
+SOLEXA-1GA-2_0051_FC62478:3:1:1371:1211#0/1
afaagggg_ddffcfffc_cfffffBBBBBBBBBBB

```

- Line 1 beginning with a `@` character represents a sequence identifier. In this identifier, each value separated by a `:` represents an information about the read. Depending on the sequencing platform, this may vary. You can find more detailed information [here](https://en.wikipedia.org/wiki/FASTQ_format).
- Line 2 consists of the actual sequence reads. 
- Line 3 beginning with a `+` character can have the same information as Line 1, be empty or may have some additional description of the reads.
- Line 4 encodes the quality values for the nucleotides in Line 2. It, thus will have same number of letter as Line 2.

> Do you know the difference between `fast[q]` and `fast[a]` file formats ? see [`here`](https://en.wikipedia.org/wiki/FASTA_format)

## Read quality encoding

### phred 33 and phred 64

**Phred 33** and **Phred 64** are two [encoding schemes](https://www.drive5.com/usearch/manual/quality_score.html), the former is commonly used by Illumina based sequencers and seen in most mordern sequencing while the latter was used in older Illumina sequenced reads. In both, quality values range from 0-40, however, they are represented by entirely different symbols in the `fastq` files. For instance, a quality score of 0 is represented by `!` in Phred 33 while its represented as `@` in Phred 64 encoding.

```
Quality encoding: !"#$%&'()*+,-./0123456789:;<=>?@ABCDEFGHIJKLMNOPQRSTUVWXYZ[\]^_`abcdefghijklmnopqrstuvwxyz{|}~
                  |                         |    |        |                              |                     |
                 33                        59   64       73                            104                   126
Phred 33:         0........................26...31.......40..                                
Phred 64:                                        ...3.....9..............................41. 
                                 
```


### Quality scores

A quality score represents the probability that a called nucleotide in a sequence in incorrect. This is represented mathematically as -

```
Q = -10 x log10(p), where p represents the probability of incorrect call

For,

p=1%,     Q=0 
p=0.1%,   Q=10
p=0.01%,  Q=20 
p=0.001%, Q=30

```

> Looking at the quality scores (4th line of the fastq), can you guess what quality encoding (Phred 64 or Phred 33) was used?

<details>
  <summary>[Click to show answer!]</summary>
  
  The read quality contains the following characters:
  ```
  afaagggg_ddffcfffc_cfffffBBBBBBBBBBB
  ```
  Looking at the encoding table above, this can only correspond to Phred 64, as the characters `a,b,c,...` do not occur in the Phred 33 encoding! In Phred 64, the have a=33, b=34, ...
</details>

<br></br>

# Quality control

We will use the [FastQC](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/) tool to perform basis quality assessment of the raw reads. This tools give us the following information -

- [Basic Statistics](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/Help/3%20Analysis%20Modules/1%20Basic%20Statistics.html)
- [Per base sequence quality](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/Help/3%20Analysis%20Modules/2%20Per%20Base%20Sequence%20Quality.html)
- [Per tile sequence quality](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/Help/3%20Analysis%20Modules/12%20Per%20Tile%20Sequence%20Quality.html)
- [Per sequence quality scores](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/Help/3%20Analysis%20Modules/3%20Per%20Sequence%20Quality%20Scores.html)
- [Per base sequence content](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/Help/3%20Analysis%20Modules/4%20Per%20Base%20Sequence%20Content.html)
- [Per sequence GC content](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/Help/3%20Analysis%20Modules/5%20Per%20Sequence%20GC%20Content.html)
- [Per base N content](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/Help/3%20Analysis%20Modules/6%20Per%20Base%20N%20Content.html)
- [Sequence Length Distribution](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/Help/3%20Analysis%20Modules/7%20Sequence%20Length%20Distribution.html)
- [Sequence Duplication Levels](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/Help/3%20Analysis%20Modules/8%20Duplicate%20Sequences.html)
- [Overrepresented sequences](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/Help/3%20Analysis%20Modules/9%20Overrepresented%20Sequences.html)
- [Adapter Content](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/Help/3%20Analysis%20Modules/10%20Adapter%20Content.html)

Based on these parameters one could estimate the sequencing quality and identify major problems right at the beginning of a project.

## FastQC

Let us perform a simple FastQC analysis on the `fastq` file that we viewed above and its control.

```
# Go to your home directory
cd 

# Create a folder for your analysis
mkdir -p analysis/FastQC/ChIP

# Check out all the available parameters in FastQC
# Do note, when in doubt, its often good practice to use default settings
# Most options are optional and set to default, focus on the essential parameters that has to be changed

fastqc --help

# FastQC analysis for two fastq files
# Pseudocode: 
# fastqc --outdir <name of output directory> <space separated list of fastq files>

# Actual analysis:
fastqc --outdir analysis/FastQC/ChIP \
data/fastqdata/ChIPseq/H3K4me3/H3K4me3_Rep1_ENCFF001FIS.fastq.gz

# Find your results here
cd analysis/FastQC/ChIP
```

## Analyzing the output

Using Cyberduck, open the folder `analysis/FastQC/ChIP`;  open the generated html files:

- Can you identify the Phred encoding? <details>
  <summary>[Click to show answer!]</summary>
  
  The encoding is Illumina 1.5, which corresponds to the Phred 64 encoding (see [here](https://en.wikipedia.org/wiki/FASTQ_format#Encoding))
</details>

- How does the base quality look like?<details>
  <summary>[Click to show answer!]</summary>
  
  Sequencing quality looks very good, with an average quality around 38.
</details>

- Are there any adapter contaminations?<details>
  <summary>[Click to show answer!]</summary>
  
  No sequencing adapters were found; however, we have contamination due to the Illumina PCA Primer 2 (section "Overrepresented sequences")
</details>

- Can you find any issues with the `fastq` files refer to [individual module description](#quality-control)<details>
  <summary>[Click to show answer!]</summary>
  
  Possible issues:
  * PCR primer contamination
  * some sequence bias at the 5'end of the reads (section "per base sequence content")
</details>

> Try performing this analysis on different ChIPseq `fastq` files of your choice - compare the read numbers and and other QC properties

> Refer to [`ENCODE ChIPseq good practices`](https://www.encodeproject.org/data-standards/chip-seq/)

In the next section, we will perform **adapter trimming/clipping** based on our knowledge of the FastQC results.
