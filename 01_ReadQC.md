# Raw read quality control - FastQC

## `fastq` file format
Raw sequencing reads are stored in text files containg the sequence of nucleotides and its associated quality scores. These are called `fastq` text files and are usually in compressed formats like `XYZ.fastq.gz`. Each read is described by **4 lines**, lets look at the first four lines of an example `fastq` file -

```
zcat /vol/volume/regomics/fastqdata/ChIPseq/H3K4me3/H3K4me3_Control_ENCFF001HME.fastq.gz | head -n 4

@SOLEXA-1GA-1_0055_FC629PW:6:1:3315:1043#0/1
TANCTGTGTTTCTCTAAGCAAATCATAATNNNTTGA
+SOLEXA-1GA-1_0055_FC629PW:6:1:3315:1043#0/1
aaB^aaaaQaa^\Wcd^^dSddYcbYIV_BBBBBBB

```

- Line 1 begining with a `@` character represents a sequence identifier. In this identifier, each value separated by a `:` represents an information about the read. Depending on the sequencing platform, this may vary. You can find more detailed information [here](https://en.wikipedia.org/wiki/FASTQ_format)
- Line 2 consists of the actual sequence reads. 
- Line 3 begining with a `+` character can have the same information as Line 1, be empty or some additional description of the reads.
- Line 4 encodes the quality values for the nucleotides in Line 2. It, thus will have same number of letter as Line 2.

> Do you know the difference between `fast`**q** and `fast`**a** file formats ?

## Read quality encoding

### phred 33 and phred 64

**Phred 33** and **Phred 64** are two encoding schemes, the former is commonly used by Illumina based sequencers and seen in most mordern sequencing while the latter was used in older Illumina sequenced reads. In both, quality values range from 0-40, however, they are represented by entirely different symbols in the `fastq` files. For instance, a quality score of 0 is represented by `!` in Phred 33 while its represented as `@` in Phred 64 encoding.

```
Quality encoding: !"#$%&'()*+,-./0123456789:;<=>?@ABCDEFGHIJKLMNOPQRSTUVWXYZ[\]^_`abcdefghijklmnopqrstuvwxyz{|}~
                  |                         |    |        |                              |                     |
                 33                        59   64       73                            104                   126
Phred 33:         0........................26...31.......40                                
Phred 64:                                           3.....9..............................41 
                                 
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

> The reads displayed above are encoded using Phred 64 (Illumina 1.5+), can you estimate how good the base call are based on the map above ?
