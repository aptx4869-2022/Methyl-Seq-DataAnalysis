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
See solutions: https://github.com/s-andrews/SeqMonk/issues/241, especially the solution is a patch  
```
I obtained feedback from our cluster tech support team and was pointed to a solution that worked: to patch the bundled java (.../SeqMonk/jre/lib/) with the paths to the libraries. It is described here, though I must admit I don't fully understand the process:
https://docs.computecanada.ca/wiki/Installing_software_in_your_home_directory#Installing_binary_packages

But it works. In the end, the solution was not to change anything with the SeqMonk release but instead to tell it where to find the library.
```
https://docs.alliancecan.ca/wiki/Installing_software_in_your_home_directory#Installing_binary_packages  
the wiki paragraph:
```
If you install pre-compiled binaries in your home directory (for example Miniconda) they may fail using errors such as /lib64/libc.so.6: version `GLIBC_2.18' not found. Often such binaries can be patched using our setrpaths.sh script, using the syntax setrpaths.sh --path path [--add_origin] where path refers to the directory where you installed that software. This script will make sure that the binaries use the correct interpreter, and search for the libraries they are dynamically linked to in the correct folder. The option --add_origin will also add $ORIGIN to the RUNPATH. This is sometimes helpful if the library cannot find other libraries in the same folder as itself.

Note:

Some archive file, such as java (.jar files) or python wheels (.whl files) may contain shared objects that need to be patched. The setrpaths.sh script extracts and patches these objects and updates the archive.
```

However I don't have access to the `setrpath.sh` file nor ComputeCanada's service, so I am trying to fix the issue by learning `LD_LIRBARY_PATH`.
```
Great, glad to hear you have things working! It sounds like you may have had the correct library but in a location where SeqMonk wouldn't automatically look. I guess your IT script updated the LD_LIRBARY_PATH so that it was found correctly.
```
A reference about `LD_LIRBARY_PATH` (not quite understand): https://www.cnblogs.com/kex1n/p/5993498.html#:~:text=linux%E7%8E%AF%E5%A2%83%E5%8F%98%E9%87%8FLD_LIBRARY_PATH.%20LIBRARY_PATH%E5%92%8CLD_LIBRARY_PATH%E6%98%AFLinux%E4%B8%8B%E7%9A%84%E4%B8%A4%E4%B8%AA%E7%8E%AF%E5%A2%83%E5%8F%98%E9%87%8F%EF%BC%8C%E4%BA%8C%E8%80%85%E7%9A%84%E5%90%AB%E4%B9%89%E5%92%8C%E4%BD%9C%E7%94%A8%E5%88%86%E5%88%AB%E5%A6%82%E4%B8%8B%EF%BC%9A.%20LIBRARY_PATH%E7%8E%AF%E5%A2%83%E5%8F%98%E9%87%8F%E7%94%A8%E4%BA%8E%E5%9C%A8%E7%A8%8B%E5%BA%8F%E7%BC%96%E8%AF%91%E6%9C%9F%E9%97%B4%E6%9F%A5%E6%89%BE%E5%8A%A8%E6%80%81%E9%93%BE%E6%8E%A5%E5%BA%93%E6%97%B6%E6%8C%87%E5%AE%9A%E6%9F%A5%E6%89%BE%E5%85%B1%E4%BA%AB%E5%BA%93%E7%9A%84%E8%B7%AF%E5%BE%84%EF%BC%8C%E4%BE%8B%E5%A6%82%EF%BC%8C%E6%8C%87%E5%AE%9Agcc%E7%BC%96%E8%AF%91%E9%9C%80%E8%A6%81%E7%94%A8%E5%88%B0%E7%9A%84%E5%8A%A8%E6%80%81%E9%93%BE%E6%8E%A5%E5%BA%93%E7%9A%84%E7%9B%AE%E5%BD%95%E3%80%82.,%E8%AE%BE%E7%BD%AE%E6%96%B9%E6%B3%95%E5%A6%82%E4%B8%8B%EF%BC%88%E5%85%B6%E4%B8%AD%EF%BC%8CLIBDIR1%E5%92%8CLIBDIR2%E4%B8%BA%E4%B8%A4%E4%B8%AA%E5%BA%93%E7%9B%AE%E5%BD%95%EF%BC%89%EF%BC%9A.%20export%20LIBRARY_PATH%3DLIBDIR1%3ALIBDIR2%3A%24LIBRARY_PATH.

Finally fixed by downloaded Xmanager (30 days of free-trial)

### 3. (Optional) Deduplication

### 4. Bismark methylation extraction
bedGraph format
https://blog.csdn.net/u011262253/article/details/109369089

## III. Methylation Calling
Plotly: Graphic Ploting https://plotly.com/javascript/

## IV. Differential Analysis
