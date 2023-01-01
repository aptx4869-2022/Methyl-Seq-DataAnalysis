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
```bash
cutadapt nextseq-trim=30 -m 20 --max-n 3
```
#### 2.1 basic principle of quality trim
__Cut bases from the 3' end until__ the *QC score* is higher than the threshold you set (default 20).

`nextseq-trim=30`

### 3. AdapterTrim with cutadapt
Methylation's effects on Adapter Triming: adapter might contain converted nucleic acid. 

## II. Mapping/Alignment
## III. Methylation Calling
## IV. Differential Analysis
