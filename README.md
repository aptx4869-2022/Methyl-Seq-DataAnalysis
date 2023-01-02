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
__Cut bases from the 3' end until__ the *QC score* is higher than the threshold you set (default 20). So if there are low quality bases in the middle of the sequence, it won't be cut out.   
  
Usually low quality bases are also interpreted as `"N"`, so can be removed (note, filter-remove, not cut-remove) when N is removed (will be discussed later).  

It is still be concerned if there are regions of low quality bases not being removed only because they are in the middle of the sequence, instead of the end. `Per sequence quality scores` section in the fastqc result can be used to present this (if there a large region of low quality bases in the middle of the sequence). If the curve start to taking off after 26, then that's fine.  *inset an example picture*  
  
Of course it can be trimed from the 5' end, or both ends. Just use proper parameters.  
  
__About the `nextseq-trim` parameter:__ ignore `G` base when trimming low qualities at the 3' end, no matter what the actual quality score the `G` has. This is because Illumia machine is not able to distingush `G` and `nothing`. *link*    

```
Note the order of the cutadapter operation: 

1. cut: 
    cut certain length
    quality trim
    adapter trim
    cut to certain length
    (rename stuff, not quite understand)
2. filter
    filter based on length
    filter based on max allowed N numbers
    (filter based on max-expected-errors, not fully understand, but might solve the problem about low quality region in the middle of sequences)
    output options (trimmed, untrimmed)
```

#### 2.2 Parameters related to quality trim：  
Filter based on the max N allowed: 3 is usually a good number. 

### 3. AdapterTrim with cutadapt
```bash
cutadapt -a AGATYGGAAGAGCACAYGTCTGAACTCCAGTCA -A  AGATCRGAAGAGCRTCRTGTAGGGAAAGAGTGT -o CAoutS10-1.fastq.gz -p CAoutS10-2.fastq.gz DMR-10_S10_R1_001.fastq DMR-10_S10_R2_001.fastq.gz
```
#### 3.1 Adapter sequences
__Need to understand:__  
1. the entire procedure (especially connections from step to step) of __Illumina library prep (adapter ligation) and sequencing;__   
2. difference of different Illumina library prep kits.

```
Qian's Methyl-Seq Exp： *used the TruSeq DNA HT kit*
Read1 Adapter to Trim: AGATCGGAAGAGCACACGTCTGAACTCCAGTCA
Read2 Adapter to Trim(used Truseq Dual Index): AGATCGGAAGAGCGTCGTGTAGGGAAAGAGTGT
```

#### 3.2 Pair-end trim  
#### 3.3 Methylation's effects  
Methylation's effects on Adapter Triming: adapter might contain converted nucleic acid. 

Other trim tools；
fastp： https://github.com/OpenGene/fastp  
Written with C++, so fast processing; 
More functions, including several useful and valuable functions. 


## II. Mapping/Alignment
## III. Methylation Calling
## IV. Differential Analysis
