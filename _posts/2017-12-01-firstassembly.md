---
layout: post
title:  "Trimming reads and Assembly"
date:   2017-12-01 13:33:00
categories: main
---

Today we have tried to assemble the genome of *P. andina* using ABySS. In order to get started, we have used the following documentation and guides:
- [Bacterial genome assembly tutorial](https://bioinformatics.uconn.edu/bacterial-genome-assembly-tutorial/)

- [De novo assembly of Illumina reads using ABySS and align using BWA](http://sjackman.ca/abyss-activity/).

### Quality control - trimming reads
Following the "Bacterial genome assembly tutorial", one of the first steps is to perform a quality control on the raw reads. This was done using a trimming tool called sickle. Sickle uses sliding windows together with length threshold and quality (which is included in the FASTQ-files) to determine when the quality of each read is low enough to trim the 3'-end of the reads, as well as trimming the 5'-ends when the quality is high enough. Sickle works for Illumina, Solexa, and Sanger quality values. More can be read here: 

[sickle - A windowed adaptive trimming tool for FASTQ files using quality](https://github.com/najoshi/sickle). 

We discussed whether we should use this tool or not, as it will completely remove reads if they have a sufficiently low quality overall. Knowing that we already do not have the best sequencing data to work with (as described in previous posts), we are not sure if this will just make the assembly even harder having less data to work with, or better in terms of removing reads that have a very low quality anyway and may lower the overall quality of the assembly. 

We decided to use both trimmed and untrimmed data and compare the difference when we have the assembly, and it was done by me as follows: 

```bash
sickle se -f SRR1817238.fastq -t illumina -o sickle-trimmed-reads.fastq
# se means single-end reads
# -f flag that designates the input file containing the forward reads
# -t designates the type of read
# -o is the output file containing trimmed forward reads
```

However, this resulted in the following error: 

```bash
ERROR: Quality value (63) does not fall within correct range for Illumina encoding.
Range for Illumina encoding: 64-110
FastQ record: SRR1817238.1
Quality string: ?:=DB:BDH?DFHEBEHIDHC?FHDGGIIII>F;?FHGBHG9BFHHIDHCEHACC77CEHEA==BDC?8?:>CCACA::C??>:A>CCA@:#+#++#+2<@<CC:9@B8@C@C>@@(4>>CACCCB>B2<?@@CCCCC>@>9AC3:4<AC>
Quality char: '?'
Quality position: 1
```

After reading in forums where people have had similar problems, the conclusion was that Illumina uses Sanger quality encoding and not Illumina quality encoding (at least some versions). Therefore I changed the quality encoding to Sanger instead, as follows: 

```bash
sickle se -f SRR1817238.fastq -t sanger -o sickle-trimmed-reads.fastq
```

And we got the following results (remembering we have 44445500 reads in total):

```bash
FastQ records kept: 44363188
FastQ records discarded: 82312
```

So the new fastq-file contains trimmed reads and very low-quality reads have been removed. However, I have not been able to assess whether removing 82312 reads is good or bad, or too much or too little, or perhaps just normal and expected. Considering it is less than 0.2% of the total number of reads, I am thinkinh it might not have a very large effect on the actual assembly (hopefully just a positive effect in improving the accuracy of the assembly).

### Assembly using ABySS  

For the assembly, we continued following the instructions and added the needed modules. I added the abyss/2.0.2-k128 module which according to UppMax's list of modules seems to be the latest one. Panos and Maria added abyss/2.0.2, the second latest ABySS-module. We decided to run ABySS with three different k-mer sizes; 15, 30 and 48. I used a k-mer size of 48 and used both untrimmed and trimmed reads. 

A script was made to make it easier and the following line was used to run ABySS for the untrimmed, raw reads: 
```bash
abyss-se k=48 name=p-andina-assembly in='SRR1817238.fastq'
```
and the following for trimmed reads using sickle: 

```bash
abyss-se k=48 name=p-andina-assembly in='sickle-trimmed-reads.fastq' 
```
Where k was set to 15 or 30 in the scripts run by Maria and Panos, respectively. However, we all noticed that we got an output almost immediately and investigated why: 

```bash
abyss-se command not found
```

The conlusion was that -se was not the way to use single-reads (in the case of paired end you would use abyss-pe). Therefor, both Maria and Panos ran the script with -pe instead and the job was submitted. 

At the time of writing, the jobs submitted by Maria and Panos have stopped and we have some of the output-files that we expect, but we also miss some important ones, for example .stats files with quality information about the assembly. The conclusion is that we somehow need to specify that we have single-reads. 
The following scripts have been submitted as two separate jobs and results are expected tomorrow: 

```bash
#!/bin/sh

cd /home/ellinor/Project

# modules needed
module add bioinfo-tools
module add abyss/2.0.2-k128

abyss-pe k=48 se="SRR1817238.fastq" name=p-andina-assembly-untrimmed "unitigs1"
```

```bash 
#!/bin/sh

cd /home/ellinor/Project

# modules needed
module add bioinfo-tools
module add abyss/2.0.2-k128

abyss-pe k=48 se="sickle-trimmed-reads.fastq" name=p-andina-assembly-sickle-trimmed "unitigs2"
```

I will update tomorrow when (if) I get the results. 

In the mean time, Maria will look into how we can BLAST the CHS-gene file from the folder in which it is deposited (as we can not download it and BLAST it using a browser). This is because this part of the project is independent on the assembly part for now, and even if the assembly part does not work, we can try and get results in other parts of the project. 

