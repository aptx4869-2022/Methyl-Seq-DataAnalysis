# Methyl-Seq-DataAnalysis

__Basic facts/parameters:__ *2016-2017, Upstate Molecular Biology Core*  
__Library Prep:__  
*TruSeq DNA LT kit  
SureSelect DNA Methylation kit*  
__Sequencing:__  
*Illumina NextSeq*  

## I. QC
### 1. Review the seq data (.fastq) with fastqc  
https://github.com/s-andrews/FastQC

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
__Zymo Bisulfite Gold Kit:__  Primer design for PCR amplification after bisulfite conversion(BC)   
__Agilent Targetted Bisulfite Sequencing:__ Adapter ligation before BC  
__Agilent PBAT:__ Adapter ligation after BC  
__Zymo RRBS:__  Adapter ligation before BC  
__Zymo WGBS:__  Adapter ligation after BC


### 4. Other trim tools
#### 4.1 fastp： https://github.com/OpenGene/fastp  
Written with C++, so fast processing;   
More functions, including several useful and valuable functions.   

#### 4.2 trim-galore https://github.com/FelixKrueger/TrimGalore/blob/master/Docs/Trim_Galore_User_Guide.md  
Cutadapt + Fastqc + Mspl digested-RRBS  
__Mspl digested-RRBS:__ Mspl-digested Reduced Representation Bisulfite Sequencing
__RRBS Guide:__ contains contents about OT,OB, CTOT, CTOB 


## II. Mapping/Alignment
SJTU HPC Bismark https://docs.hpc.sjtu.edu.cn/app/bioinformatics/bismark.html  
Bismark https://github.com/FelixKrueger/Bismark  
Bismark User Guide https://github.com/FelixKrueger/Bismark/tree/master/Docs  
Bowtie2 https://github.com/BenLangmead/bowtie2

(IX) Notes about diff lib types and commercial kits
RRBS
PBAT
Single Cell BS-Seq (scBS-Seq)
TruSeq DNA methylation kit (formerly Epigenome)
Zymo Pico kit
Swift
NEB EM-Seq

```bismark_genome_preparation ```
```bismark```
```bismark_methylation_extraction```

### 1. Bismark genome preparation
Download reference genome
Prepared reference genome (Output files)  

### 2. Bismark
Paremeters related
__Visualization:__ SeqMonk 
https://github.com/s-andrews/seqmonk/issues/  
https://www.bioinformatics.babraham.ac.uk/projects/seqmonk/  
SeqMonk Linux install had the error message:
```
CLASSPATH is : /home/data/t180315/ProjAna/Rat-16-ETOH-MethylSeq-2017/Visualization/SeqMonk:/home/data/t180315/ProjAna/Rat-16-ETOH-MethylSeq-2017/Visualization/SeqMonk/htsjdk.jar:/home/data/t180315/ProjAna/Rat-16-ETOH-MethylSeq-2017/Visualization/SeqMonk/Jama-1.0.2.jar:/home/data/t180315/ProjAna/Rat-16-ETOH-MethylSeq-2017/Visualization/SeqMonk/commons-math3-3.5.jar
Java interpreter is '/home/data/t180315/ProjAna/Rat-16-ETOH-MethylSeq-2017/Visualization/SeqMonk/jre/bin/java'
openjdk version "13.0.2" 2020-01-14
OpenJDK Runtime Environment AdoptOpenJDK (build 13.0.2+8)
OpenJDK 64-Bit Server VM AdoptOpenJDK (build 13.0.2+8, mixed mode, sharing)

Prefs file is at: /home/data/t180315/seqmonk_prefs.txt
Memory ceiling is 10240
Raw physical memory is 1031876
Correcting for VM actual requested allocation for 10240 is 10240
Command is: /home/data/t180315/ProjAna/Rat-16-ETOH-MethylSeq-2017/Visualization/SeqMonk/jre/bin/java -Xss4m -Xmx10240m -Dawt.useSystemAAFontSettings=on -Dswing.aatext=true uk.ac.babraham.SeqMonk.SeqMonkApplication 

Exception: java.lang.NoClassDefFoundError thrown from the UncaughtExceptionHandler in thread "main"
```
See solutions: https://github.com/s-andrews/SeqMonk/issues/241, especially the solution is https://docs.alliancecan.ca/wiki/Installing_software_in_your_home_directory#Installing_binary_packages
```
I obtained feedback from our cluster tech support team and was pointed to a solution that worked: to patch the bundled java (.../SeqMonk/jre/lib/) with the paths to the libraries. It is described here, though I must admit I don't fully understand the process:
https://docs.computecanada.ca/wiki/Installing_software_in_your_home_directory#Installing_binary_packages

But it works. In the end, the solution was not to change anything with the SeqMonk release but instead to tell it where to find the library.
```

### 3. (Optional) Deduplication

### 4. Bismark methylation extraction
bedGraph format
https://blog.csdn.net/u011262253/article/details/109369089

## III. Methylation Calling
Plotly: Graphic Ploting https://plotly.com/javascript/

## IV. Differential Analysis
