# Read Trimming

Adapters are synthetic sequences attached to the biological read fragments during library preparation. These adapters contain crucial barcoding information, forward/reverse primers and sequences needed to bind to flowcell during the bridge amplification step of Illumina sequencing. In  short read sequencing, if the DNA insert/fragment (biological sequence of interest) is shorter than the number of bases sequenced (cycles), the machine will also sequence into the 3' adapter. Thus, it is considered good practice to remove these artificial reads prior to alignment. Also, note that there is a [discussion](https://www.ecseq.com/support/ngs/trimming-adapter-sequences-is-it-necessary) going on in the bioinformatics community in which situations is adapter trimming actually required !!

There are many well established tools for adapter trimming like [Trimmomatic](http://www.usadellab.org/cms/?page=trimmomatic), [Cudadapt](https://cutadapt.readthedocs.io/en/stable/) and [Trim Galore!](https://github.com/FelixKrueger/TrimGalore), we will use **TrimGalore!** which can automatically detect contaminating adapters in single end and paired end data.

# Trimming

Lets try Trimming only on those `fastq` files that showed significant adaptor contamination and/or overrepresented sequences. If your have carefully analyzed the **FastQC** reports from the previous section you will note that only the **CTCF ChIPseq** and **ATACseq** samples require trimming.

```
# Go to your home directory
cd ~/

# Create a folder for your analysis
mkdir -p myanalysis/Trimming

# Docker image for Trim Galore!
docker pull bschiffthaler/trim-galore:latest

# Run TrimGalore!

# Pseudocode: Single end
# trim_galore --phred64<or phred33> --fastqc --cores 8  --output_dir <output directory> <input fastq>
        
# Pseudocode: Paired end 
# trim_galore --phred64 --fastqc --cores 8  --output_dir <output directory> <input A_R1.fastq.gz> <input A_R2.fastq.gz> <input B_R1.fastq.gz> <input B_R2.fastq.gz> 

##-- NOTE: change /home/sharma/ below to your home directory --##

docker run \
-v /vol/volume/HCT116/:/home/ \
-v /home/sharma/myanalysis/:/home/results \
bschiffthaler/trim-galore:latest \
--phred64 \
--fastqc \
--cores 8 \
--output_dir /home/results/Trimming \
/home/fastqdata/ChIPseq/ChIPseq/CTCF/CTCF_Rep2_ENCFF001HLW.fastq.gz

```

Now you can run the trimming for all **CTCF** and **ATACseq** `fastq` files. Keep in find to use you knowledge of -

- Read encoding
- Paired end/ Single end reads

to change the **Trim Galore!** parameters accordingly.

Next we will perform **read alignment** to the reference genome.
