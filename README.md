# Differential_Gene_Expression_and_Splicing Repository
*Background*  
Differential gene expression and differential gene splicing are characterized by contrasting three age groups (neonate, maturing, and adult), three tissues (liver, brain, muscle), and two sexes.
Analyses include overlap between differentially expressed and differentially spliced genes, tissue-specificity of expression of age-/sex-biased genes, and population-level genetic diversity.

*Index*
- Differential Gene Expression Preparation
  1. Quality Control (FastQC, TrimGalore)
  2. Alignment (SubJunc)
  3. Read Counting (featureCounts)


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



