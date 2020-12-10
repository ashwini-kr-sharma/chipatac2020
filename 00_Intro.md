# 1. Introduction
## Data background

In this workshop, we will analyse different genomic data generated from wild-type [HCT116](https://www.lgcstandards-atcc.org/products/all/CCL-247.aspx?geo_country=de#generalinformation) colon cancer cell-lines. These data will comprise of genome-wide chromatin accessibility measurements using **ATACseq**, histone modification of **H3K4me3** and **H3K4me1** marks and **CTCF** transcription factor binding using ChIPseq.

H3K4me3 is known to mark active promoters around their TSS, H3K4me1 are known to mark enhancer regions and the transcription factor CTCF is known to mediate enhancer-promoter interactions. Also, genomic regions of activity (active promoter or enhancers) are expected to have an open chromatin landscape to allow the binding of regulatory factors. Thus, integration of ATACseq data and ChIPseq data (H3K4me3, H3K4me1 and CTCF) from the same cell-line will give us an understanding of its regulatory landscape.

Raw data will be downloaded and re-processed from [`ENCODE`](https://www.encodeproject.org/) and the following data will be analyzed -

| Sample | Experiment | Assay                          | Replicate | Source   | 
| -------|------------|--------------------------------|-----------|----------|
| HCT116 | ChIPseq    | Histone modification - H3K4me3 | 1         | [ENCFF001FIS](https://www.encodeproject.org/experiments/ENCSR000DTQ/) |
| HCT116 | ChIPseq    | Histone modification - H3K4me3 | 2         | [ENCFF001FIZ](https://www.encodeproject.org/experiments/ENCSR000DTQ/) |
| HCT116 | ChIPseq    | ChIPseq control - H3K4me3      | 1         | [ENCFF001HME](https://www.encodeproject.org/experiments/ENCSR000DTP/) |
| HCT116 | ChIPseq    | Histone modification - H3K4me1 | 1         | [ENCFF000VCI](https://www.encodeproject.org/experiments/ENCSR000EUS/) |
| HCT116 | ChIPseq    | Histone modification - H3K4me1 | 2         | [ENCFF000VCK](https://www.encodeproject.org/experiments/ENCSR000EUS/) |
| HCT116 | ChIPseq    | ChIPseq control - H3K4me1      | 1         | [ENCFF000VCW](https://www.encodeproject.org/experiments/ENCSR000EUX/) |
| HCT116 | ChIPseq    | Transcription factor - CTCF    | 1         | [ENCFF001HLV](https://www.encodeproject.org/experiments/ENCSR000DTO/) |
| HCT116 | ChIPseq    | Transcription factor - CTCF    | 2         | [ENCFF001HLW](https://www.encodeproject.org/experiments/ENCSR000DTO/) |
| HCT116 | ChIPseq    | ChIPseq control - CTCF         | 1         | [ENCFF001HME](https://www.encodeproject.org/experiments/ENCSR000DTP/) |

## Download data
> NOTE: You don't need to download any data, all of the data has been pre-downloaded and saved in `/vol/volume/HCT116/fastqdata`. You will directly access these data during the practical training session.

### ChIPseq - H3K4me1
We will download the fastq files (2 isogenic replicates and 1 control) containing sequence reads (single end) from a H3K4me1 ChIPseq experiment done on the HCT116 cell-line from the ENCODE database.

```bash
wget https://www.encodeproject.org/files/ENCFF000VCI/@@download/ENCFF000VCI.fastq.gz \
-O H3K4me1_Rep1_ENCFF000VCI.fastq.gz ;
wget https://www.encodeproject.org/files/ENCFF000VCK/@@download/ENCFF000VCK.fastq.gz \
-O H3K4me1_Rep2_ENCFF000VCK.fastq.gz ;
wget https://www.encodeproject.org/files/ENCFF000VCW/@@download/ENCFF000VCW.fastq.gz \
-O H3K4me1_Control_ENCFF000VCW.fastq.gz
```

### ChIPseq - H3K4me3
We will download the fastq files (2 isogenic replicates and 1 control) containing sequence reads (single end) from a H3K4me3 ChIPseq experiment done on the HCT116 cell-line from the ENCODE database.

```bash
wget https://www.encodeproject.org/files/ENCFF001FIS/@@download/ENCFF001FIS.fastq.gz \
-O H3K4me3_Rep1_ENCFF001FIS.fastq.gz ;
wget https://www.encodeproject.org/files/ENCFF001FIZ/@@download/ENCFF001FIZ.fastq.gz \
-O H3K4me3_Rep2_ENCFF001FIZ.fastq.gz ;
wget https://www.encodeproject.org/files/ENCFF001HME/@@download/ENCFF001HME.fastq.gz \
-O H3K4me3_Control_ENCFF001HME.fastq.gz
```

### ChIPseq - CTCF
We will download the fastq files (2 isogenic replicates and 1 control) containing sequence reads (single end) from a CTCF ChIPseq experiment done on the HCT116 cell-line from the ENCODE database.

```bash
wget https://www.encodeproject.org/files/ENCFF001HLV/@@download/ENCFF001HLV.fastq.gz \
-O CTCF_Rep1_ENCFF001HLV.fastq.gz ;
wget https://www.encodeproject.org/files/ENCFF001HLW/@@download/ENCFF001HLW.fastq.gz \
-O CTCF_Rep2_ENCFF001HLW.fastq.gz ;
wget https://www.encodeproject.org/files/ENCFF001HME/@@download/ENCFF001HME.fastq.gz \
-O CTCF_Control_ENCFF001HME.fastq.gz
```
### hg38 genome index
We will also download the precomputed hg38 genome index reference for allignment from iGenomes

```bash
wget http://igenomes.illumina.com.s3-website-us-east-1.amazonaws.com/Homo_sapiens/NCBI/GRCh38/Homo_sapiens_NCBI_GRCh38.tar.gz
```

## Public datasets
Often one has to use publicly available datasets. These datasets are widely available through [Gene Expression Omnibus - GEO](https://www.ncbi.nlm.nih.gov/geo/) and [Array Express](https://www.ebi.ac.uk/arrayexpress/). Raw data `fastq` from human samples are usually deposited in [The database of Genotypes and Phenotypes - dbGAP](https://www.ncbi.nlm.nih.gov/gap/) and [European Genome-phenome Archive - EGA](https://www.ebi.ac.uk/ega/home) and are available under protected access. These data are usually in `SRA` format and an array of tools called [sra-tools](https://github.com/ncbi/sra-tools) are available to manipulate these formats prior to regular analysis. We will not talk about these files formats in this workshop, but we want to make the participants aware that most of the publically available raw ChIPseq and ATACseq data are in `SRA` format which needs to be converted to `fastq` format using `SRA-tools`.
