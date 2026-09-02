# Differential_Gene_Expression_and_Splicing Repository
*Background*  
Differential gene expression and differential gene splicing are characterized by contrasting three age groups (neonate, maturing, and adult), three tissues (liver, brain, muscle), and two sexes. Analyses include overlap between differentially expressed and differentially spliced genes, tissue-specificity of expression of age-/sex-biased genes, and population-level genetic diversity (RNAseq comes from one population while WGS for PopGen analysis come from another). This workflow is intended as a guide through code that has been developed to begin with raw reads and end with statistical analyses and figure generation. Note: This is not a self-sufficient repository where code can be compiled and run independently. However, I have named files such that output of step X will be consistent with the input for step Y. Paths and SLURM headers will likely need adjustment. Please contact dtnondorf@gmail.com for questions or comments.

*Index*
- Differential Gene Expression Preparation (RNAseq)
  1. Quality Control (FastQC, TrimGalore)
  2. Alignment (SubJunc)
  3. Sorting and Indexing (samtools)
  4. Read Counting (featureCounts)
 
- Differential Splicing Preparation and Calculation (RNAseq)
  1. Contrasting Variables (rMATS)

- Variant Calling (WGS)
  1. Quality Control (FastQC, TrimGalore)
  2. Alignment (bwa-mem)
  3. Sorting and Indexing (samtools)
  4. Variant Calling (GATK)
  5. Variant Filtering (GATK, bedtools, vcftools)
  6. PopGen Statistics (vcftools)
     - Nucleotide Diversity and Tajima's D

- Statistical Analyses and Figure Generation
  1. All analyses will be contained in annotated R files (RStudio)


## Differential Gene Expression Preparation
### Quality Control

First we must inspect the quality of raw reads. We use FastQC for all samples (Forward and Reverse (F/R here on) are run separately).  

Array File: Single column containing all files as rows. *FastQC_array.txt*

`sbatch FastQC_array.slurm`

This will create QC html files you can view for each file. Within this same directory we analyze all FastQC output with MultiQC.  
If per-base-sequencing quality or quality scores are flagged, we will likely need to trim. Check "overrepresented sequences" for potential adapter presence.  
*Assessing sequencing quality is user-specific and the user should seek advice on forums and in literature.

---

Next we can trim troublesome reads. In my project, the first 5 or so bps in reads were always low-quality sequence. I hard-trim after carefully assessing each file. Attached code runs on all files as a simple example, but make sure to adjust when running.  

Array File: Two columns, first column with F and second column with R. *TrimGalore_array.txt*

`sbatch TrimGalore_array.slurm`

Run FastQC again on the trimmed output to ensure reads are at the desired quality. Repeat as necessary.

---

### Alignment

I use the SubRead alignment software; however, you are encouraged to try different aligners. Due to a high level of off-target intron sequencing in my dataset I used the SubJunc aligner which accounts for splicing junctions.  
This step will require a well annotated reference fasta and gtf file.

First we need to index the reference genome fasta file. Follow steps here [SubJunc_Manual](https://subread.sourceforge.net/subjunc.html) and make sure to alter path and file names in the subsequent .slurm file.

Array File: Two columns, first column with F and second column with R. *SubJunc_align_array.txt*

`sbatch SubJunc_align_array.slurm`

---

### Sorting and Indexing

Many downstream commands require alignments to be sorted and indexed. Further, we convert large .sam files into smaller .bam files.

Array File: One column of aligned .sam files. *SortDex_array.txt*

`sbatch SortDex_array.slurm`

---

### Read Counting

Now we can get read counts for each gene (must be reads overlapping exons). Rather than an array, we will run a single job that will return a table with all aligned samples as columns.

`sbatch featureCounts.slurm`

---

