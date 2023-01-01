# Methyl-Seq-DataAnalysis

__Basic facts/parameters:__ *2016-2017, Upstate Molecular Biology Core*  
__Library Prep:__  
*TruSeq DNA LT kit  
SureSelect DNA Methylation kit*  
__Sequencing:__  
*Illumina NextSeq*  

## I. QC
### 1. Review the seq data (.fastq) with fastqc
### 2. QualityTrim with cutadapt
``` cutadapt nextseq-trim=30 -m 20 --max-n 3```
### 3. AdapterTrim with cutadapt
Methylation's effects on Adapter Triming: adapter might contain converted nucleic acid. 

## II. Mapping/Alignment
## III. Methylation Calling
## IV. Differential Analysis
