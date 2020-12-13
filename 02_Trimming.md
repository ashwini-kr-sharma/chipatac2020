# 3. ChIP-seq : Read Trimming

Adapters are synthetic sequences attached to the biological read fragments during library preparation. These adapters contain crucial barcoding information, forward/reverse primers and sequences needed to bind to flowcell during the bridge amplification step of Illumina sequencing. In  short read sequencing, if the DNA insert/fragment (biological sequence of interest) is shorter than the number of bases sequenced (cycles), the machine will also sequence into the 3' adapter. Thus, it is considered good practice to remove these artificial reads prior to alignment. Also, note that there is a [discussion](https://www.ecseq.com/support/ngs/trimming-adapter-sequences-is-it-necessary) going on in the bioinformatics community in which situations is adapter trimming actually required !!

There are many well established tools for adapter trimming like [Trimmomatic](http://www.usadellab.org/cms/?page=trimmomatic), [Cudadapt](https://cutadapt.readthedocs.io/en/stable/) and [Trim Galore!](https://github.com/FelixKrueger/TrimGalore), we will use **TrimGalore!** which can automatically detect contaminating adapters in single end and paired end data.

# Trimming

Lets try Trimming only on those `fastq` files that showed significant adaptor contamination and/or overrepresented sequences. If your have carefully analyzed the **FastQC** reports from the previous section you will note that only the **CTCF ChIPseq** samples require trimming.

```
# Go to your home directory
cd 

# Create a folder for your analysis
mkdir -p analysis/Trimming

# Check out all the available parameters in trim_galore
# Do note, when in doubt, its often good practice to use default settings
# Most options are optional and set to default, focus on the essential parameters that has to be changed

trim_galore --help

# Run TrimGalore!

# Pseudocode: Single end
# trim_galore --phred64<or phred33> --fastqc --cores 8  --output_dir <output directory> <input fastq>

trim_galore \
  --phred64 \
  --fastqc \
  --cores 8 \
 --output_dir analysis/Trimming \
 /vol/volume/HCT116/ChIPseq/CTCF_Rep2_ENCFF001HLW.fastq.gz

```

Try running trimming for another **CTCF** `fastq` files. Keep in mind to change the **Trim Galore!** parameters according to your knowledge of -

- Read encodings
- Paired end/ Single end reads

> Compare the **FastQC** results from the trimmed and untrimmed analysis. Can you spot the differences in numbers and QC reports ?

In the next section, we will perform **read alignment** to the reference genome.
